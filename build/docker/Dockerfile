# Tomcat 9 + JDK 8
FROM tomcat:9.0-jdk8-temurin-jammy

# (선택) 기본 예제 webapp 제거
RUN rm -rf /usr/local/tomcat/webapps/*

# ===== 기본 환경변수(필요 시 docker run -e 로 덮어쓰기) =====
ENV SPRING_PROFILES_ACTIVE=default \
    APPLICATION_ROLE=app \
    POSTGRESQL_FILEPATH=/config/postgresql.properties

# ===== WAR 배포 =====
# 컨텍스트를 / 로 쓰려면 ROOT.war 로 배포
COPY ./egov01-1.0.0-SNAPSHOT.war /usr/local/tomcat/webapps/ROOT.war

# ===== JVM 옵션 주입 (Tomcat 기동 시 setenv.sh 읽음) =====
RUN printf '%s\n' \
  '#!/bin/sh' \
  'export CATALINA_OPTS="$CATALINA_OPTS -Dspring.profiles.active=${SPRING_PROFILES_ACTIVE} -Dapplication.role=${APPLICATION_ROLE} -Dpostgresql.filepath=${POSTGRESQL_FILEPATH}"' \
  > /usr/local/tomcat/bin/setenv.sh \
  && chmod +x /usr/local/tomcat/bin/setenv.sh

EXPOSE 8080
CMD ["catalina.sh", "run"]
