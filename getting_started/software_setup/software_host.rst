.. _doc_software_host:

1. Pit/Host 설정
==================
**Equipment Used:**
	* Laptop/Computer

**소요시간:** 1-2 hours

개요
----------
차량에는 리눅스를 운영한다. **Pit** 노트북에 Linux와 ROS를 설치한다. 노트북은 디버깅 지원 및 개발 환경으로 사용한다. 이미 Linux와 ROS에 대한 지식이 있다고 가정한다. 만약 Linux나 ROS에 익숙하지 않다면 다음을 참고한다 :

	* `Introduction to Linux Operating System (OS) <https://www.guru99.com/introduction-linux.html>`_
	* `The Linux Command Line for beginners <https://ubuntu.com/tutorials/command-line-for-beginners#1-overview>`_

.. important:: 지원 환경 **Bionic 18.04/ROS Melodic**.

**Pit** 컴퓨터는 Host 컴퓨터나 노트북과 동일한 의미로 사용한다.

**Configure F1TENTH System** 섹션에서 Host 컴퓨터로 NVIDIA JetPack 소프트웨어를 **NVIDIA Jetson NX**에 flash시킨다. Host 컴퓨터에서 SSH로 **NVIDIA Jetson NX**에 접근한다.

1. Ubuntu 설치
---------------------
노트북에 Linux가 깔려있지 않는 경우, 외부 하드 드라이브에 Ubuntu를 설치해서 듀얼부팅을 하는 것을 추천한다. `here <https://ubuntu.com/download/desktop>`_ 에서 Ubuntu image를 다운받고  `here <https://ubuntu.com/tutorials/tutorial-install-ubuntu-desktop#1-overview>`_ 에서 설치방법을 따른다.

Mac이나 Windows에서는 VM(Virtual Machine)을 사용가능 하며 :ref:`Appendix A <doc_appendix_a>` 를 참고하자.

Ubuntu 설치하는데 시간이 좀 걸릴 수 있다.

2. ROS 설치하기
------------------
`here <https://wiki.ros.org/ROS/Installation>`_ 를 참고하여 설치하자.


.. image:: img/host/host01.gif
	:align: center
	:width: 300px
