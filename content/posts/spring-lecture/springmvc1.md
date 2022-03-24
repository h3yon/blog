---
title: "스프링 mvc 1편 정리"
date: 2021-12-23T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - spring
  - mvc
---


스프링 mvc 1편을 듣고 정리한 내용입니다.

# 1 Servlet이해하기

서블릿을 자동 등록하는 경우,
`@ServletComponentScan`어노테이션을 사용한다.

예제를 보면서 이해해보자
```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("request = " + request);
        System.out.println("response = " + response);

        String username = request.getParameter("username");
        System.out.println("username = " + username);

        response.setContentType("text/plain");
        response.setCharacterEncoding("utf-8");
        response.getWriter().write("hello " + username);
    }
}
```
서블릿을 보면 이렇게 request, response로 구성되어 있으며,  
파라미터를 추출해낼 수 있고,  
response의 contentType, CharacterEncoding을 설정 가능하며  
write로 원하는 내용을 뿌려줄 수 있다  

urlPatterns로 원하는 uri를 설정 가능한 걸 볼 수 있다  

또, 아래처럼 이렇게 원하는 정보를 뽑아낼 수 있다  

```java
System.out.println("request.getMethod() = " + request.getMethod()); //GET
System.out.println("request.getProtocal() = " + request.getProtocol()); //HTTP/1.1
System.out.println("request.getScheme() = " + request.getScheme()); //http
// http://localhost:8080/request-header
System.out.println("request.getRequestURL() = " + request.getRequestURL());
// /request-test
System.out.println("request.getRequestURI() = " + request.getRequestURI());
//username=hi
System.out.println("request.getQueryString() = " + request.getQueryString());
System.out.println("request.isSecure() = " + request.isSecure()); //https사용 유무
```

header정보를 **아래처럼 iterator로 뽑아내**는 등 다양한 정보를 확인할 수 있다!!

```java
request.getHeaderNames().asIterator().forEachRemaining(headerName -> System.out.println("headerName = " + headerName));
```

## 요청 body의 json 뽑아서 객체로 만들기!

```java
@WebServlet(name = "requestBodyJsonServelt", urlPatterns = "/request-body-json")
public class RequestBodyJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletInputStream inputStream = req.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        System.out.println("messageBody = " + messageBody);

        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);

        System.out.println("helloData.username = " + helloData.getUsername());
        resp.getWriter().write("ok");
    }
}
```

이렇게 ServletInputStream을 뽑아낸 후,  
copyToString으로 body를 뽑아낸다.  
ObjectMapper를 사용해서 json 내용을 객체로 변환해서 볼 수 있다  

> 강의 중, 동시성 문제 고려 필요할 때에는 ConcurrentHashMap, AtomicLong 사용 고려 필요

+result에 2개의 정보가 포함되어있는지 테스트할 때

```java
assertThat(result).contains(member1, member2);
```

# JSP?

jsp를 보면서 이해해보자

```java
<%@ page import="java.util.List" %>
<%@ page import="hello.servlet.domain.member.MemberRepository" %>
<%@ page import="hello.servlet.domain.member.Member" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
    MemberRepository memberRepository = MemberRepository.getInstance();
    List<Member> members = memberRepository.findAll();
%>
<table>
    <thead>
    <th>id</th>
    <th>username</th>
    <th>age</th>
    </thead>
    <tbody>
    <%
        for (Member member : members) {
            out.write("    <tr>");
            out.write("      <td>" + member.getId() + "</td >");
            out.write("      <td>" + member.getUsername() + "</td >");
            out.write("      <td>" + member.getAge() + "</td >");
            out.write("    </tr>");
        }
    %>
    </tbody>
</table>
```

이렇게 위에 자바에서 사용되는 코드가 있고,  
아래에 html 관련 코드가 있다   
+html, head 이런 태그는 생략!  

딱 보기만 해도 jsp를 사용하는 것에 대해 고민할 것이란 걸 알 수 있다!

# MVC

> 강의 들으면서 하나 더 알게 된 건, "/WEB-INF/~"에 있는 부분은, 컨트롤러에서 뷰로 이동할 때 사용한다는 것이었다

```java
//서블릿 관련 코드
request.setAttribute("member", member);

String viewPath = "/WEB-INF/views/members.jsp";
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```

requestDispatcher에 viewPath를 넣고  
`dispatcher.forward()`로 화면을 뿌려준단 걸 알 수 있다  
-> 여기서 중복이 계속 발생할 수 있다(viewPath, forward 부분!)
또, setAttribute로 model에 데이터를 보관할 수 있다  

jsp의 경우 아래처럼 forEach로 members에 대해  
forEach로 item을 뽑아오는 걸 알 수 있다

```html
<table>
    <thead>
    <th>id</th>
    <th>username</th>
    <th>age</th>
    </thead>
    <tbody>
    <c:forEach var="item" items="${members}">
        <tr>
            <td>${item.id}</td>
            <td>${item.username}</td>
            <td>${item.age}</td>
        </tr>
    </c:forEach>
    </tbody>
</table>
```

# 중복 개선을 위한 ServletV1

ServletV1에서 uri에 맞게 Controller를 매핑해준다.  
HttpServlet을 구현한 것이다보니까 service로 process를 해준다.

```java
@WebServlet(name = "ServletV1", urlPatterns = "/controller/v1/*")
public class ServletV1 extends HttpServlet {

    private Map<String, ControllerV1> controllerV1Map = new HashMap<>();

    public ServletV1() {
        controllerV1Map.put("/controller/v1/members/new-form", new MemberFormControllerV1());
        controllerV1Map.put("/controller/v1/members/save", new MemberSaveControllerV1());
        controllerV1Map.put("/controller/v1/members", new MemberListControllerV1());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String requestURI = request.getRequestURI();
        ControllerV1 controller = controllerV1Map.get(requestURI);
        controller.process(request, response); //아까 계속 중복되었던 부분(requestDispatcher, forward)
    }
}
```

위의 process는 controller마다 구현해주는 부분으로,  
아까 중복되었던 부분을 포함한다

```java
public class MemberSaveControllerV1 implements ControllerV1 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //로직 (생략)
        String viewPath = "/WEB-INF/views/save-result.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

그래도 컨트롤러마다 다 위처럼 중복된 부분을 가져야함을 알 수 있다


# 중복 개선을 위한 ServletV2

그래서 뷰라는 클래스를 만들고,  
중복되는 부분을 render에 담는다

```java
public class MyView {

    private String viewPath;

    public MyView(String viewPath) {
        this.viewPath = viewPath;
    }

    public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

서블릿을 보면 아까와 달라보이지 않지만,  
process 밑에 렌더가 추가됨을 알 수 있다

```java
@WebServlet(name = "ServletV2", urlPatterns = "/controller/v2/*")
public class ServletV2 extends HttpServlet {

    private Map<String, ControllerV2> controllerV2Map = new HashMap<>();

    public ServletV2() {
        controllerV2Map.put("/controller/v2/members/new-form", new MemberFormControllerV2());
        controllerV2Map.put("/controller/v2/members/save", new MemberSaveControllerV2());
        controllerV2Map.put("/controller/v2/members", new MemberListControllerV2());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String requestURI = request.getRequestURI();

        ControllerV2 controller = controllerV2Map.get(requestURI);
        MyView view = controller.process(request, response);
        view.render(request, response);
    }
}
```

그럼 컨트롤러는 이렇게 해당 경로를 리턴해줌을 알 수 있다

```java
public class MemberFormControllerV2 implements ControllerV2 {

    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        return new MyView("/WEB-INF/views/new-form.jsp");
    }
}
```

괜찮은 것 같은데,  
서블릿 종속성이 여전히 존재한다는 단점이 있다

# 서블릿 종속성 개선을 위한 ServletV3

아까 MyView와 달리,  
viewName에다가 model이 있다.

```java
@Getter @Setter
public class ModelView {

    private String viewName;
    private Map<String, Object> model = new HashMap<>();

    public ModelView(String viewName) {
        this.viewName = viewName;
    }
}
```

그럼 MyView에도 model 관련 작업을 해준다

```java
   public void render(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        modelToRequestAttribute(model, request);
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }

    private void modelToRequestAttribute(Map<String, Object> model, HttpServletRequest request) {
        //        model.forEach(request::setAttribute);
        model.forEach((key, value) -> request.setAttribute(key, value));
    }
```

그럼 ServletV3은 어떻게 바뀔까?

```java
@WebServlet(name = "ServletV3", urlPatterns = "/controller/v3/*")
public class FrontControllerServletV3 extends HttpServlet {

    private Map<String, ControllerV3> controllerV3Map = new HashMap<>();

    public FrontControllerServletV3() {
        controllerV3Map.put("/controller/v3/members/new-form", new MemberFormControllerV3());
        controllerV3Map.put("/controller/v3/members/save", new MemberSaveControllerV3());
        controllerV3Map.put("/controller/v3/members", new MemberListControllerV3());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String requestURI = request.getRequestURI();
        ControllerV3 controller = controllerV3Map.get(requestURI);

        Map<String, String> paramMap = createParamMap(request);
        ModelView mv = controller.process(paramMap);
        String viewName = mv.getViewName(); //논리 이름
        MyView view = viewResolver(viewName);

        view.render(mv.getModel(), request, response);
    }

    private MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }

    private Map<String, String> createParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator().forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
}
```

아래 부분이 상당히 길어졌다  
내용을 보면, paramMap을 가져오고  
process한 부분에서 viewName을 가져와서 그 논리 이름에 대한
view를 리턴해줌을 알 수 있다  

Controller는 무슨 일을 할까?

```java
public class MemberSaveControllerV3 implements ControllerV3 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public ModelView process(Map<String, String> paramMap) {
        String username = paramMap.get("username");
        int age = Integer.parseInt(paramMap.get("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        ModelView modelView = new ModelView("save-result");
        modelView.getModel().put("member", member);
        return modelView;
    }
}
```

paramMap에서 원하는 정보를 get하고 로직 처리한 후에,  
modelView에 정보를 넣어주고 리턴함을 알 수 있다  

이걸 어떻게 개선할 수 있을까?

# ServletV4




```java
@WebServlet(name = "ServletV4", urlPatterns = "/controller/v4/*")
public class FrontControllerServletV4 extends HttpServlet {

    private Map<String, ControllerV4> controllerV4Map = new HashMap<>();

    public FrontControllerServletV4() {
        controllerV4Map.put("/controller/v4/members/new-form", new MemberFormControllerV4());
        controllerV4Map.put("/controller/v4/members/save", new MemberSaveControllerV4());
        controllerV4Map.put("/controller/v4/members", new MemberListControllerV4());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String requestURI = request.getRequestURI();
        ControllerV4 controller = controllerV4Map.get(requestURI);

        Map<String, String> paramMap = createParamMap(request);
        Map<String, Object> model = new HashMap<>();
        String viewName = controller.process(paramMap, model);

        MyView view = viewResolver(viewName);
        view.render(model, request, response);
    }

    private MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }

    private Map<String, String> createParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator().forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
}
```