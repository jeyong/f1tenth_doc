# F1tenth 문서

[F1tenth](http://f1tenth.org) 문서와 관련된 소스 코드로 reST(reStructuredText markup language)로 되어 있다.

### 기존 페이지 수정

페이지 수정은 기존 .rst 파일을 수정해서 commit한 후에 pull request하면 된다.

### 새 페이지 추가

To add a new page, create a .rst file with a meaningful name in the section you want to add a file to, e.g. `going_forward/more_cool_stuff.rst`. Write its content like you would do for any other file, and make sure to define a reference name for Sphinx at the beginning of the file (check other files for the syntax), based on the file name with a "doc_" prefix (e.g. `.. _doc_cool_stuff:`).

You should then add your page to the relevant "toctree" (table of contents, e.g. `tutorials/3d/index.rst`). By convention, the files used to define the various levels of toctree are prefixed with an underscore, so in the above example the file should be referenced in `tutorials/3d/_3d_graphics.rst`. Add your new filename to the list on a new line, using a relative path and no extension, e.g. here `light_baking`.

### Sphinx와 reStructuredText 문법

문법에서 Sphinx's [reST Primer](https://www.sphinx-doc.org/en/stable/rest.html)와 [official reference](http://docutils.sourceforge.net/rst.html)를 확인

Sphinx은 특정 동작을 위해서 특정 reST 코멘트를 사용한다. 컨텐츠 테이블에 (`:toctree:`) 나 상호 참조 동작을 정의한다. 더 상세한 내용은 [official Sphinx documentation](https://www.sphinx-doc.org/en/stable/index.html) 을 참고하자.

### 이미지와 파일 첨부

image 추가는 해당 .rst 파일에 있는 `img/` 폴더에 이름을 잘 붙여서 추가:
```rst
.. image:: img/image_name.png
```

유사하게 첨부를 추가할 수 있다. .rst 파일에 있는 `files/` 폴더에 넣고 다음과 같이 마크업을 사용한다.:
```rst
:download:`myfilename.zip <files/myfilename.zip>`
```

## Sphinx로 만들기

HTML 웹사이트를 만들기 위해서는(PDF, EPUB나 LaTeX) [readthedocs.org theme](https://github.com/snide/sphinx_rtd_theme)와 [Sphinx](https://www.sphinx-doc.org/) >= 1.3 을 설치한다. Python3에서만 테스트하였다.

이런 tool들은 [pip](https://pip.pypa.io)을 사용해서 설치하는게 최선이다. Linux에서 `pip3` 나 `python3-pip` 를 제공하며 다음과 같이 실행할 수 있다.:

```sh
pip3 install sphinx
pip3 install sphinx_rtd_theme
```

이 repo의 root 폴더에서 HTML 문서를 만들려면 다음과 같이 하자:

```sh
make html
```

or:

```sh
make SPHINXBUILD=~/.local/bin/sphinx-build html
```

`classes/` 폴더에 파일이 많으면 컴파일에 시간이 걸린다.
브라우저에서 `_build/html/index.html`를 열어서 변경내용을 확인할 수 있다.

### Windows에서 Sphinx로 빌드하기

Windows에서는 다음과 같은 것들이 필요하다:
* Python installer [here](https://www.python.org/downloads/)
* Python 설치하면서 "Add Python to PATH" 체크 박스를 체크한다.
* 위에서 말한 `pip` 명령을 사용한다.

이 reo의 root 폴더에서 빌드를 하며 이를 위해 `make.bat`를 제공하고 있다:
```sh
make.bat html
```

다른 대안으로 아래 명령으로 빌드할 수 있다:
```sh
sphinx-build -b html ./ _build
```

처음 빌드하는 동안 여러 설치 프롬프트가 뜨고 LateX 플러그인 설치를 물어본다.
다른 윈도우 뒤에 열릴 수도 있으니 잘 살펴보도록 하자.

일반적인 `make` toolchain를 설치하고(MinGW) `make html`로 빌드할 수 있다.

### Sphinx와 virtualenv로 빌드하기

이 프로젝트 내에서 Sphinx 설치를 원하는 경우라면 virtualenv를 사용해서 설치할 수 있다.
이 repo의 root 폴더에서 다음을 실행한다:

```sh
virtualenv --system-site-packages env/
. env/bin/activate
pip3 install sphinx
pip3 install sphinx_rtd_theme
```

다음으로 `make html`를 한다.

## License

라이센스는 Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License를 따른다. http://creativecommons.org/licenses/by-nc-sa/4.0/ 를 참고하자.