.. _doc_gap_finding:


LiDAR 스캔을 통한 Gap 찾기
======================================

..note:: Ubunu 18.04/ROS Melodic이 실행되는 컴퓨터가 필요하다.

lidar 정보 메시지 topic은 ``/scan`` 이다. :ref:`simulator <doc_going_forward_simulation>` 와 ``$rostopic info /scan​`` 을 실행하면 ``std_msgs/LaserScan`` 타입의 메시지를 볼 수 있다.

Scan Matching Odometry 
------------------------------------------------------
ROS’ ​laser_scan_matcher​ 패키지가 scan matching odometry를 수행한다.

패키지 설치하기
------------------------
터미널 열어서 필요한 패키지를 설치하기 위해서 아래 명령을 수행한다.

.. code-block:: bash

	$ sudo apt-get install ros-kinetic-amcl
	$ sudo apt-get install ros-kinetic-scan-tools

예제 Launch 파일 가져오기
---------------------------------------
f110 저장소는 미리 저장된(pre-recorded) bag 데이터에서 laser_scan_matcher를 실행되는 것을 보여주기 위한 launch 파일을 포함하고 있다. 여러분의 workspace의 ``src/`` 폴더에 이것을 복사한다.

.. code-block:: bash

	$​ cp -r f110-course-upenn/algorithms/localization src/

이 폴더는 ROS의 ​``laser_scan_matcher`` 패키지를 사용하는 localization 패키지를 정의한다.

``setup.bash`` 를 다시 source하면 실행이 가능하다.

.. code-block:: bash

	$​ rospack find localization

​``laser_scan_matcher​node``에 있는 파라미터의 대부분은 ``laser_scan_matcher`` 패키지에 있는 ROS docs에서 가져오며 `here​ <https://wiki.ros.org/laser_scan_matcher#Parameters>`_ 를 참고하자.

미리 저장한 bag 파일에서 ``laser_scan_matcher​`` 를 실행하기 위해서 터미널에서 다음을 실행한다.

.. code-block:: bash

	$ roslaunch localization laser_scan_matcher.launch

RViz를 보지 않으려면 launch 파일에 있는 ``use_rviz`` 을 ``“false”`` 로 설정한다. 차량의 pose를 출력하는 rostopic과 covariance matrix는 ``/pose_with_covariance_stamped`` 라고 부른다. 관련 자료에서 웹에서 찾아보자.

Localization
--------------
lidar로부터 정보를 가지고 차량의 localize 를 시작할 수 있다.

Hector SLAM으로 Localization
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
bag 파일로부터 map을 생성하기 위해서 Hector SLAM를 사용한다. 먼저 ``hector-slam`` 를 설치한다.

.. code-block:: bash

	$​ sudo apt-get install ros-kinetic-hector-slam

다시 생성하기 위해서 다음 명령을 실행한다.

.. code-block:: bash

	$​ roslaunch localization hector_slam.launch

Rviz 윈도우가 떠서 Moore Building 2층 지도가 나타난다. launch 파일은 모든 topic들이 저장된 bag 파일을 읽어온다. Hector SLAM은 map과 localize를 동시에 하기 위해서  ``/scan`` topic만 필요로 한다.(laser scan을 포함) 어떤 odometry 데이터도 사용하지 않았지만 Google Cartographer와 같은 고급 패키지는 odometry 데이터와 IMU 데이터까지도 옵션으로 사용한다.

일단 map이 완전히 생성되면 새로운 터미널에서 yaml으로 map을 저장하기 위해서 following을 수행한다. “-f” 이후 마지막 문자열은 저장할 map의 이름이다. 이 경우 Moore Building bag 파일을 사용하므로 map 이름은 “moore”가 적절한다.

.. code-block:: bash

	$​ rosrun map_server map_saver -f moore

이제 홈디렉토리에 ``levine.yaml`` 파일과 ``moore.pgm`` 파일이 있다. 나중에 이 2개 모두 필요하게 된다. ``localization/localization/maps/moore.yaml`` 아래에 ``moore.pgm`` 파일 뿐만 아니라 버전을 복사 및 붙여넣기한다. 

이제 Hector SLAM이 동작한다. ``hector_slam.launch`` 파일에 대해서 좀더 상세히 알아보자. 파일의 맨 상단에 ``/use_sim_time`` 가 true로 되어 있는 것을 볼 수 있다. 왜냐하면 launch 파일은 bag 파일을 play하기 때문이다. 이 경우 차량이 Moore 주위를 한 번 도는 동안 bag 파일을 저장한다. bag 파일을 play할 때마다 --clock 인자를 포함하는 것이 중요한데 그 이유는 ROS가 bag 메시지에 대해서 동기화 되어 bag 파일을 play되기 때문이다. (추가 정보는 `here <https://answers.ros.org/question/12577/when-should-i-need-clock-parameter-on-rosbag-play/%E2%80%8B>`_ 참고)

rosbag이 ``hector_slam.launch`` 파일에 있는 instruction을 play하고 난 이후에 ``tf2_ros`` transform node가 있다는 것을 알게 되낟. ``tf2_ros`` 는 ``base_link`` 와 laser 사이에서 transform된다. include하는 것은 아주 중요한데 그렇지 않으면 Hector SLAM은 laser가 차량의 무게 중심에서 어느 위치에 있는지 알지 못한다. 이 경우 static transform을 사용하여 laser는 차량의 상대 위치로 이동하지 않는다.

launch 파일에서 ``tf2_ros`` 변한 명령 이후에, ``base_frame``, ``odom_frame``, ``map_size``, ``scan_topic`` 등의 이름을 지정하는 파라미터를 가지는 ``hector_mapping mapping_default.launch`` 파일에 대한 참조를 볼 수 있다. 다음으로 map을 Geotiff 파일로 저장하는데 사용하는 ``hector_geotiff`` 이 있다. 마지막으로 ``rviz_cfg`` 를 인자로 rviz를 실행한다. 매번 시각화를 위해 모든 topic을 선택할 필요는 없다. 아래 알고리즘에서 Rviz에 추가된 몇 초 지연이 있는 launch 파일은, publishing을 시작하면서 시간이 걸리는 특정 node에 대해서 Rviz time을 주기 때문이다. 그렇게 안하면 Rviz는 old data를 가지게 된다.

hector_slam.launch 파일이 제대로 동작하지 않는 경우 디버깅하는 좋은 방법은 ``rqt_graph`` 와 ``rqt_tf_tree`` 를 비교해 보는 것이다.


.. figure:: img/hectorslam1.jpg
	:align: center

“rosrun rqt_graph rqt_graph” 실행해서 생성된 Hector SLAM을 위한 Rqt_graph

.. figure:: img/hectorslam2.jpg
	:align: center

“rosrun rqt_tf_tree rqt_tf_tree” 실행해서 Hector SLAM을 위해 생성한 Rqt_tf_tree


Localization with AMCL (Adaptive Monte Carlo Localization)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
map을 생성하고 다음 단계로 map 내부에서 차량을 localize 할 수 있다. 이런 질문이 있을 수 있다. 'SLAM을 이미 했는데 Hector SLAM을 이용해서 매번 동시에 localize와 map을 할 수 있지 않나?' Hector SLAM은 계산량이 많아서 매번 새로운 map을 생성하기를 원치 않는다. world는 변화가 없다는 가정하므로(wall은 자주 허물어지지 않는다.) 고정된 world 내부에서 차량을 localize하기만 하면 된다. 차량을 localize 하기 위해서 AMCL 이라는 알고리즘을 사용한다.

먼저 ROS용 amcl을 설치한다.

.. code-block:: bash

	$ sudo apt-get install ros-kinetic-amc1

다음으로 amcl을 위해 생성한 launch 파일을 실행한다. amcl은 자신의 ROS master를 생성하므로 roscore를 실행되기를 원치 않는다. 2개 ROS master를 가지게 된다면 간섭이 생기고 따라서 AMCL은 제대로 동작하지 않게 된다.

.. code-block:: bash

	$​ roslaunch localization amcl.launch

5초 지연 후에 Rviz가 화면에 나타난다.(모든게 load되도록 하는(특히 map 서버) 지연 시간을 설정한다.) 다음으로 map이 나타나고 녹색 입자의 map을 통해 차량이 움직인다. Rviz에서 상단 가운데 2D Pose Estimate 클릭하고 차량이 시작할 위치로 클릭해서 드래그 시킨다. 초기 pose를 설정하는 것은 중요하다. 왜냐하면 차량이 원점에서 시작하고 localiztion은 잘못된 값이 되기 때문이다. moore.yaml map에서 차량은 바닥 중앙에 T-shaped crossroad에서 왼쪽을 바라보면서 시작된다. 차량은 원래 위치에서 반시계 방향으로 돌게 된다.

.. figure:: img/amcl1.jpg
	:align: center

AMCL을 위해서 초기 2D pose estimate를 설정한다. 상단 바의 4번째 버튼. 다음으로 map에서 클릭해서 드래그 시킨다.

결국 아래 이미지와 같은 path를 보게 된다. `AMCL <http://wiki.ros.org/amcl%E2%80%8B>`_ 은 ``/tf`` (transform) topic을 필요로 하기 때문에 완벽하지 않다. ``/tf`` 를 생성해야만 하는 최고의 방법은 ``/vesc/odom`` topic을 사용하는 것이다. 이것은 글자 그대로 휠 회전 수와 각도를 카운트해서 odometry를 추정하낟. VESC odometry는 아주 정확하지는 않은데 왜냐하면 에러가 시간에 따라서 누적되기 때문이다. 하지만 차량을 위한 일반 location으로 AMCL을 가이드하기에는 충분히 괜찮다. ``/vesc/odom`` ``/tf`` 로 변환하기 위해서 messagetotf node를 사용했고 이는 AMCL에서 사용된다.

이제 AMCL이 성공적으로 동작하게 되었다. ``amcl.launch`` 파일에서 어떻게 동작하는지 상세하게 알아보자. Hector SLAM을 실행할때와 같이 bag 파일에서 가져와서 play시키고 있으므로 ``/use_sim_time parameter`` 을 true로 설정한다. moore.yaml map을 publish하기 위해서 ``map_server`` node를 실행한다. 해당 line 이후에 Hecktor SLAM을 제공함으로서 동일한 ``base_link_to_laser`` 변환을 포함한다. 해당 line 이후에 launch 파일은 amcl node를 실행한다. 여기서 모든 수치적인 파라미터를 동일하게 유지하고 ``base_frame_id`` 만 수정하고 초기 pose x, y를 추가한다. A는 map frame에 상대적인 차량의 orientation이다. 각 parameter에 있는 정보에 대해서 `AMCL page <http://wiki.ros.org/amcl%E2%80%8B>`_ 에 있는 이러한 것들을 더 자세히 읽어보도록 하자.

만약에 AMCL이 동작하지 않는다면, rqt_graph 와 rqt_tf_tree 를 비교해 보면 좋다. 아래 화면 참고.

.. figure:: img/amcl2.jpg

``rqt_tf_tree`` 으로 AMCL이 실행되는 동안 다른 터미널을 띄워서 ``rosrun rqt_tf_tree rqt_tf_tree`` 실행해서 어떻게 보이지는 확인할 수 있다.

.. figure:: img/amcl3.jpg

새로운 터미널에서 ​``rosrun rqt_graph rqt_graph`` 을 실행하면 이렇게 rqt graph가 생성된다.

.. figure:: img/amcl4.jpg

이제 차량을 map에서 localize할 수 있다. 다음은 무엇을 해야할까? 정말 멋진 것을 할 수 있다. 차량이 follow하는 waypoint를 설정할 수 있고 이 waypoint들은 위치 정보 뿐만 아니라 track의 각 point에서 속도를 가질 수 있다. 차량은 waypoint간 이동을 위해서 순수하게 쫓아가는 알고리즘의 타입을 사용할 수도 있다. 이와 관련된 내용은 다음 장에서 다른다.

Localization with Particle Filter (Faster and More Accurate than AMCL)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
AMCL에서 MIT particle filter로 업그레이드 해보자. AMCL는 초당 4번 가량만 업데이트 한다. 반면에 particle filter는 초당 30번 정도 업데이트 한다. 추가로 particle filter는 GPU를 사용하고 AMCL는 CPU만 사용한다. 결과적으로 100x particle을 사용할 수 있게 된다. 이로서 보다 정확하게 localization이 가능하다. 순수하게 쫓아가는 localization을 위해서 AMCL을 사용하는 경우에 특정 threshold distance를 움직이지 않기 때문에 estimated pose topic에 대한 어떤 메시지도 수신하지 않는다는 문제가 있다. AMCL 파라미터에서 이런 threshold를 낮게 설정하면 localization 성능은 떨어진다. 따라서 Corey Walsh가 작성한 particle filter 코드를 사용한다. 코드는 `publication <https://arxiv.org/abs/1705.01167>`_ 와 같다.

``RangeLibc`` 를 설치하기 위해서 `here <https://github.com/f1tenth/particle_filter>`_ 지시를 따라서 해보자. particle filer를 위한 의존성이 있다.

일단 이런 의존성을 설치하면 소스 코드를 설치할 필요가 없다. 왜냐하면 이미 ``/src/algorithms/particle_filter`` 내부에 소스를 포함하고 있기 때문이다. particle filter의 데모를 보기 위해서 터미널로 이동해서 아래 launch 명령을 입력하자.

.. code-block:: bash

	$​ roslaunch localization particle_filter.launch

아래와 같은 것을 기대할 수 있다.

.. figure:: img/pf1.jpg
	:align: center

Rviz 윈도우가 map과 particle과 함께 나타난다. world에서 차량이 있는 위치를 나타낸다. ``particle_filter.launch`` 파일은 rosbag을 play하고 반시계 방향으로 map 주위를 차량과 particle이 움직이는 것을 볼 수 있다. ``article_filter.launch`` 파일에서 수동으로 메시지를 ``/initialpose`` topic에 전달하지만 Rviz에서 직접 설정하기를 원하면 상단에 있는 2D Pose Estmate 버튼을 눌러서 지도 상에서 클릭 및 드래그하면 된다.

조이스틱으로 실제로 이를 해보고 싶다면 ``particle_filter_live.launch`` 파일을 실행한다.:

.. code-block:: bash

	$​ roslaunch localization particle_filter_live.launch

``particle_filter_live.launch`` 와 ``particle_filter.launch`` 의 차이점은 ``particle_filter_live.launch`` 는 rosbag를 실행하지 않고 time을 시뮬레이션하지 않고 대신에 teleop.launch 파일을 포함한다. 그 이외에는 모든 것이 동일하다.

``particle_filter.launc`` 가 동작하게 되었다. 파일의 내용을 좀더 자세히 살펴보자. ``particle_filter.launch`` 와 ``amcl.launch`` ,  ``hector_slam.launch`` 사이에 많은 공통점이 있따는 것을 알 수 있다. 예를 들자면 map server, ``/use_sim_time`` 파라미터, rosbag과 base_footprint와 laser 사이에 static transform을 알 수 있다. ``particle_filter.launch`` 파일에서 ``base_link`` 대신에 ``base_footprin`` 라는 이름을 사용한다. 왜냐하면 particle filter는 이를 ``base_footprint`` 라고 부르기 때문이다. 다음으로 ``particle_filter`` node를 몇 개 인자와 함께 실행한다. ``particle_filter`` 에게 ``scan_topic`` 는 ``/scan`` 으로 odometry topic은 ``/vesc/odom`` 라고 부르게 한다. 기본적으로 4,000 개의 ``max_particles`` 를 유지한다. 아래는 ``rqt_tf_tree`` 와 ``rqt_graph`` 화면이다.

더 낮은 업데이트 속도로 particle filter를 실행한다면? (GPU가 제공하는 속도를 평가하기 위해서 아니면 더 느린 컴퓨터를 시뮬레이션하기 위해서) particle_filter.launch 파일 내부에서 “rmgpu”에서 “bl”로 “range_method”를 변경할 수 있다. particle filter Github repo에 문서로 “bl”은 GPU를 사용하지 않고 더 적은 particle를 가진다. 우리 테스팅에서는 “bl”은 대략 7Hz의 inferred_pose 업데이트 속도를 달성한 반면에 “rmgpu”는 40Hz를 달성했다.

.. figure:: img/pf2.jpg
	:align: center

	Rqt_graph for particle filter

.. figure:: img/pf3.jpg
	:align: center

	Rqt_tf_tree for particle filter


