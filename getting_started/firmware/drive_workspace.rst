.. _doc_drive_workspace:

3. ROS Workspace 설정하기
=====================
**필요한 장치:**
	* 완성된 F1TENTH 차량
	* Pit/Host 컴퓨터
	* 외부 monitor/display, HDMI 케이블, 키보드, 마우스

**걸리는 시간:** 1.5 시간

개요
----------
궁극적으로 차량을 운행하기 위해서 모든 연결에 ROS를 사용한다. :ref:`ROS workspace <ros_workspace>`, :ref:`udev rules <udev_rules>`와 :ref:`lidar 연결 테스트 <lidar_setup>` 을 설정한다. 여기서는 모든 것을 **Jetson NX**에서 수행하며 **Pit** 노트북에서 SSH로 연결한다.

.. _ros_workspace:

1. ROS Workspace 설정하기
---------------------------------
**Jetson NX**에 SSH나 **Pit** 노트북 아니면 모니터와 키보드로 연결가능하다.

**Jetson NX**에서 터미널 윈도우를 열어서 ROS workspace를 설정하고 아래 단계를 따라하자.

#. 다음 저장소를 clone한다.

	.. code-block:: bash

		$​ ​cd​ ~/sandbox (원하는 폴더)
		$​ git ​clone​ https://github.com/f1tenth/f1tenth_system

#.  여기서는 ``f1tenth_ws``라는 workspace 폴더를 생성한다. 그리고 ``f1tenth_system`` 폴더를 여기로 복사한다.

	.. code-block:: bash

		$​ mkdir -p f1tenth_ws/src
		$​ cp -r f1tenth_system f1tenth_ws/src/

#. 추가 ROS 패키지 설치하기

	ROS Kinetic:

		.. code-block:: bash

			$​ sudo apt-get update
			$​ sudo apt-get install ros-kinetic-driver-base

	ROS Melodic:

		.. code-block:: bash

			$​ sudo apt-get update
			$​ sudo apt-get install ros-melodic-driver-base

#. 모든 python 스크립트를 실행가능하게 설정

	.. code-block:: bash

		$​ ​cd​ f1tenth_ws
		$​ find . -name “*.py” -exec chmod +x {} \;

#. workspace 폴더로 이동하고 코드를 컴파일한다.(catkin_make은 단순히 코드를 컴파일하는 것 이상의 일을 수행한다. 온라인 문서 참고)

	.. code-block:: bash

		$​ catkin_make

#. 마지막으로 shell에서 working directory를 참조할 수 있도록 source 명령 수행한다.

	.. code-block:: bash

		$​ source devel/setup.bash

이제 완료! onboard driver workspace이 모두 설정되었다.

..
	Workspace Content Breakdown
	^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
	Examine the contents of your workspace and you will see 3 folders. In the ROS world we call these **meta-packages** since they contain package.

		* algorithms
		* simulator
		* system

	#. Algorithms contains the brains of the car which run high level algorithms, such as wall following, pure pursuit, localization.
	#. Simulator contains racecar-simulator which is based off of MIT Racecar’s repository and includes some new worlds such as Levine 2nd floor loop. Simulator also contains f1_10_sim which contains some message types useful for passing drive parameters data from the algorithm nodes to the VESC nodes that drive the car.
	#. System contains code from MIT Racecar that the car would not be able to work without. For instance, System contains ackermann_msgs (for Ackermann steering), racecar (which contains parameters for max speed, sensor IP addresses, and teleoperation), serial (for USB serial communication with VESC), and vesc (written by MIT for VESC to work with the racecar).

	We will be focusing on the **System** folder in this section. :ref:`Going Forward <doc_going_forward_intro>` will utilize the firsit two folders - **Algorithms** and **Simulator**.

.. _udev_rules:

2. Udev Rules Setup
----------------------
When you connect the VESC and a USB lidar to the Jetson, the operating system will assign them device names of the form ``/dev/ttyACMx``, where ``x`` is a number that depends on the order in which they were plugged in. For example, if you plug in the lidar before you plug in the VESC, the lidar will be assigned the name ``/dev/ttyACM0​``, and the VESC will be assigned ``/dev/ttyACM1​``. This is a problem, as the car’s ROS configuration scripts need to know which device names the lidar and VESC are assigned, and these can vary every time we reboot the Jetson, depending on the order in which the devices are initialized.

Fortunately, Linux has a utility named ​udev​ that allows us to assign each device a “virtual” name based on its vendor and product IDs. For example, if we plug a USB device in and its vendor ID matches the ID for Hokuyo laser scanners (15d1), ​udev​ could assign the device the name ``/dev/sensors/hokuyo`` instead of the more generic ``/dev/ttyACMx​``. This allows our configuration scripts to refer to things like ``/dev/sensors/hokuyo`` and ``/dev/sensors/vesc​``, which do not depend on the order in which the devices were initialized. We will use udev to assign persistent device names to the lidar, VESC, and joypad by creating three configuration files (“rules”) in the directory ``/etc/udev/rules.d``.

First, as root, open ``/etc/udev/rules.d/99-hokuyo.rules`` in a text editor to create a new rules file for the Hokuyo. Copy the following rule exactly as it appears below and save it:

.. code-block:: bash

	KERNEL=="ttyACM[0-9]*", ACTION=="add", ATTRS{idVendor}=="15d1", MODE="0666", GROUP="dialout", SYMLINK+="sensors/hokuyo"

Next, open ``/etc/udev/rules.d/99-vesc.rules`` and copy in the following rule for the VESC:

.. code-block:: bash

	KERNEL=="ttyACM[0-9]*", ACTION=="add", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="5740", MODE="0666", GROUP="dialout", SYMLINK+="sensors/vesc"

Then open ``/etc/udev/rules.d/99-joypad-f710.rules`` and add this rule for the joypad:

.. code-block:: bash

	KERNEL=="js[0-9]*", ACTION=="add", ATTRS{idVendor}=="046d", ATTRS{idProduct}=="c219", SYMLINK+="input/joypad-f710"

Finally, trigger (activate) the rules by running

.. code-block:: bash

	$ sudo ​udevadm control --reload-rules
	$ sudo udevadm trigger​

Reboot your system, and you should find three new devices by running

.. code-block:: bash

	$ ls /dev/sensors
	$ hokuyo​    vesc

and:

.. code-block:: bash

	$ ls /dev/input
	$ joypad-f710​

If you want to add additional devices and don’t know their vendor or product IDs, you can use the command

.. code-block:: bash

	$ sudo ​udevadm info --name=<your_device_name> --attribute-walk

making sure to replace ``<your_device_name>`` with the name of your device (e.g. ttyACM0 if that’s what the OS assigned it. The Unix utility ​dmesg​ can help you find that). The topmost entry will be the entry for your device; lower entries are for the device’s parents.

.. _lidar_setup:

3. Testing the Lidar
----------------------
This section assumes that the lidar has already been plugged in (either to the USB hub or to the Orbitty's ethernet port). If you are using the Hokuyo 10LX or a lidar that is connected via the ethernet port of the Orbitty, make sure that you have completed the :ref:`Hokuyo 10LX Ethernet Connection <doc_firmware_hokuyo10>` section before preceding.

Once you’ve set up the lidar, you can test it using ​urg_node​/hokuyo_node (replace the hokuyo_node by the urg_node if you have 10LX with Ethernet connection: https://github.com/ros-drivers/urg_node.git), ​rviz​, and ​rostopic​.

A. If you're using the 10LX:

	* Start ``roscore​`` in a terminal window.
	* In another (new) terminal window, run ``rosrun urg_node urg_node _ip_address:="192.168.0.10"​``. Make sure to supply the urg node with the correct port number for the 10LX.
	* This tells ROS to start reading from the lidar and publishing on the ​/scan​ topic. If you get an error saying that there is an “error connecting to Hokuyo,” double check that the Hokuyo is physically plugged into a USB port. You can use the terminal command ``lsusb​to`` check whether Linux successfully detected your lidar. If the node started and is publishing correctly, you should be able to use ``rostopic echo /scan​`` to see live lidar data.
	* In the racecar config folder under ``lidar_node`` set the following parameter in sensors.yaml: ``ip_address: 192.168.0.10``. In addition in the ``sensors.launch.xml`` change the argument for the lidar launch from ``hokuyo_node`` to ``urg_node`` do the same thing for the ``node_type`` parameter.

B. If you're using the 30LX:

	* Run ``roslaunch racecar teleop.launch`` in a sourced terminal window, by default, the launch file brings up the hokuyo node.

Once your lidar driver node is running, open another terminal and run ``rosrun rviz rviz​`` or simply ``rviz`` to visually see the data. When ``rviz​`` opens, click the “Add” button at the lower left corner. A dialog will pop up; from here, click the *By topic* tab, highlight the *LaserScan* topic, and click *OK*. You might have to switch from viewing in the ``\map`` frame to the ``laser`` frame. If the laser frame is not there, you can type in ``laser`` in the frame text field.

``rviz`` will now show a collection of points of the lidar data in the gray grid in the center of the screen. You might have to change the size and color of the points in the LaserScan setting to see the points clearer.

	* Try moving a flat object, such as a book, in front of the lidar and to its sides. You should see a corresponding flat line of points on the ​rviz​ grid.
	* Try picking the car up and moving it around, and note how the lidar scan data changes,

You can also see the lidar data in text form by using ​``rostopic echo /scan`` ​. The type of message published to it is sensor_msgs/LaserScan​, which you can also see by running ``rostopic info /scan​`` . There are many fields in this message type, but for our course, the most important one is ​ranges​, which is a list of distances the sensor records in order as it sweeps from its rightmost position to its leftmost position.

With all of the parts connected now, we can move on to driving with a joystick!

.. image:: img/drive01.gif
	:align: center
	:width: 200pt
