.. _doc_software_advance:

고급 설정
===================
다음 2개 섹션은 옵션이다.

* :ref:`Wireless Hot Spot on the NVIDIA Jetson NX <jetson_wireless>` 넓은 트랙에서 테스트하고 싶으면 차량에 직접 연결해야한다.
* :ref:`VNC Server on the NVIDIA Jetson NX <jetson_wireless>` mapping과 localization 알고리즘을 실행하는 경우 유용. RViz를 보고 이런 도구를 잘 사용하는게 중요하다.

 .. _jetson_wireless:

 NVIDIA Jetson NX에서 무선 WiFi
---------------------------------
**사용 장치:**
	* 완성된 F1TENTH 차량
	* Pit/Host 노트북 혹은
	* 외부 모니터, HDMI 케이블, 키보드, 마우스

**소요시간:** 30 분

더 넓은 트랙에서 테스트할려면 차량에 직접 연결의 필요성을 느끼게 된다. 솔루션은 NVIDIA Jetson NX에 hot spot을 설정한다.

**Pit**에서 SSH를 사용해서 **NVIDIA Jetson NX**에 연결한다. 아니면 **NVIDIA Jetson NX**에 키보드, 마우스를 연결해서 사용할 수 있으며 시스템 Settings > Network에서 가능하다.

.. image:: img/combine/wireless1.jpg

On the bottom center of the pop-up window for the network, click on “Use as Hotspot...” You will no longer have internet connection because your wireless antennas will now be used as a hot spot rather than to connect to the previous Wi-Fi connection that you were on.

Note that if you plan on using the wireless hotspot feature often, you will want it to boot up on startup. To do this, open up Network Connections, under Wi-Fi select Hotspot and Edit.

.. image:: img/combine/wireless2.jpg

Under General click on “Automatically connect to this network when available”.

On your phone, tablet, or laptop you can now connect directly to this Hotspot, and ssh into it. You can use a VNC client as well if you have set up a VNC server on the car. The default IP address for Hotspot on the Jetson is 10.42.0.1.

 .. _jetson_vnc:

NVIDIA Jetson NX에서 VNC Server
-------------------------
**Equipment Used:**
	* 완성된 F1TENTH 차량
	* Pit/Host 노트북 혹은
	* 외부 모니터, HDMI 케이블, 키보드, 마우스

**소요시간:** 1 시간

mapping와 localization 알고리즘을 구동되기 시작할 때, RViz를 보고 도구를 사용해야한다. 즉 원격 데스크탑을 위한 GUI 인터페이스가 필요하다.

Jetson에서 VNC server를 설정하는 것은 원격으로 Jetson을 제어할 수 있다. 장점은 무엇인가? 차량이 실제 world에서 운행되면 Jetson을 HDMI로 연결할 수 없다. 고전적인 방법은 디렉토리를 보기 위해서 Jetson에 ssh를 사용하는 것이지만 Rviz와 같은 그래픽 프로그램을 볼려면 어떻게 해야하나? (실시간으로 laser scan을 보려면) Jetson에서 여러 터미널 창을 띄워보고 싶다면? VNC 서버가 이런 것을 가능하게 해준다.

#. XIIVNC 설치

	.. code-block:: bash

		sudo apt install x11vnc
#. password 파일 생성

	.. code-block:: bash

		echo mypassword > /home/nvidia/.vnc/password

	원하는 password로 변경. .vnc 디렉토리를 생성해야만 할 수도 있다.
#. windows/command/super 키를 누르고 ‘startup applications’ 를 검색한다. 새로운 startup 명령을 생성하고 이름을 주기 위해서 다음 명령을 수행:

	.. code-block:: bash

		/usr/bin/x11vnc -auth guess -forever -loop -noxdamage -repeat -passwdfile /home/nvidia/.vnc/password -rfbport 5900 -shared
#. Jetson을 재시작. vnc server는 재시작 후에 실행해야만 한다.
#. 원하는 VNC client(Ubuntu는 기본적으로 Remmina 설치되어 있고 VNC Viewer는 대부분 플랫폼에서 사용 가능)에서 port 5900으로 차량 IP에 연결해서 원격 데스트탑을 볼 수 있다.

.. note::
  NVIDIA Jetson NX의 네트워크 카드를 사용해 본 바로 hotspot은 가끔 동작하지 않는다. 만약 NVIDIA Jetsn NX의 네트워크 카드에 문제가 있다면 USB wifi 동글을 대신 사용하자.


.. image:: img/combine/wireless4.gif
	:align: center
	:width: 300px
