.. _doc_appendix_A:

Appendix A: Host와 VM 사이에 폴더 공유
=================================================
Ubuntu가 실행되는 host 컴퓨터가 없는 경우, VM(Virtual Machine)을 통해서 가능하다. host(사용하는 기본 OS)와 guest(VM에서 돌아가는 Ubuntu) 사이에 공유 폴더를 공유하는 것이 유용하다.

아래는 Mac OS host로 Virtualbox에서 Ubuntu 16.04 guest를 실행하였다.

host에 이미 Virtualbox가 설치되었다면 설치된 Ubuntu 16.04를 설치하자. 좀더 자세한 내용은 온라인을 검색해보도록 한다. 아래와 같이 설치할 수 있다.

#. host에서 공유할 폴더를 생성하낟. 이를 sfVM이라고 부르고 ``~/sfVM``에 위치한다.
#. VirtualBox 시작하기. 실행되고 있는 VM이 없다는 것을 확인한다.
#. Ubuntu VM을 선택
#. *Settings* -> *Shared Folders* 클릭하고 ‘+’ 기호를 클릭해서 sfVM 위치로 이동. **Auto-mount**와 **Make permanent**를 체크한다.
#. VM 시작하기
#. Guest 추가하기 설치 : VBox 메뉴에서 *Devices* -> *Install Guest Additions* -> ... -> 를 클릭한다.
#. Software 실행하기
#. 공유 폴더를 수동으로 마운트하기 :
	.. code:: bash

		$​ mkdir ~/guest_sfVM (or whatever you want to call it)
		$​ id
		uid=1000(houssam) gid=1000(houssam)
		$​ sudo mount -t vboxsf -o uid=1000,gid=1000 sfVM ~/guest_sfVM

#. host에서 성공적으로 완료되었는지 확인하고 ``~/sfVM`` 공유폴더에 파일 넣어보기. guest에서 ``~/guest_sfVM``가 보여야 한다.