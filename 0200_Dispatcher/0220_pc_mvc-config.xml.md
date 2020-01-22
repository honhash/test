passion-fruit/apps/pc/web/WEB-INF/spring/mvc-config.xml 분석

# import AOP
```
<import resource="classpath:META-INF/application-aop.xml"/>
```
- passion-fruit/apps/pc/src/main/resources/META-INF/application-aop.xml : 비어있다.
- AOP를 사용하지 않음

# `<context:component-scan>`
```
<context:component-scan base-package="com.naver.shopping3" use-default-filters="false">
    <context:include-filter expression="org.springframework.stereotype.Controller" type="annotation"/>
</context:component-scan>
```
- com.naver.shopping3 이하 @Controller 어노테이션 클래스를 컨트롤러로 스캔

# userFactory
```
<!-- request에서 쿠키기반 사용자 정보를 얻어옴 -->
<bean id="userFactory" class="com.naver.shopping3.common.auth.user.ServiceUserFactory"/>
```
- passion-fruit/services/service-front-auth/src/main/java/com/naver/shopping3/common/auth/user/ServiceUserFactory.java
- ServiceUserFactory 클래스는 컴포넌트 스캔 대상인 com.naver.shopping3 밖에 있기 때문에 따로 등록

# interceptors
- [RequestThrottlingInterceptor]
  - passion-fruit/apps/pc/src/main/java/com/naver/shopping3/front/web/utils/RequestThrottlingInterceptor.java
  - IP+NNB 키값으로 최대 요청 한도를 조정
- [UserAgentInterceptor]
  - passion-fruit/libs/lib-webmvc-core/src/main/java/com/naver/shopping3/common/web/layout
  - UA 관련 처리
- [LayoutFrontInterceptor]
  - passion-fruit/apps/pc/src/main/java/com/naver/shopping3/front/layout/LayoutFrontInterceptor.java
  - 설명 없음
- [UserInterceptor]
  - passion-fruit/services/service-front-auth/src/main/java/com/naver/shopping3/common/auth/UserInterceptor.java
  - {@link HttpServletRequest} 로부터 {@link ServiceUser} 생성
    - 유저정보 객체를 생성하는 듯
- [SearchAccessLogInterceptor]
  - passion-fruit/apps/pc/src/main/java/com/naver/shopping3/front/web/search/accesslog/SearchAccessLogInterceptor.java
  - 검색로그 관련 처리인듯
- [NeloLoggingInterceptor]
  - passion-fruit/apps/pc/src/main/java/com/naver/shopping3/front/event/NeloLoggingInterceptor.java
  - NELO(NAVER Error Logging System) 관련 처리
- webContentInterceptor
  - 스프링 MVC 브라우저 캐시 제어 설정

# argument-resolvers
- [UserArgumentResolver]
    - passion-fruit/services/service-front-auth/src/main/java/com/naver/shopping3/common/auth/UserArgumentResolver.java
    - 프런트 사용자 정보를 갖고온다.
        - {@link ServiceUser} 객체를 파라미터로 넣어주기
- [SearchLogInfoBuilderArgumentResolver]
    - passion-fruit/apps/pc/src/main/java/com/naver/shopping3/front/web/search/accesslog/SearchLogInfoBuilderArgumentResolver.java
    - 검색 로깅 정보를 갖고온다.
- [SearchParamArgumentResolver]
    - passion-fruit/apps/pc/src/main/java/com/naver/shopping3/front/web/search/param/SearchParamArgumentResolver.java
    - SearchParam
        - 리퀘스트에 담긴 검색 파라메터를 컨트롤러가 처리하기 전에 정제한다. (중요)

# ViewResolver
- 1순위 : tilesviewResolver
    - 타일 위치 : /WEB-INF/tiles/tiles*.xml (tilesConfigurer 에서 지정)
- 2순위 : InternalResourceViewResolver
    - jsp 파일을 직접 디스패치 할 때 사용하던 레거시 코드인듯
    - jsp 위치 : WEB-INF/jsp/*.jsp

# ExceptionResolver
- [AuthorizationExceptionResolver]
    - passion-fruit/services/service-front-auth/src/main/java/com/naver/shopping3/common/auth/exception/AuthorizationExceptionResolver.java
    - 접근제어 관련 설정 - 노출 가능한 정보를 알려준다.
- SimpleMappingExceptionResolver
    - exceptionMappings : com.naver.shopping3.common.web.acl.AccessControlException
    - [AuthorizationExceptionResolver] 에서 잡아주지 못한 접근제어 처리


