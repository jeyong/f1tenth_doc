.. _doc_wall_following:


벽 따라가기(Wall Following)
================

..note:: 이 섹션은 Ubuntu 18.04와 ROS Melodic이 필요하다.

Hokuyo lidar 센서를 차량에 부착하고 가장 간단한 알고리즘 중에 하나로 벽 따라가기 알고리즘을 수행한다. 기본 아이디어는 차량은 왼쪽이나 오른쪽 벽 혹은 양쪽벽에 대한 거리를 측정하는 lidar 센서를 사용한다는 것이다. 벽으로부터 특정 거리를 유지한다. ``/launch`` 아래에 wall_following 패키지 내부에는 ``wall_following.launch`` 라는 파일을 볼 수 있다.

터미널에서 다음 명령을 실행하면 Gazebo 시뮬레이터에서 왼쪽 벽을 따라가는 로봇을 볼 수 있다.

첫번째 터미널에서 실행:

.. code-block:: bash

	$​ roscore

두번째 터미널에서 실행:

.. code-block:: bash

	$​ ​source​ devel/setup.bash
	$​ roslaunch wall_following wall_following.launch

Gazebo 시뮬레이터는 Levine Building 2nd floor의 원점에 차량을 위치시키면서 실행되는 것을 볼 수 있다. 차량은 왼쪽으로부터 0.5 m의 거리를 유지하고 반시계 방향으로 왼쪽 턴을 하면서 following한다. 코드는 어떻게 구성되어 있을까? 높은 관점에서 이 순서로 데이터가 처리된다.:
``pid_error.py -> control.py -> sim_connector.py``

wall_following 에 대한 메인 코드는 ``wall_following/scripts/pid_error.py`` 아래에 있다. 이 script에서 ``followLeft``, ``followRight``, ``followCenter`` 를 위한 method를 찾을 수 있다.

* ``Pid_error.py`` 는 laser scan ``/scan`` topic을 subscribe하고 새로운 lidar 데이터를 받을때마다 callback 함수를 호출한다.(초당 40회 정도) PID를 사용한다. 
 subscribes to the laser scan ``/scan`` topic and calls the callback function each time it gets new lidar data (around 40 times per second). It uses PID to calculate the error and adjusts the steering angle accordingly in order to try to keep its desired trajectory a certain distance away from the wall. ``Pid_error.py`` then outputs over the ``/error`` topic a message type we custom defined called pid_input which contains pid_vel and pid_error. 
 
* ``Control.py`` subscribes to the ``/error`` topic and limits the car’s turning angle to 30 degrees and slows down the car when it is making a turn, or speeds up the car when it is going straight. ``Control.py`` publishes to the ``/drive_parameters`` topic using our custom message type called drive_param which contains velocity and angle. 

* ``sim_connector.py`` subscribes to ``/drive_parameters`` and basically repackages the velocity and steering angle data into the ``AckermannDriveStamped`` message type so that it can be read in by the simulator under the ``/vesc/ackermann_cmd_mux/input/teleop`` topic.

If you run the Gazebo simulator long enough, you’ll notice that when the car reaches around ¾ of the way through the track, it encounter an opening on the left that leads to a dead-end. Because the car is programmed to just do a wall follow, it gets stuck here. How might we alleviate this problem? With hard-coded turn instructions, as described in the next section.

If you want to try running the wall following in the real world, run these instructions. Depending on how wide the hallway is that the car is driving in, you may want to modify the parameters for ``LEFT_DISTANCE`` and ``RIGHT_DISTANCE`` at the top of the ``pid_error.py`` file.

In one terminal run:

.. code-block:: bash

	$​ roscore

In a second terminal, run:

.. code-block:: bash

	$​ ​source​ devel/setup.bash
	$​ roslaunch real_world_wall_following wall_following.launch

Wall Following with Explicit Instructions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If we do a simple wall follow (left, right, or center), the robot will always make turns at openings it sees. But sometimes we may not want the robot to turn into an opening because it dead ends, or we just want it to keep going down the hallway. The idea in this section is to give the robot a sequence of turn instructions, which it calls sequentially each time it sees an opening. For instance, imagine in the Levine World telling the robot to turn [“left”, “left”, “left”, “center”, “left”]. The “center” would allow it to skip the dead end opening and continue straight with a center wall follow instead. Explicit instructions also includes velocity instructions.

To run the hard-coded turn instructions code in the simulator, do the following in your terminal.

In one terminal run:

.. code-block:: bash

	$​ roscore

In a second terminal, run:

.. code-block:: bash

	$​ ​ source​ devel/setup.bash
	$​ roslaunch real_world_wall_following_explicit_instructions.Launch

To change the instructions, navigate to the ``explicit_instructions/instructions.csv`` file and change the values. You will see something that looks like this:

.. code-block:: bash

	left, 1.5
	left, 2.0
	left, 1.0
	center, 0.5
	left, 2.0
	center, 1.5
	stop, 0.0

The first value is the turn instruction and the second value is the velocity which gets executed after making that turn for some duration of time specified in the ``pid_error_explicit_instructions.py`` file.

The core logic is contained in the file ``wall_following/scripts/pid_error_explicit_instructions.py``. There are a lot of comments in the code that describe the algorithm. At a high level, the car is constantly scanning for an opening by subscribing to the laser scan data. If the car detects an opening, then it takes the next instruction off of the turn instruction array and commits to that turn instruction for a specified number of seconds. The reason we commit for some seconds is that we don’t want the car to mistakenly think it sees a “new” opening midway through a turn, and prematurely call the next turn instruction.

How does the robot detect an opening? The robot scans to the right (and left as well) between some window of degrees. It compares lidar scans sequentially (so for instance, 0 degrees vs 0.25 degrees) and checks if the distance measured to 0 degrees and the distance measured to 0.25 degrees has a difference of some distance in meters. If there is a dropoff distance, then we know there is an opening.

A challenge we ran into is reflections off of metal plates on the doors in Levine Building. The robot calculated these as openings because Lidar data showed the points reflecting off the metal to be 60 meters away! Our solution was to ignore points that were further than 40 meters away because we know that they are metal.

You will also notice that in the ``real_world_wall_following_explicit_planning.launch`` file, we call a ``dead_mans_switch.py`` node. This allows us to use the joystick and the car only moves when the top right dead mans switch bumper is held down. This is for safety reasons.

If you notice your car is oscillating a lot on straightaways, try turning the kp value down in ``control.py``.

Wall following with hard coded turns is a tedious algorithm because it requires us to manually predict where the car will detect openings before we launch the algorithm. Sometimes the car detects openings unpredictably, such as when it passes by an office with glass walls or when it goes down the ramp from Levine 3rd floor into Towne. This causes the car to prematurely take the next instruction set, which then interferes with the rest of the instruction sets. Hence we move on to localization and mapping next in search of a better solution to autonomous driving that doesn’t require as much human input and is more robust.

The car can do parallel parking (kind of). But it needs a lot of parameter tuning in terms of the min_obstacle_dist, the weight_kinetmatic_forward_drive, etc.

All in all, TEB is just really cool!
