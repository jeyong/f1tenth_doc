.. _doc_software_setup:

F1TENTH 시스템 설정
========================
.. note::  :ref:`Building the Car <doc_build_car>` 완료

이 섹션에서는 NVIDIA Jetson NX와 host 컴퓨터 설정을 다룬다.

**필요 장치:**
	* 완성된 F1TENTH 차량
	* 노트북/컴퓨터
	* 모니터
	* HDMI cable
	* 키보드
	* 마우스
	* WiFi
	* 이더넷 케이블(Pit/Host 컴퓨터가 WiFi가 안되는 경우)

**난이도:** 중고급

**소요시간:** 2-3 시간

F1TENTH 차량을 실제로 동작시키고 나면 차량에 프로그래밍을 해서 뭔가 유용한 일을 하고 싶어진다.

**Configure F1TENTH System** 섹션은 NVIDIA Jetson NX을 설정한다. 프로그램을 F1TENTH Autonomous 차량 시스템에 프로그램하고 차량과 통신한다.

아래 이미지는 F1TENTH Autonomous 차량 시스템에 정보의 흐름을 나타낸다.

.. figure:: img/f1tenth_sys_flow_NEW.png
  	:align: center

	F1TENTH Autonomous 차량 시스템의 정보 흐름.

**NVIDIA Jetson NX**는 전체 시스템의 중요 brain이다. F1TENTH 차량에 **Servo** 와 **Brushless Motor** 를 제어하는 **VESC** 에 명령을 전달한다. **NVIDIA Jetson NX** 는 USB나 이더넷을 통해서 **Lidar**로부터 정보를 수신한다. **Pit/Host** 노트북은 **NVIDIA Jetson NX** 에 **SSH** 를 통해서 원격으로 연결할 수 있다.

5개 세브섹션:

#. :ref:`Pit/Host setup <doc_software_host>` ROS와 시뮬레이션 설치 방법(**Pit/Host** 와 컴퓨터)
#. :ref:`Configuring the NVIDIA Jetson NX <doc_optional_software_nx>` **NVIDIA Jetson Xavier NX** 를 설정하기 위한 필요한 단계를 포함
#. :ref:`Combine setup <doc_software_combine>` **Pit/Host** 컴퓨터와 **NVIDIA Jetson NX** 사이에 무선통신 시스템을 설정하는 방법. 위 2개 섹션은 완료한 상태여야 한다.
#. :ref:`DEPRECATED - Configuring the TX2 <doc_software_jetson>` **NVIDIA Jetson TX2** 를 설정하기 위한 모든 필요한 단계들을 포함한다.

.. tip::
  질문은 `forum <http://f1tenth.org/forum.html>`_ 참고.

이 섹션의 대부분의 내용은 `Dr. Rosa Zheng <http://www.lehigh.edu/~yrz218/>`_ 가 수행하였다.

.. toctree::
   :maxdepth: 1
   :name: Software Setup
	 :hidden:

   software_host
   optional_software_nx
   software_combine
   software_jetson
