.. _doc_calib_odom:

Odometry를 칼리브레이션하기
=========================
.. note:: 이미 :ref:`Building the Car <doc_build_car>`, :ref:`System Configuration <doc_software_setup>`, :ref:`Installing Firmware <doc_build_car_firmware>`와 :ref:`Driving the Car <doc_drive>`가 완료되었다고 가정한다.

마지막 단계.

**필요한 장비:**
	* 완성된 F1TENTH vehicle
	* Pit/Host 컴퓨터
	* Logitech F710 joypad
	* Tape measure

**난이도:** 중간

**걸리는 시간:** 1 시간

모든 것이 준비되었다면 차량의 odometry를 칼리브레이션이 필요하다. VESC는 속도(m/s)와 조향(radians)를 입력받는다. 모터와 servo는 RPM(revolution per minute)와 servo position을 명령으로 받는다. 차량에 맞게 변환 파라미터 튜닝이 필요하다.

#. `vesc.yaml <https://github.com/f1tenth/f1tenth_system/blob/master/racecar/racecar/config/racecar-v2/vesc.yaml>`_ 에 있는 파라미터를 칼리브레이션한다.

#. `Tuning Guide <https://mushr.io/tutorials/tuning/>`_ 와 `Mushr <https://mushr.io/about/>`_ 를 참고해서 따라하자.

.. tip::
  설정 관련 질문이 있는 경우 `forum <http://f1tenth.org/forum.html>`_에 질문한다.
