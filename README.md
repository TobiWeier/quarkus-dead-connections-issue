# quarkus-dead-connections-issue

1. start database

        docker run -d -e POSTGRES_USER=hibernate -e POSTGRES_PASSWORD=hibernate -e POSTGRES_DB=hibernate_db -e POSTGRES_PORT=5432 --name mydb -p 5432:5432 postgres
2. start quarkus

        mvn clean quarkus:dev
3. use command for watching metrics from connection pool

        watch -n 2 "curl -s http://localhost:8080/q/metrics | grep -E '^vendor_agroal'"
4. use command for watching health status

        watch -n 2 "curl -is http://localhost:8080/q/health"
5. kill database (hard!)

         docker kill mydb
6. restart database

         docker start mydb

Now repeat steps 5 and 6 several times and observe the metrics values "vendor_agroal_available_count" and the health status.
If the maximum number of connections is reached, the pool status remains permanently on DOWN, even when the database is available again.
