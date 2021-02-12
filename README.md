# quarkus-dead-connections-issue

1. Create quarkus project

        mvn io.quarkus:quarkus-maven-plugin:1.11.3.Final:create -DprojectGroupId=org.acme -DprojectArtifactId=dead-conns-issue -DclassName="org.acme.getting.started.GreetingResource" -Dpath="/hello"
2. add hibernate-orm dependencies 

        <!-- Hibernate ORM specific dependencies -->
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-hibernate-orm</artifactId>
        </dependency>
        <!-- JDBC driver dependencies -->
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-jdbc-postgresql</artifactId>
        </dependency>
3. add quarkus-smallrye-metrics dependency

        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-smallrye-metrics</artifactId>
        </dependency>
4. add quarkus-smallrye-health dependency

        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-smallrye-health</artifactId>
        </dependency>
5. add properties to application.properties

        quarkus.datasource.db-kind = postgresql
        quarkus.datasource.username = hibernate
        quarkus.datasource.password = hibernate
        quarkus.datasource.jdbc.url = jdbc:postgresql://localhost:5432/hibernate_db
        quarkus.hibernate-orm.database.generation=drop-and-create
        quarkus.hibernate-orm.dialect=org.hibernate.dialect.PostgreSQLDialect
        quarkus.hibernate-orm.log.jdbc-warnings=true
        quarkus.datasource.health.enabled=true
        quarkus.datasource.metrics.enabled=true
        quarkus.datasource.jdbc.enable-metrics=true
        quarkus.datasource.jdbc.max-size=5
        quarkus.datasource.jdbc.min-size=3
7. start database

        docker run -d -e POSTGRES_USER=hibernate -e POSTGRES_PASSWORD=hibernate -e POSTGRES_DB=hibernate_db -e POSTGRES_PORT=5432 --name mydb postgres
8. start quarkus

        mvn clean quarkus:dev
9. use command for watching metrics from connection pool

        watch -n 2 "curl -s http://localhost:8080/q/metrics | grep -E '^vendor_agroal'"
10. use command for watching health status

        watch -n 2 "curl -is http://localhost:8080/q/health"
11. kill database (hard!)

         docker kill mydb
12. restart database

         docker start my

Now repeat steps 11 and 12 several times and observe the metrics values "vendor_agroal_available_count" and the health status.
