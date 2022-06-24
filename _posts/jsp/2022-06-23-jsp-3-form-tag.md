---
title: 쉽게 배우는 JSP 웹 프로그래밍 - 3 - 폼 태그
author: namu6747
date: 2022-06-23 09:11:00 +0900
categories: [공부, JSP]
tags: [자바, 웹]
---

- - - - form tag - - - - > 사용자가 다양한 정보를 입력하고 서로 전달

action : 폼 데이터를 받아 처리하는 웹 페이지의 URL 설정
method : 폼 데이터가 전송되는 HTTP 방식
 GET, POST는 보안 유무로 따지면 됨

name : 폼을 식별하기 위한 이름
target : 처리 결과의 응답을 실행할 프레임
enctype : 폼을 전송하는 콘텐츠 MIME 유형을 설정
accept-charset : 폼 전송에 사용할 문자 인코딩 설정

 - - input tag - -
type : text, radio, checkbox, password, hidden, file, 
 	button, reset, submit
name : 입력 양식을 식별하는 이름
value : 입력 양식의 초깃값
readonly, maxlength, size, disabled, checked

* HTML5 추가
required, autofocus, placeholder, pattern(정규표현식)

 - - select tag - -
여러 개의 항목이 나타나는 목록 상자에서 항목을 선택하는 태그
name, size, multiple

내부 목록은 option tag로 추가
value, selected, disabled

> 동일한 name을 가진다
> optgroup 태그와 label 속성으로 그룹화도 가능

 - - textarea tag - -
name, cols, rows, wrap[off, soft, hard]

wrap = hard 캐리지 리턴 문자를 전달
"캐리지 리턴(carriage return) 또는 간단히 리턴(return)은 
 문자의 새 줄을 시작하는 데 쓰이는 제어 문자나 그 구조를 가리킨다.
 컴퓨터 환경에서는 간단히 CR로 줄여 쓴다. 
 원래 캐리지 리턴은 타자기의 구조나 레버를 가리키는 용어였다."


 - - 파일 업로드 - - 
MultipartRequest, Apache API
cos.jar, commons-fileupload.jar, commons-io.jar
위와 같은 라이브러리를 이용해야 서버로 간단히 전송할 수 있음

 - MultipartRequest -
생성자 매개변수
 request, saveDirectory, maxPostSize, encoding, policy
ex)
new MultipartRequest(request, "C:\\upload", 
	5*1024*1024, "utf-8", new DefaulFileRenamePolicy());

getContentType
getParameter
getParameterNames
getFile
getFileNames
getFilesystemName
getOriginalFileName

- Commons-FileUpload -

DiskFileUpload 기본생성자.parseRequest(request)
setRepositoryPath : 업로드된 파일 임시로 저장할 디렉토리
setSizeMax : 최대 파일 크기 설정
setSizeThreshold : 메모리상에 저장할 최대 크기 설정
parseRequest : multipart/form-data 유형의 요청파라미터 get

파일업로드 인스턴스 생성 후 파싱한 리스트 생성
리스트 이터레이터를 통해 FileItem를 생성
isFormField : 파일(false)인지 일반데이터(true)인지
getFieldName : R.P.K get
getString : 기본 문자 인코딩을 사용하여 R.P.V get
getName : 업로드된 파일의 이름 get
getSize : 업로드된 파일의 크기
get : 업로드된 파일을 byte[] 
isInMemory : 메모리 저장 유무. 임시 디렉토리에 있으면 false
delete 
write : 파일과 관련된 자원 저장 
getContentType : 콘텐츠 유형 반환

> 직접 해봐야 쉬움