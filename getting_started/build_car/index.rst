.. _doc_build_car:

F1TENTH Car 만들기
=====================
만들기 가이드라인에서는 차량의 하드웨어 설정에 집중한다. 차량은 `Traxxas Slash 4x4 Premium <https://traxxas.com/products/models/electric/6804Rslash4x4platinum>`_ 이고 탑재 컴퓨터는 `NVIDIA Jetson Xavier NX <https://developer.nvidia.com/embedded/jetson-xavier-nx-devkit>` 이다. 마지막에는 모든 기능이 동작하는 차량이 완성되게 된다.

차량에 대한 3개 주요 섹션 :

.. image:: img/build_outline_NX.JPG

| [1] will be referred to as the **Lower Level Chassis**
| [2] will be referred to as the **Autonomy Elements**
| [3] will be referred to as the **Upper Level Chassis**

#. 먼저, :ref:`Lower Level Chassis <doc_build_lower_level>`에서 시작한다. 차량의 기반이 되는 부분이다.
#. 다음으로 모든 :ref:`Autonomy Elements <doc_build_autonomy_elements>` 를 합친다.
#. 다음은 :ref:`Upper Level Chassis <doc_build_upper_level>` 에 장착한다.
#. 마지막으로 Upper Level Chassis를 Lower Level Chassis와 :ref:`connected <doc_build_all_together>` 한다.

.. danger::
	**LIPO (LITHIUM POLYMER) 배터리 안전 관련**

	차량은 LIPO 배터리를 사용한다. LiPO 배터리를 사용하면 오랜 시간동안 운행이 가능하다. 하지만 주의가 필요하다. 작은 사이즈에 많은 에너지를 지니고 있고 차량을 손상시키거나 불이 날 수 있다. 아래와 같은 안전에 관련된 내용을 숙지하도록 하자.

	* 충전하는 동안 항상 지켜보고 방염 가방에 두도록 한다.
	* 사용하지 않는 경우 LIPO 배터리를 차량에 연결된 상태로 방치하지 않는다. 배터리는 방전되어 전압이 너무 떨어지면 충전이 불가능하게 된다.
	* 배터리에서 펑하는 소리, 냄새, 연기 등이 나면 바로 차량과의 연결을 해제한다.
	* 쇼트나게 하지 마라~
	* 배터리를 반대로 연결하지 않도록 주의한다. VESC와 power board가 쇼트가 날 수 있다.
	* 배터리를 부주의하게 다르면 발생하는 일에 대해서 `video <https://www.youtube.com/watch?v=gz3hCqjk4yc>`_ 를 참고하자.

**난이도:** 중급

**소요시간:** 1.5 hours


.. note::
  만들거나 설정에 관한 질문은 `forum <http://f1tenth.org/forum.html>`_ 를 참고하자.

.. toctree::
   :maxdepth: 1
   :caption: Building the Car
   :name: sec-build-instructions
   :hidden:

   bom
   lower_level_chassis
   autonomy_elements
   upper_level_chassis
   all_together
   additional_components
