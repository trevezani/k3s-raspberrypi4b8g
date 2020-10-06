FROM arm64v8/adoptopenjdk:11-jdk-hotspot as build
WORKDIR /workspace/app

RUN mkdir -p target

COPY target/*.jar target

RUN mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)


FROM arm64v8/adoptopenjdk:11-jre-hotspot
VOLUME /tmp

ARG DEPENDENCY=/workspace/app/target/dependency
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app

ENV JAVA_OPTS="-XX:+UnlockExperimentalVMOptions -Xms16m -Xmx48m -XX:MaxMetaspaceSize=64m -XX:CompressedClassSpaceSize=8m -Xss256k -Xmn8m -XX:InitialCodeCacheSize=4m -XX:ReservedCodeCacheSize=8m -XX:MaxDirectMemorySize=16m"

USER root

ENTRYPOINT java $JAVA_OPTS -cp app:app/lib/* br.com.trevezani.zipcode.infraestructure.Application