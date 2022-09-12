---
title: 9. 도서 관련 - Betty
author: namu6747
date: 2022-09-06 09:09:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

## &nbsp;&nbsp;&nbsp; index
1. [서론](/posts/project-betty-0/)
2. [기획 및 명세](/posts/project-betty-1-concept/)
3. [패키지 트리](/posts/project-betty-2-package-tree/)
4. [프로젝트 환경](/posts/project-betty-3-config/)
5. [메시지와 국제화](/posts/project-betty-4-message/)
6. [예외 다루기](/posts/project-betty-5-exception/)
7. [검증](/posts/project-betty-6-validation/)
8. [계정 관련](/posts/project-betty-7-sign/)
9. [권한 인터셉터](/posts/project-betty-8-interceptor/)
10. [도서 관련](/posts/project-betty-9-book/)
11. [대여 관련](/posts/project-betty-10-rental/)
12. [오프라인 관련](/posts/project-betty-11-offline/)
13. [about ajax](/posts/project-betty-12-ajax/)
14. [팀원 교육 - ajax](/posts/project-betty-13-edu-ajax/)
15. [DTO, Form, VO, Entity ?](/posts/project-betty-14-object/)
16. [후기](/posts/project-betty-15-review/)

<hr/>
<br/>

도서 목록 출력과 상세 검색에 관한 내용이다.  
코드가 너무 많아졌는데 최대한 줄이려 노력했다...  

<br/>
<hr/>

## 검색했을 때의 url 

**도서 목록 화면에서의 검색**

![Desktop View](/assets/img/betty/book/booklist.png)

도서 목록 페이지에서  
장르를 지정하지 않고 여행을 검색한 뒤 5페이지로 가서 
특정 도서 상세글로 이동하였을 떄의 url 이다.  

`"http://localhost:8080/betty/books/9791190073158?page=5&perPageNum=8&searchOption=title&searchText=%EC%97%AC%ED%96%89&auth=&pub=&pubDate=&genre="`

<br/>

**Home 화면에서의 상세 검색**

![Desktop View](/assets/img/betty/book/searchdetail.png)

Home 화면의 상세 검색을 수행하고 특정 도서 상세글로   
이동하였을 때의 url 이다.  

`http://localhost:8080/betty/books/9788932916804?page=1&perPageNum=8&searchOption=title&searchText=%EB%8F%88%ED%82%A4%ED%98%B8%ED%85%8C&auth=%EB%AF%B8%EA%B2%94&pub=%EC%97%B4%EB%A6%B0&pubDate=2013-05-29&genre`

검색 조건은 차이나지만, 검색을 위한 객체는 하나로 사용하기에  
url 에 불필요한 텍스트가 많이 나타난다.    
분리하려면 도서 목록 페이지에 변화가 생기거나,  
상세 검색을 위한 modal 을 따로 만드는 작업이 필요할 것 같아  
하나로 놔두기로 했다.  

<br/>
<hr/>

## 도서 목록을 가져오기 위한 객체
<br/>

```java
public class BookCriteria extends Criteria {
	
	{ this.setPerPageNum(8); }
	
	private String genre;
	private String searchOption;
	private String searchText;
	private String auth;
	private String pub;
	private String pubDate;
	
	
	public BookCriteria(int page, int perPageNum, 
			String genre, String searchOption, String searchText) {
		super(page, perPageNum);
		this.genre = genre;
		this.searchOption = searchOption;
		this.searchText = searchText;
	}
	public BookCriteria(int page, int perPageNum, 
			String searchOption, String searchText, String pub, String pubDate) {
		super(page, perPageNum);
		this.searchOption = searchOption;
		this.searchText = searchText;
		this.pub = pub;
		this.pubDate = pubDate;
		this.auth = auth;
	}
	
}
```

검색 조건을 필드에 저장하여 동적 쿼리에 쓰이는 객체다.  
부모 클래스인 **"Criteria"** 에는 현재 페이지와 페이지 당 출력 개수를 위한 필드가 존재한다.  

<br/>
<hr/>

## 페이지네이션을 위한 객체
<br/>

```java
public class BookPageMaker extends PageMaker{
	
	public BookPageMaker(BookCriteria cri, int count) {
		super(cri,count);
	}
	
	@Override
	public String makeQuery(int page) {
		BookCriteria bcr = (BookCriteria)super.getCri();
		UriComponents uri = UriComponentsBuilder.newInstance()
				.queryParam("page", page)
				.queryParam("perPageNum", bcr.getPerPageNum())
				.queryParam("searchOption",bcr.getSearchOption())
				.queryParam("searchText",bcr.getSearchText())
				.queryParam("auth",bcr.getAuth())
				.queryParam("pub", bcr.getPub())
				.queryParam("pubDate", bcr.getPubDate())
				.queryParam("genre",bcr.getGenre())
				.build();
		return uri.toUriString();
	}
}
```

쿼리 파라미터를 만들어주는 메소드이다.  

<br/>

**부모 클래스인 PageMaker 의 연산 메소드**

```java
public void calcPaging() {
		
		endPage = (int)Math.ceil(cri.getPage()/(double)displayPageNum)*displayPageNum;
		
		startPage = (endPage - displayPageNum)+1;
		
		maxPage = (int)(Math.ceil(totalCount/(double)cri.getPerPageNum()));
		
		if(endPage > maxPage)endPage = maxPage;
		
		first = startPage > 1 ? true : false;
		
		last = (cri.getPage() < maxPage) ? true : false;

		prev = (endPage - displayPageNum <= 0) ? false : true;
		
		next = (endPage == maxPage) ? false : true;
		
	}

```

페이지네이션 연산을 위한 메소드이다.  
"1 2 3 4 5" 같은 페이지 번호를 출력하는 연산과  
"6 7 8 9 10" 과 같이 다음 페이지로의 이동 버튼을 출력하는데 사용된다.  

<br/>
<hr/>

## BookService
<br/>

```java
public class BookService {
	
	public void bookList(BookCriteria cri, Model model) {
		int totalCount = bookRepository.findAllCount(cri);
		BookPageMaker pageMaker = new BookPageMaker(cri, totalCount);
		List<Book> list = bookRepository.findAll(cri);
		model.addAttribute("list", list);
		model.addAttribute("pm",pageMaker);
	}
}
```

검색을위한 BookCriteria 객체를 이용하여,  
도서 테이블의 총 행 개수를 가져와 페이지네이션을 위한 객체를 생성한다.  

다시 BookCriteria 객체를 이용하여 도서 목록을 가져온다.  
model 에 함께 담아 jsp 페이지에서 해석할 수 있도록 만든다.  

<br/>
<hr/>

## BookProvider
<br/>

```java
public class BookProvider {

	public String findAll(BookCriteria cri) {
		SQL sql = new SQL();
		sql.SELECT("*").FROM(BOOK_TBL);
		bookListResolver(sql, cri);

		if (cri != null) {
			sql.OFFSET("#{startRow}");
			sql.LIMIT("#{perPageNum}");
		}

		return sql.toString();
	}

	public String findAllCount(BookCriteria cri) {
		SQL sql = new SQL();
		sql.SELECT("count(*)").FROM(BOOK_TBL);
		bookListResolver(sql, cri);
		return sql.toString();
	}

    private void bookListResolver(SQL sql, BookCriteria cri) {
		if (cri.getSearchText() != null) {
			switch(cri.getSearchOption()) {
                case "title":
                    sql.WHERE("title LIKE CONCAT('%',#{searchText},'%')");
                    break;
                case "auth":
                    sql.WHERE("auth LIKE CONCAT('%',#{searchText},'%')");
                    break;
                case "intro":
                    sql.WHERE("intro LIKE CONCAT('%',#{searchText},'%')");
                    break;				
			}
		}

		if (cri.getGenre() != null && cri.getGenre() != "") {
			sql.WHERE("genre = #{genre}");
		}

		if (cri.getAuth() != null && cri.getAuth() != "") {
			sql.WHERE("auth LIKE CONCAT('%',#{auth},'%')");
		}
		
		if (cri.getPub() != null && cri.getPub() != "") {
			sql.WHERE("pub LIKE CONCAT('%',#{pub},'%')");
		}			
		
		if (cri.getPubDate() != null && !cri.getPubDate().trim().equals("")) { 
			Timestamp pubDate = Timestamp.valueOf(
                                    cri.getPubDate() + " 00:00:00"
                                );
			String date = new SimpleDateFormat("yyyy-MM-dd").format(pubDate);
			sql.WHERE("pub_date >= '" + date + "'");
		}
		
	}
}
```

검색 객체를 사용한 동적 쿼리다.  
findAll, findAllCount 에 들어갈 조건이 동일하기 떄문에  
코드 조각을 하나의 메소드로 옮겼다.  
출판일인 pubDate 는 사실 타임스탬프 변환 과정이 무의미하고  
문자열 그대로 넣게 해도 상관없다.  

<br/>
<hr/>

## 도서 목록 출력부
<br/>

```jsp
<c:choose>
				
    <c:when test="${!empty list}">
        <div class="row row-cols-md-4">
            <c:forEach var="board" items="${list}">
                <div class="col mb-3">
                    
                    <div class="card">
                        <img src=
                            "${path}/resources/img/book/origin/${board.code}.jpg" 
                            onclick=
                            "location.href=
                            '${path}/books/${board.code}${pm.makeQuery(pm.cri.page)}'" 
                            class="card-img-top">
                        <div class="card-body">
                        <h5 class="card-title">${board.title}</h5>
                        </div>
                    </div>
                    
                    </div>
            </c:forEach>
        </div>
    </c:when>
    
    <c:otherwise>
        <h1 style="height: 600px">도서가 존재하지 않습니다.</h1>
    </c:otherwise>
    
</c:choose>
```

간단히 이미지와 도서 제목을 부트스트랩을 이용하여 하나의 카드로 만든다.  
부트스트랩 row 클래스를 사용하여 flex 를 적용하고 4개의 열이 존재하도록 했다.  
BookCriteria 에서 페이지 당 출력 개수를 8개로 제한 했으니  
도서 목록은 한 행이 4개씩 두 행 까지만 나타난다.  


<br/>
<hr/>

## 검색 폼
<br/>

```jsp
<form id="searchForm">
    <div class="form-row input-group mb-3">
        <input name="genre" id="hiddenGenre" hidden/>
        <input name="page" id="hiddenPage" hidden/>
        <div class="input-group-prepend">
            <select name="searchOption" id="searchOption" >
                <option value="title">제목</option>
                <option value="auth">작성자</option>
                <option value="intro">내용</option>
            </select>
        </div>
        <input name="searchText" class="form-control">
        <div class="input-group-append">
            <button type="submit" class="btn btn-primary">검색하기</button>
        </div>
    </div>
</form>
```

장르와 현재 페이지 정보는 form 에 묶으려면 form 영역이 너무 넓어졌다.  
hidden 속성을 적용하여 선택된 장르와 페이지를 form 에 함께 넣었다.  

<br/>
<hr/>

## 검색 폼에 장르, 페이지 정보 넣기
<br/>

```jsx
<script>
	let genreBtn = $('#hiddenGenre');
	let hiddenPage = $('#hiddenPage');
	
	$(function(){
		
		if(hiddenPage.val() == ''){
			hiddenPage.val(1);
		}
		
		if('${cri.genre}' != ''){
			let genre = $("input:radio[name=genre][value='${cri.genre}']");
			genre.parent().addClass('focus');
			genreBtn.val(genre.val());
		} else {
			let clicked = $("input:radio[name=genre][value='']");
			clicked.parent().addClass('focus');
		}
	})
	
	$('input:radio[name=genre]').on('click', function(ev){
		let genreValue = this.value;
		genreBtn.val(genreValue);
		location.href=`${path}/books?genre=\${genreValue}`;
	})
</script>
```

장르와 페이지 정보를 위의 검색 폼에 넣는 과정이다.  
라디오 버튼 중 name 이 "genre" 일 때, 값을 가져와 hidden input 태그 내부에 값을 전달한다.  

페이지가 로드 됐을 때 BookCriteria 정보를 이용하여  
해당 장르를 선택하는 효과를 주고, 다시 hidden input 태그 내부에 값을 전달한다.  