# Spring Boot starter로 프로젝트 시작하기

#### 프로젝트 전반적인 설정

* SpringBoot 3.x 버전 이상에서는 최소한의 Java 버전이 17이상이어야 합니다.

![프로젝트 설정](img/spring/3.png)


#### 종속성(라이브러리) 추가

![종속성 설정](img/spring/4.png)

1. Lombok
    * Lombok은 자바 클래스에서 반복적으로 작성해야 하는 코드 (예: Getter, Setter, toString, equals, hashCode 등)를 어노테이션을 통해 자동으로 생성해주는 라이브러리입니다. 
    * Lombok을 사용하면 코드의 가독성을 높히고, 반복하여 작성되어지는 코드를 줄일 수 있습니다.
    * @Getter, @Setter, @ToString, @EqualAndHashCode, @NoArgsconstructor, @AllArgsConstructor등의 어노테이션을 제공합니다. 

2. Spring Configuration Processor
    * Spring Configuration Processor는 외부 설정 파일(application.properties, application.yml 등)에 정의된 속성을 자바 클래스에 매핑할 때 개발자 경험을 향상시키는 라이브러리입니다. 
    * 이 라이브러리를 사용하면 외부 설정 파일의 속성에 대한 자동 완성, 문서화, 유효성 검사 등의 기능을 사용할 수 있습니다. 
    * @ConfigurationProperies 어노테이션과 함께 사용되어 외부 설정 파일의 속성을 자바 클래스에 매핑할 수 있습니다. 

3. Spring Web
    * Spring Web은 웹 애플리케이션 개발을 위한 핵심 기능을 제공하는 라이브러리입니다. 
    * RESTful 웹 서비스, MVC 패턴 기반 웹 애플리케이션 개발, HTTP 요청 처리, 응답 생성 등의 기능을 포함합니다. 
    * @RestController, @RequestMapping, @GetMapping, @PostMapping 등의 어노테이션을 제공하여 웹 요청을 처리하고 응답을 생성할 수 있습니다. 

4. Thymeleaf
    * Thymeleaf는 자바 기반 웹 애플리케이션에서 사용되는 템플릿 엔진입니다. 
    * HTML 템플릿에 Thymeleaf의 특수한 속성과 식을 사용하여 동적으로 데이터를 바인딩하고 화면을 구성할 수 있습니다. 
    * 서버 사이드 렌더링을 지원하며, 자연스러운 템플릿 작성과 유지보수성을 제공합니다. 
    * Spring Boot에서는 Thymeleaf를 기본 템플릿 엔진으로 사용할 수 있으며, 별도의 설정 없이 바로 사용할 수 있습니다. 

5. Spring Boot DevTools
    * Spring Boot DevTools는 개발자 도구 모음으로, 애플리케이션 개발 시 생산성을 향상시키는 기능을 제공합니다. 
    * 코드 변경 시 자동으로 애플리케이션을 다시 시작하거나 브라우저를 리프레시하는 기능(Hot Swapping)을 제공합니다.
    * 정적 자원(HTML, CSS, JavaScript 등)의 변경 사항을 즉시 반영하여 개발 시 병경 사항을 빠르게 확인할 수 있습니다. 