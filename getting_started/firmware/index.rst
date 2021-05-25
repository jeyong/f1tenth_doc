.. _doc_build_car_firmware:

F1TENTH Software 설치
=======================
.. note:: :ref:`Building the Car <doc_build_car>` 와 :ref:`System Configuration <doc_software_setup>` 는 이미 완료했다고 가정한다.

이 섹션을 통해 VESC 튜닝과 lidar 연결이 완료된다.

**필요 장치:**
	* 완성된 F1TENTH 차량
	* (각 섹션별로 필요 장치 확인)

**난이도:** 중고급

**소요시간:** 1.5 시간

물리적으로 차량과 시스템 설정이 완료되고 난 후에 차량에 필요한 펌웨어를 설치할 수 있다.

2개 서브 섹션이 있다. :ref:`VESC 설정하기 <doc_firmware_vesc>` 와 lidar 연결하기(Hokuyo 30LX인 경우 USB이므로 skip가능)

.. toctree::
   :maxdepth: 1
   :name: Firmware 설정
   :hidden:

   firmware_vesc
   firmware_hokuyo10
   drive_workspace

#. :ref:`VESC 설정하기 <doc_firmware_vesc>` VESC 설정과 튜닝하기
#. :ref:`Hokuyo 10LX 이더넷 연결 설정 <doc_firmware_hokuyo10>`  Jetson NX에 연결하기
#. :ref:`Workspace Setup <doc_drive_workspace>` workspace 설정 및 차량에 필요한 모든 컴포넌트 연결하기

.. tip::
  질문은 `forum <http://f1tenth.org/forum.html>`_ 참조
