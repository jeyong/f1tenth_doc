Linux/ROS Installation
============================

ROS는 Linux에서만 실행된다. Ubuntu에서 시뮬ㄹ이터를 사용하는 것을 지원한다. 만약에 ROS Melodic 설치된 것이 없으면 `<http://wiki.ros.org/melodic/Installation/Ubuntu>`_ 을 참고하자.

의존성
------------------
다음과 같은 의존성이 필요하다.:
  
  - tf2_geometry_msgs
  - ackermann_msgs
  - joy
  - map_server

다음을 이용해서 설치하기

.. code-block:: bash
  
  sudo apt-get install ros-melodic-tf2-geometry-msgs ros-melodic-ackermann-msgs ros-melodic-joy ros-melodic-map-server

``package.xml`` 파일에서 의존성 전체 목록을 확인할 수 있다.

Package
------------
simulator 패키지를 설치하기 위해서 simulator 저장소를 catkin workspace에 clone한다:

.. code-block:: bash

  cd ~/catkin_ws/src
  git clone https://github.com/f1tenth/f1tenth_simulator.git

다음으로 catkin_make로 빌드하자:

.. code-block:: bash

  cd ~/catkin_ws
  catkin_make
  source devel/setup.bash

Quick Start
---------------
simulator를 실행하기 위해서 다음을 실행:

.. code-block:: bash

  roslaunch f1tenth_simulator simulator.launch

전체 simulator에 필요한 모든 것을 launch한다.: roscore, the simulator, a preselected map, a model of the racecar, the joystick server

.. figure:: img/sim_install.png
  :align: center

  전체 simulation launched.

