```sh
docker container run -d \
                     -p 8080:8080 \
                     -p 50000:50000 \
                     -v jenkins_home:/var/jenkins_home \
                     --name jenkins-server \
                     --restart=on-failure \
                     jenkins/jenkins:lts-jdk11
```

```sh
docker exec -it jenkins-server cat /var/jenkins_home/secrets/initialAdminPassword
```

## Build Maven Project

1. Maven Intergration plugin 플러그인 설치
2. `clean compile package`
3. `https://github.com/joneconsulting/cicd-web-project`
4. deploy to container 플러그인 설치
5. `docker inspect network <network-name>` 으로 tomcat 컨테이너 IP 주소 획득 (만약 호스트 머신에 tomcat에 배포해야 한다면, host 머신의 `ifconfig` 명령어를 통해 `en0` 의 IP 주소를 획득해야한다.)
6. Maven project item 추가
7. ![image-20240101164524544](/Users/rosang/Library/Application Support/typora-user-images/image-20240101164524544.png)
8. ![image-20240101164555203](/Users/rosang/Library/Application Support/typora-user-images/image-20240101164555203.png)



## Deploy Maven Project on Tomcat

```sh
docker run -it \
           --rm \
           --name tomcat-server \
           -v /Users/rosang/Documents/jenkins-practice/context.xml:/usr/local/tomcat/webapps.dist/manager/META-INF/context.xml \
           -v /Users/rosang/Documents/jenkins-practice/tomcat-users.xml:/usr/local/tomcat/conf/tomcat-users.xml \
           -p 8888:8080 tomcat:9
```

```sh
docker container exec tomcat-server mv webapps webapps2 && cp -r webapps.dist webapps
```

### context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<Context antiResourceLocking="false" privileged="true" >
  <CookieProcessor className="org.apache.tomcat.util.http.Rfc6265CookieProcessor"
                   sameSiteCookies="strict" />
  <!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```

### tomcat-users.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
<!--
  By default, no user is included in the "manager-gui" role required
  to operate the "/manager/html" web application.  If you wish to use this app,
  you must define such a user - the username and password are arbitrary.

  Built-in Tomcat manager roles:
    - manager-gui    - allows access to the HTML GUI and the status pages
    - manager-script - allows access to the HTTP API and the status pages
    - manager-jmx    - allows access to the JMX proxy and the status pages
    - manager-status - allows access to the status pages only

  The users below are wrapped in a comment and are therefore ignored. If you
  wish to configure one or more of these users for use with the manager web
  application, do not forget to remove the <!.. ..> that surrounds them. You
  will also need to set the passwords to something appropriate.
-->
  <user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
  <user username="deployer" password="deployer" roles="manager-script"/>
  <user username="tomcat" password="tomcat" roles="manager-gui"/>
<!--
  The sample user and role entries below are intended for use with the
  examples web application. They are wrapped in a comment and thus are ignored
  when reading this file. If you wish to configure these users for use with the
  examples web application, do not forget to remove the <!.. ..> that surrounds
  them. You will also need to set the passwords to something appropriate.
-->
<!--
  <role rolename="tomcat"/>
  <role rolename="role1"/>
  <user username="tomcat" password="<must-be-changed>" roles="tomcat"/>
  <user username="both" password="<must-be-changed>" roles="tomcat,role1"/>
  <user username="role1" password="<must-be-changed>" roles="role1"/>
-->
</tomcat-users>
```

## Deploy Maven Project on Tomcat Using PollSCM

* Poll SCM uses cron job(job scheduler on linux) and build project when commits changed.

![image-20240101171437533](/Users/rosang/Library/Application Support/typora-user-images/image-20240101171437533.png)

## Deploy Maven Project on Tomcat Using SSH

1. `publish over ssh` 플러그인 설치
2. Manage Jenkins > System > Publish Over SSH
    ![image-20240102063318251](/Users/rosang/Library/Application Support/typora-user-images/image-20240102063318251.png)

3. 
