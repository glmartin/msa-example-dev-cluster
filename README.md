# Microservices Architecture (MSA) Dev Cluster

## Overview

This project acts as an example of the many compoents that go into building a REST API Microservice. Ideally this
would be deployed using [Kubernetes](https://kubernetes.io/), but for develoment, I am using [Docker Compose](https://docs.docker.com/compose/).

This code is a culimantion of a few of the projects that I have worked on in the past. The code has been greatly simplified and changed as to not give away any secrets from previous companies. Additionally, I have added a closer adherance to [Roy Fieldings dissertation](https://ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm) on REST. This incles support for [HAL - Hypertext Application Language](https://stateless.group/hal_specification.html).

This project includes the following:

* REST API using Spring Boot, unit testing provided using [JUnit](https://junit.org/junit5/), [REST Asssured](https://rest-assured.io/), and [TestContainers](https://testcontainers.com)
* Database Migrations, handled using [Sqitch](https://sqitch.org/). The migrations are handled in a separate image from the 
REST API image because the REST API is intended to be one of potentially many microservices that use the same DB. Therefore, we could use different images, and ideally different GitHUb Repos.
* JWT Authentication - TODO
* Buid and Deployment with GitHub Actions - TODO

## Related Repos:

https://github.com/glmartin/msa-example-rest-api
https://github.com/glmartin/msa-example-db-mgmt


## References

https://spring.io/guides/tutorials/rest/
https://oauth.net/2/
https://rest-assured.io/
https://testcontainers.com/guides/testing-spring-boot-rest-api-using-testcontainers/
https://ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm
https://stateless.group/hal_specification.html
https://spring.io/guides/topicals/spring-boot-docker/