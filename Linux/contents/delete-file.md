# 특정 위치에 있는 특정 파일 삭제하기.

웹서버에서 `marko` 템플릿을 사용하고 있는데 `github` 레파지토리에 빌드시 생성되는 `*.marko.js` 파일도 같이 올리고 있었다.

그러다 보니 서버에 올라가는 파일 자체가 너무 많고 보기도 힘들어서 빌드시 생성되는 파일은 삭제하고 올리기로 하였다.

그런데 이거를 `vscode`에서 지우다가 이건 아니다 싶어서 검색했더니 바로 해결 방법 찾았다.

``` bash
find ./views -name '*.marko.js'
// view 디렉토리에 '*.marko.js' 조건을 같는 파일을 찾는 명령어이다. 

find ./views -name '*.marko.js' -ls
// view 디렉토리에 '*.marko.js' 조건을 같는 파일을 -ls 옵션으로 보여준다. 

find ./views -name '*.marko.js' -exec rm -f {} \;
// view 디렉토리에 '*.marko.js' 조건을 같는 파일을 찾아서 삭제하라는 명령어이다. 
```
*참조*
- [리눅스 포털 : find,rm (특정위치의 특정파일을 모두 찾아서 삭제하기)](https://www.linux.co.kr/home/lecture/index.php?cateNo=&secNo=&theNo=&leccode=201)