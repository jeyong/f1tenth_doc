.. _drive_manualcontrol:

Manual Control
=================
**필요한 장치:**
	* 완성된 F1TENTH 차량
	* Pit/Host 컴퓨터
	* Logitech F710 조이스틱

**걸리는 시간:** 30분 - ∞ 

개요
------------
직접 차량을 조정한다. 
Before we can get the car to drive itself, it’s a good idea to test the car to make sure it can successfully drive on the ground under human control. Controlling the car manually is also a good idea if you’ve recently re-tuned the VESC or swapped out a drivetrain component, such as the motor or gears. Doing this step early can spare you a headache debugging your code later since you will be able to rule out lower-level hardware issues if your code doesn’t work.

**TX2에 연결시 SSH 사용**

1. 차량 살펴보기
-----------------------
시작하기 전에 사고를 최소화해야 하므로 차량을 먼저 살펴보도록 한다.

#. LIPO 배터리 누수 확인
#. **Logitech Joypad**의 USB 동글을 **USB hub**에 연결하기
#. VESC 연결되었는지 확인
#. 차량과 노트북 양쪽이 wifi로 연결되었는지 확인한다. 만약 연결되지 않은 경우 :ref:`System Configuration <doc_software_setup>` 를 참고한다.
#. ``f110_system`` 저장소를 clone하고 :ref:`previous section <doc_drive_workspace>`에서 설명한 작업 디렉토리를 설정한다.
#. 여기서는 ``tmux`` 프로그램을 사용하여 하나의 SSH연결로 여러 terminal을 실행할 수 있다. GUI를 선호하면 VNC를 사용할 수 있다.

2. 차량 운전하기
----------------------
#. Open a terminal on the **Pit** laptop and SSH into the car from your computer.
#. Once you’re in, open a terminal window and run ​``$tmux`` so that you can spawn new terminal sessions over the same SSH connection.
#. In your tmux session, spawn a new window (using ``Ctrl-B`` and then ``C``) and run ​``$roscore``​ to start ROS.
#. Navigate to other free terminal using ``Ctrl-B`` and then ``P`` or ``N`` by switch to previous or next session, or using ``Ctrl-B`` and then the number of the session, navigate to your workspace that we set up before, run ``$ catkin_make`` and source the directory using ``$ source devel/setup.bash``.
#. Run ``$ roslaunch racecar teleop.launch​`` to launch the car. 
	* If you see an error like this: ``[ERROR] [1541708274.096842680]: Couldn't open joystick force feedback!`` It means that the joystick is connected. 
	* If this gives you a segmentation error and it’s caused by compiling the joy package (which you can check by running the joy node on its own), this could be because you are using the joy package from the ROS distribution (i.e., installed with apt-get). Remove that by ``sudo apt-get remove joy`` and ``catkin_make`` in your workspace, and sourcing the setup bash again. This should compile and use the joy package that’s in the repo.

#. Hold the LB button on the controller to start controlling the car. Use the left joystick to move the car forward and backward and the right joystick for steering.
	
	* If nothing happens or if the right joystick is not mapped to steering, your might joystick might be in a different mode, press the *mode* button to change the mode.
	* If nothing happens, one reason can be that the ``joy_node`` is listening for inputs on the ``js0`` port, but the operating system has assigned a different port to it, like ``js1``. Edit the ``yaml`` file which specifies which port to listen to. You can tell what file that is by reading the launch file (and following the call tree to other launch files).
	* Note that the LB button acts as a “dead man’s switch,” as releasing it will stop the car. This is for safety in case your car gets out of control.
	* You can see a mapping of all controls used by the car in ``f110_system/racecar/racecar/config/racecar-v2/joy_teleop.yaml``. For example, in the default configuration, axis 1 (left joystick’s vertical axis) is used for throttle, and axis 2 (right joystick’s horizontal axis) is used for steering. If you need to check which axis correspond to what buttons/axis on the joystick, run the joy node, when the joystick is connected, you can see the index of the button/axis that's changed.

문제해결
------------------
일반적인 에러:

* **VESC out of sync errors**: VESC가 연결되어 있는지 확인한다. 에러가 계속되면 VESC driver node가 제대로 동작하는지 확인한다. 현재 f110_system 저장소에 있는 ``vesc`` 패키지만 VESC 6+를 지원한다. 예전 VESC로 구동(FOCBox 처럼)되는 경우 대신에 `여기 있는 <https://github.com/mit-racecar/vesc>`_ 를 사용하자.
* **Serial port busy errors**: VESC가 금방 부팅되어서 잠시 후에 시도해보자.
* **SerialException errors** 30LX Hokuyo를 사용하는 경우 port 충돌이 원인일 수 있다. 
​and you’re using the 30LX Hokuyo​, the errors might be due to a port conflict: e.g., suppose that the lidar was assigned the (virtual serial bi-directional) port ``ttyACM0`` by the operating system. And suppose that the ``vesc_node`` is told the VESC is connected to port ``ttyACM0`` (as per ``vesc.yaml``). Then when the ``vesc_node`` receives joystick commands from ``joy_node`` (via ROS), it pushes them to ``ACM0`` - so these messages actually go to the lidar, and the VESC gets garbage back. So change the ``vesc.yaml`` port entry to ``ttyACM1``. (This whole discussion remains valid if you switch 0 and 1, i.e. if the OS assigned ACM1 to the lidar and your ``vesc.yaml`` lists ACM1). Note that everytime you power down and up, the OS will assign ports from scratch, which might again break your config files. So a better solution is to use udev rules, as explained in `this <firmware.html#udev-rules-setup>`_ section​. (See the source code of joy node for the default port for the joystick. You can over-ride that using a parameter in the launch file. See the joy documentation for what parameter that is).
* **urg_node related errors**: port를 확인하고(sensors.yaml에 있는 ip 주소는 10LX에서만 사용가능하고 30LX에서는 사용할 수 없다. /dev/ttyACM​n​에 대해서는 반대다)
* **razor_imu errors**: launch 파일에서 IMU entry를 삭제한다. - 이 build에서는 IMU를 사용하지 않는다.

차량 구성, 설정, 펌웨어 설치, 차량 운전까지!! `Learn <https://f1tenth.org/learn.html>`_ 로 가서 다른 labs을 시도해보자.

.. image:: img/drive02.gif
	:align: center
	:width: 300px

