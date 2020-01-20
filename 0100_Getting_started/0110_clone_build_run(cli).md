# clone, build, run (command line interface)
IDE 없이 oss에서 passtion-fruit 리포를 클론한 뒤 빌드, 톰캣 실행까지

# 가이드 환경
- apps/pc 빌드만 가이드
- MAC OSX Catalina
- IntelliJ IDEA 2019.1
- [Homebrew](https://brew.sh/index_ko)를 사용한 설치
    - gradle
    - tomcat@8
        - 버전 8.5.50
    - mongoDB
- 본 가이드에서 사용하는 경로
    - MAC OS 계정
        - user
    - passion-fruit 리포 클론 경로
        - `/Users/user/work/passion-fruit`
    - Tomcat 설치 경로
        - `/usr/local/Cellar/tomcat@8/8.5.50/libexec`

# 리포 클론
1. git 클론
    ```
    $ cd /Users/user/work
    $ git clone https://oss.navercorp.com/shopping/passion-fruit.git

    Cloning into 'passion-fruit'...
    Username for 'https://oss.navercorp.com': [자신의 oss 계정정보 입력]
    Password for 'https://cheolyong.lee@oss.navercorp.com': [자신의 oss 계정정보 입력]
    
    remote: Enumerating objects: 393, done.
    remote: Counting objects: 100% (393/393), done.
    remote: Compressing objects: 100% (209/209), done.
    ...
    ```
1. `release` 브랜치 체크아웃
    ```
    $ cd /Users/user/work/passion-fruit
    $ git checkout release
    
    Branch 'release' set up to track remote branch 'release' from 'origin'.
    Switched to a new branch 'release'
    ```

# gradle 설치 & 빌드
1. Gradle 설치 ([Homebrew](https://brew.sh/index_ko) 사용)
    ```
    $ brew install gradle
    ```
 1. apps/pc 만 빌드
    ```
    $ cd /Users/user/work/passion-fruit/apps/pc 
    $ gradle clean build -x test
    ```
    1. 빌드 결과
        1.  /Users/user/work/passion-fruit/apps/pc/build/libs/pc-0.0.1-SNAPSHOT.war

# Tomcat 설치 및 설정
1. 설치 ([Homebrew](https://brew.sh/index_ko) 사용)
    ```
    $ brew install tomcat@8
    ```
1. Tomcat 기본 포트를 8080에서 80으로 수정
    ```
    $ vi /usr/local/Cellar/tomcat@8/8.5.50/libexec/conf/server.xml 
    ``` 
    1. 다음 행의 8080을 80으로 수정 후 `:wq`
        ```
        <Connector port="8080" protocol="HTTP/1.1"
        ```
1. Tomcat 시작 옵션 추가
    ```
    $ vi /usr/local/Cellar/tomcat@8/8.5.50/libexec/bin/setenv.sh
    ```
    1. 아래 내용 추가 후 `:wq`
        ```
        CATALINA_OPTS=" ${CATALINA_OPTS} -Dshopping.npc.port=7191"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Dshopping.phase=LOCAL"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Xms2g"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Xmx2g"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Dfile.encoding=UTF-8"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Duser.language=ko"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Duser.country=KR"
        ```

# Tomcat 배포
1. pc 앱의 war 파일 복사
    ```
    $ mv /usr/local/Cellar/tomcat@8/8.5.50/libexec/webapps/ROOT /usr/local/Cellar/tomcat@8/8.5.50/libexec/webapps/original_ROOT <----- 최초 1회만 실행
    $ rm -rf /usr/local/Cellar/tomcat@8/8.5.50/libexec/webapps/ROOT
    $ rm /usr/local/Cellar/tomcat@8/8.5.50/libexec/webapps/ROOT.war
    $ cp /Users/user/work/passion-fruit/apps/pc/build/libs/pc-0.0.1-SNAPSHOT.war /usr/local/Cellar/tomcat@8/8.5.50/libexec/webapps/ROOT.war
    ```
1. Tomcat 시작
    ```
    $ /usr/local/Cellar/tomcat@8/8.5.50/bin/catalina run
    ```

# 빌드 & 배포 스크립트
빌드 -> Tomcat 적용 한 번에 하는 스크립트. 모든 app 가능
1. Tomcat 시작 옵션 스크립트 수정
    ```
    $ vi /usr/local/Cellar/tomcat@8/8.5.50/libexec/bin/setenv.sh
    ```
    1. 아래 내용 추가 후 `:wq`
        ```
        # shopping.npc.port setting
        SHOPPING_NPC_PORT=7191
        if [ "$2" = "pc" ] ; then
            SHOPPING_NPC_PORT=7191
        elif [ "$2" = "mobile" ] ; then
            SHOPPING_NPC_PORT=7192
        elif [ "$2" = "api" ] ; then
            SHOPPING_NPC_PORT=7193
        elif [ "$2" = "admin" ] ; then
            SHOPPING_NPC_PORT=7194
        elif [ "$2" = "worker" ] ; then
            SHOPPING_NPC_PORT=7195
        fi
        CATALINA_OPTS=" ${CATALINA_OPTS} -Dshopping.npc.port=${SHOPPING_NPC_PORT}"
        
        # other setting
        CATALINA_OPTS=" ${CATALINA_OPTS} -Dshopping.phase=LOCAL"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Xms2g"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Xmx2g"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Dfile.encoding=UTF-8"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Duser.language=ko"
        CATALINA_OPTS=" ${CATALINA_OPTS} -Duser.country=KR"
        
        # print 
        echo ""
        echo "/usr/local/Cellar/tomcat@8/8.5.50/libexec/bin/setenv.sh >>>>>>>>>>>>>>>>>>>>>> "
        echo "   project = [${2}]"
        echo "   CATALINA_OPTS = ${CATALINA_OPTS}"
        echo ""
        ```
1. 스크립트 생성
    ```
    $ vi /Users/user/work/build_and_run_tomcat.sh
    ```
    1. 내용 작성 후 `:wq`
        ```
        # select project
        SHOPPING_PROJECT="default"
        if [ "$1" = "pc" ] ; then
            SHOPPING_PROJECT="pc"
        elif [ "$1" = "mobile" ] ; then
            SHOPPING_PROJECT="mobile"
        elif [ "$1" = "api" ] ; then
            SHOPPING_PROJECT="api"
        elif [ "$1" = "admin" ] ; then
            SHOPPING_PROJECT="admin"
        elif [ "$1" = "worker" ] ; then
            SHOPPING_PROJECT="worker"
        fi

        #build and run tomcat
        if [ $SHOPPING_PROJECT = "default" ] ; then
            echo "select project in (pc, mobile, api, admin, worker) like below"
            echo ""
            echo "build_and_run_tomcat.sh pc"
            echo ""
        else
            echo ""
            echo "<<<<<<<<<< build and run tomcat [${SHOPPING_PROJECT}] >>>>>>>>>>>>"
            echo ""
            cd /Users/user/work/passion-fruit/apps/$SHOPPING_PROJECT
            gradle clean build -x test
            rm -rf /usr/local/Cellar/tomcat@8/8.5.50/libexec/webapps/ROOT
            rm /usr/local/Cellar/tomcat@8/8.5.50/libexec/webapps/ROOT.war
            cp /Users/user/work/passion-fruit/apps/$SHOPPING_PROJECT/build/libs/$SHOPPING_PROJECT-0.0.1-SNAPSHOT.war /usr/local/Cellar/tomcat@8/8.5.50/libexec/webapps/ROOT.war
            /usr/local/Cellar/tomcat@8/8.5.50/bin/catalina run $SHOPPING_PROJECT
        fi
        ```
1. 권한 부여
    ```
    $ chmod 777 /Users/user/work/build_and_run_tomcat.sh
    ```
1. 실행
    ```
    $ /Users/user/work/build_and_run_tomcat.sh pc
    ```
1. 테스트
    1. http://127.0.0.1/
        1. http://127.0.0.1/search/all.nhn?query=mp3 로 리다이렉트 된다.
