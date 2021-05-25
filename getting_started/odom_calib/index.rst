.. _doc_calib_odom:

Odometry 칼리브레이션
=========================
.. note:: :ref:`Building the Car <doc_build_car>`, :ref:`System Configuration <doc_software_setup>`, :ref:`Installing Firmware <doc_build_car_firmware>`, and :ref:`Driving the Car <doc_drive>` 가 완료되어야 한다. 

마지막 단계

**필요 장치:**
	* 완성된 F1TENTH 차량
	* Pit/Host 컴퓨터
	* Logitech F710 조이스틱
	* Tape measure

**난이도:** 중간

**소요시간:** 1 시간

이제 만들기, 설정하기, 설치가 완료되어서 차량의 odometry의 칼리브레이션이 필요하다. VESC는 속도(m/s)와 조향각(radians)을 입력받는다. 하지만 모터와 서보는 RPM과 서보 position 명령이 필요하다. 차량에 따라 파라미터 변경이 필요하다.

#. `vesc.yaml <https://github.com/f1tenth/f1tenth_system/blob/master/racecar/racecar/config/racecar-v2/vesc.yaml>`_ 에 있는 파라미터 칼리브레이션이 필요하다.

#. `Tuning Guide <https://mushr.io/tutorials/tuning/>`_ 와 `Mushr <https://mushr.io/about/>`_  튜터리얼을 따라한다.

.. tip:: 
  질문은 `forum <http://f1tenth.org/forum.html>`_ 참고
