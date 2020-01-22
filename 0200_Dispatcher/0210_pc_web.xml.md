passion-fruit/apps/pc/web/WEB-INF/web.xml 분석

# servlet-mapping
```
<servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>*.nhn</url-pattern>
</servlet-mapping>
```
- `.nhn` 으로 끝나는 모든 리퀘스트를 매핑

# dispatcherServlet
```
<servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/spring/mvc-config.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
```
- /WEB-INF/spring/mvc-config.xml 파일을 `.nhn` 매핑의 dispatcherServlet 설정으로 지정
