.. _doc_firmware_vesc:

1. VESC 설정
==========================
.. danger:: **중요 안전 팁**

    * 차량을 스탠드에 놓고 작업해야 휠이 돌더라도 움직이지 않느낟. RC 카 스탠드가 없는 경우 Jetson의 박스를 사용하면 된다.
    * 모터 테스트하는 동안 차량을 잡고 있어야 안전하다.
    * 테스팅하는 동안 휠 근처에는 사람이나 물건이 없어야 한다.
    * 파워 서플라이 대신에 완충된 LiPO 배터리를 사용해야 모터가 회전할 수 있는 충분한 전류를 가질 수 있다.

**필요 장치:**
	* 완성된 F1TENTH 차량
	* 차량을 거칠할 박스나 `Car stand <https://www.amazon.com/Duratrax-Tech-Deluxe-Truck-Stand/dp/B0014T74MS/ref=sr_1_6?keywords=rc+car+jack&link_code=qs&qid=1584393402&sr=8-6>`_
	* Laptop/computer (Linux일 필요는 없다.)

**걸리는 시간:** 1 시간

.. note::

	VESC mkIV를 사용하고 있는 경우 `here <https://github.com/RacecarJ/vesc-firmware>`_ 를 참고하자.

1. VESC 도구 설치하기
-----------------------------
ROS driver 패키지와 연동시키기 위해서 VESC를 설정해야 한다. 시작하기 전에, `VESC Tool v2.03 <https://github.com/rpasichnyk/vesc_tool/releases/tag/v2.03>`_ 를 설치한다. 최신 버전은  v2.05인데 여기에는 우리가 사용하는 펌웨어가 들어가 있지 않다. Linux VESC tool v2.03 은 `여기 <https://drive.google.com/file/d/1tGrboseLUIlSdDjkhxDVxyopWc0h4_LC/view?usp=sharing>`_ 에서 찾을 수 있다.

..
	PC에서 `VESC Tool <https://vesc-project.com/vesc_tool>`_. MacOS용 VESC Tool은 `여기 <https://github.com/rpasichnyk/vesc_tool/releases>`_ .

2. VESC 전원공급
-------------------------
먼저 VESC에 전원을 공급한다. 배터리에 플러그를 꼽는다.

.. figure:: img/vesc/vesc01.JPG
	:align: center

	배터리에 연결. 극성이 올바른지 확인한다.

VESC를 설정하기 위해서 Powerboard를 켤 필요는 없다.

다음으로 Jetson NX의 VESC의 USB 케이블을 뽑고 USB를 PC에 연결한다. 더 긴 케이블이 필요할 수 있다.

.. figure:: img/vesc/vesc02.JPG
	:align: center

	micro USB 케이블을 VESC에서 컴퓨터로 연결

3. VESC를 PC에 연결하기
-----------------------------------------
VESC tool을 실행하기. 페지이의 왼쪽 하단에 있는 **AutoConnect** 버튼을 누른다. VESC가 연결된 후에 화면의 오른쪽 바닥에 있는 업데이트 상태를 봐야만 한다.

.. figure:: img/vesc/connect.png
	:align: center

	VESC Tool에 있는 *Autoconnect*를 클릭한다.

4. VESC에 있는 펌웨어를 업데이트하기
-----------------------------------------
..
	This is Kim's edit for people using VESC tool 2.05.
	We are currently using an older firmware version of the VESC. Download it `here <https://drive.google.com/file/d/19veWRe745p3efOyn-Ff3RRYlADhp_c5V/view?usp=sharing>`_. This is assuming that you are also using the version 4.12 of the VESC hardware. Read more about it `here <https://github.com/RacecarJ/vesc-firmware/tree/master/firmware>`_. Switch to the "Custom File" tab and upload the file that you downloaded. There will be a warning. Continue to upload.

가장 먼저해야할 일은 VESC의 펌웨어를 업데이트하는 것이다. 화면의 왼쪽에 **Firmware** 탭을 누른다. 화면의 왼쪽 아래에 **Show non-default firmwares** 체크 박스를 체크한다. 오른쪽에 추가 펌웨어 옵션이 나타난다. **VESC_servoout.bin** 옵션을 선택한다. 결국 화면의 오른쪽 아래에 연결된 VESC의 펌웨어를 업데이트하기 위해서 다운로드 버튼을 누른다. 페이지의 하단에 상태바가 펌웨어 업데이트 상태를 보여준다. 마치고 나면 화면에 prompt 를 지시를 따른다.

.. figure:: img/vesc/firmware.png
	:align: center

	펌웨어 업데이트하기

5. Motor 설정 XML 업로드하기
-------------------------------------------
펌웨어 업데이트 후에 **Load Motor Configuration XML** 를 드롭다운 메뉴에서 선택하고 `here <https://drive.google.com/file/d/1-KiAh3hCROPZAPeOJtXWvfxKY35lhhTO/view?usp=sharing>`_ 에서 제공하는 XML 파일을 선택한다. XML가 업로드되고 난 후에, 화면 오른쪽에 있는 **Write Motor Configuration** 버튼을 누른다.(이 버튼은 아래 화살표와 M이 쓰여진 버튼) 향후에 모터 설정을 바꿀때마다 이 버튼을 눌러야만 한다.

.. figure:: img/vesc/xml.png
	:align: center

	XML 파일 업로드

6. 모터 파라미터 검출 및 계산하기
------------------------------------------------
FOC motor 파라미터를 검출하고 계산하기. 왼쪽에 **Motor Settings** 아래에 **FOC** tab으로 이동. 화면의 아래에 화살표의 방향을 따라서 차례로 하나씩 4개 버튼을 클릭한다. measuring process 동안에 모터는 소음이나 회전이 발생할 수 있다. 차량의 휠이 확인한다.

.. figure:: img/vesc/detect_motor.png
	:align: center

	모터 검출

모터 파라미터를 검출한 후에, 화면의 아래에 영역이 녹색으로 바뀐다. **Apply** 버튼을 클릭하고 **Write Motor Configuration** 버튼을 클릭한다.

.. figure:: img/vesc/apply_motor.png
	:align: center

	모터 파라미터를 적용

7. the Openloop Hysteresis and Openloop Time 변경하기
-------------------------------------------------------
화면의 상단에 **Sensorless** tab으로 가기. **Openloop Hysteresis** 와 **Openloop Time**을 0.01로 변경하기. **Write Motor Configuration** 버튼 누르기.

.. figure:: img/vesc/open_loop.png
	:align: center

	openloop time을 변경하기

8. PID controller를 튜닝하기
---------------------------------
PID controller를 튜닝을 시작하기. 모터에서 RPM 응답을 보기 위해서 왼쪽에 **Data Analysis** 아래에 **Realtime Data** tab으로 이동한다. 오른쪽에 있는 **Stream Realtime Data** 버튼을 클릭한다.(RT라고 적힌 버튼) 화면의 상단에 **RPM** tab으로 이동. RPM 데이터가 출력되는 것을 볼 수 있다.

.. figure:: img/vesc/realtime.png
	:align: center

	RPM data streaming.

모터에 대한 step 응답을 생성하기 위해서, 화면 아래에 target RPM을 설정한다.(범위 : 2000 ~ 10000 RPM) 모터를 구동시키기 위해서 text box 옆에 있는 play 버튼을 누른다. 모터가 회전하기 시작하면 차량의 휠을 확인한다. Anchor이나 STOP 버튼을 클릭해서 모터를 정지시킨다. 
To create a step response for the motor, you can set a target RPM at the bottom of the screen (values between 2000 - 10000 RPM). Click the play button next to the text box to start the motor. Note that the motor will spin, so make sure the wheels of your vehicle are clear from objects. Click the Anchor or STOP button to stop the motor.

.. figure:: img/vesc/response.png
	:align: center

	모터의 step response

gain을 조정하기 위해서 왼쪽에 **Motor Settings** 아래에 **PID Controllers** tab으로 이동하여 Speed Controller gains을 변경한다. 튜닝한 PID gains을 적용하는 방법이다. 만약 오실레이션이 발생하면 Speed PID Kd filter를 변경한다.

.. figure:: img/vesc/pid_gains.png
	:align: center

	PID gain 조정

.. danger:: **설정을 마치면 배터리는 빼놓도록 한다. LIPO 배터리 닳으니까.**

만약 튜닝이 잘 되었으면 아래보다 더 잘 달리게 된다.:

.. figure:: img/vesc/vesc03.gif
	:align: center
	:width: 300px
