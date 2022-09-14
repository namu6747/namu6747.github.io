---
title: 9. 권한 인터셉터
author: namu6747
date: 2022-09-06 09:08:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

## &nbsp;&nbsp;&nbsp; index
1. [서론](/posts/project-betty-1/)
2. [기획 및 명세](/posts/project-betty-2-concept/)
3. [패키지 트리](/posts/project-betty-3-package-tree/)
4. [프로젝트 환경](/posts/project-betty-4-config/)
5. [메시지와 국제화](/posts/project-betty-5-message/)
6. [예외 다루기](/posts/project-betty-6-exception/)
7. [검증](/posts/project-betty-7-validation/)
8. [계정 관련](/posts/project-betty-8-sign/)
9. [권한 인터셉터](/posts/project-betty-9-interceptor/)
10. [도서 관련](/posts/project-betty-10-book/)
11. [대여 관련](/posts/project-betty-11-rental/)
12. [오프라인 관련](/posts/project-betty-12-offline/)
13. [about ajax](/posts/project-betty-13-ajax/)
14. [팀원 교육 - ajax](/posts/project-betty-14-edu-ajax/)
15. [DTO, Form, VO, Entity ?](/posts/project-betty-15-object/)
16. [후기](/posts/project-betty-16-review/)

<br/>
<hr/>
<br/>

머나먼 여정을 지나 드디어 권한 인터셉터로 왔다.  
시큐리티를 등한시한 내게 이 인터셉터는 차선책이었다.

<br/>
<hr/>


```java

public class AuthInterceptor implements HandlerInterceptor{

	@Override
	public boolean preHandle(
                    HttpServletRequest request, 
                    HttpServletResponse response, 
                    Object handler
                    )
			throws Exception {
		
		String uri = request.getRequestURI();
		String path = request.getServletContext().getContextPath();
		HttpSession session = request.getSession(false);
		Member user = (Member)session.getAttribute(SessionConst.USER);
		
		
		if(session == null || user == null) {
			response.sendRedirect(path+"/sign/in?redirectURL="+uri);
			return false;
		}
		
		int right = user.getRights();
		String area = uri.substring(path.length());
		
		
		boolean memberPath = area.startsWith(PathConst.MEMBERS);  
		boolean staffPath = area.startsWith(PathConst.STAFF);
		boolean adminPath = area.startsWith(PathConst.ADMIN);  
		boolean offlinePath = area.startsWith(PathConst.OFFLINE);
		boolean isMember = (right == 0);
		boolean isStaff = (right == 2);
		boolean isAdmin = (right == 3);
		
		if(offlinePath || memberPath && isMember) {
			String pathVariable = uri.split("/")[3];
			if(!user.equalsId(pathVariable)) {
				request.setAttribute("message", "요청 실패");
				response.sendRedirect(path);
				return false;
			}
		}
		
		if(memberPath && !isMember) {
			request.setAttribute("message", "일반회원만 접근할 수 있습니다.");
			response.sendRedirect(path);
			return false;
		} else if (staffPath && !isStaff) {
			request.setAttribute("message", "직원회원만 접근할 수 있습니다.");
			response.sendRedirect(path);
			return false;
		} else if(offlinePath && !isMember) {
			request.setAttribute("message", "일반회원만 접근할 수 있습니다.");
			response.sendRedirect(path);
			return false;
		} else if(adminPath && !isAdmin) {
			request.setAttribute("message", "관리자만 접근할 수 있습니다.");
			response.sendRedirect(path);
			return false;
		}
			
		request.setAttribute("nav", uri.split("/")[2]);
		return true;
	}

}

```

경로에 따라, 권한에 따라 boolean 을 많이도 생성했다.  
분명 훨씬 나은 방법이 존재하겠지만 현재까지도 떠오르지 않는다.  

권한에 따른 접근을 제어하기 위한 인터셉터다.   
만드는 데 큰 어려움은 없었다.  
애초에 경로를 나눠 놓을 때 권한 별 제어를 할 수 있도록 구성했었다.  

uri 와 contextPath 를 활용하는 부분은  
JSP & 서블릿 구조에서 프론트 컨트롤러의 작업과 비슷하다.  

우선 권한 별 접근 제한이기에 세션 자체가 없거나 (로그인을 안 했거나)  
세션이 존재하더라도 세션 Member 객체가 null 일 때 로그인 화면으로 보낸다.  
이때 이전의 요청 경로를 쿼리파라미터에 함께 넘겨서  
로그인 성공했을 때 돌아갈 수 있도록 지정했다.  

이 부분에서 한 가지 궁금증이 생길 수 있는데,  

**악의적으로 admin 경로로 진입한 뒤 일반 회원으로 로그인 한다면?**  

걱정하지말자.  
**"권한에 따른 접근 제어"** 이게 본질이다.  
접근 제어가 필요한 경로엔 이 인터셉터가 매번 적용되어  
위와 같은 상황은 로그인 성공 시 Home 화면으로 이동된다.

<br/>

Betty 의 member 영역에 대한 경로는 아래와 같다.  
`"http://localhost:8080/betty/members"`  
members, staff, admin, offline 중 해당하는 경로가 있을 때 
검증이 시작된다. 

<br/>
<hr/>
<br/>

맨 마지막에 있는 **"nav"**는 좌측 네비게이터를 동적으로 구성하기 위한 장치다.  

인증, 인가 검사를 거치고 나면  
request 영역 객체에 해당 카테고리의 nav 정보를 담는다.  

![Desktop View](/assets/img/betty/auth/nav.png)

jsp:include 태그를 이용하여 contextPath 와 Session Member 객체를 넘긴다.  

동적 include 를 사용할 때 변수를 그대로 이용할 수 없어 param 태그로 넘겨줘야 된다.  

include file 은 동일한 Servlet 내에서 렌더링되지만  
경로 내에 el 을 사용할 수 없다.    

jsp include 는 각자 독립된 Servlet 에서 렌더링된 뒤 포함되는 형식이고, el 을 사용할 수 있다.  