# Microservices Architecture (MSA) Dev Cluster

## Overview

This project acts as an example of the many compoents that go into building a REST API Microservice. Ideally this
would be deployed using [Kubernetes](https://kubernetes.io/), but for develoment, I am using [Docker Compose](https://docs.docker.com/compose/).

**NOTE**: I am not publishing the images for these example to any image repository. So they need to be build manually. See the other repos for more information.

This code is a culimantion of a few of the projects that I have worked on in the past. The code has been greatly simplified and changed as to not give away any secrets from previous companies. Additionally, I have added a closer adherance to [Roy Fieldings dissertation](https://ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm) on REST. This incles support for [HAL - Hypertext Application Language](https://stateless.group/hal_specification.html).

This project includes the following:

* REST API using Spring Boot, unit testing provided using [JUnit](https://junit.org/junit5/), [REST Asssured](https://rest-assured.io/), and [TestContainers](https://testcontainers.com)
* Database Migrations, handled using [Sqitch](https://sqitch.org/). The migrations are handled in a separate image from the 
REST API image because the REST API is intended to be one of potentially many microservices that use the same DB. Therefore, we could use different images, and ideally different GitHUb Repos.
* JWT Authentication - TODO
* Buid and Deployment with GitHub Actions - TODO


## Access Managment

For this project, I am using [Keycloak](https://www.keycloak.org/) to handle access management. I set up a realm (lv-426), user (eripley), auth client, and realm role (msa-orgs). Using the default Keycloak setup for the JWT, the role is listed in the `realm_access.roles` array:

```
"realm_access": {
    "roles": [
      "msa-orgs",
      "offline_access",
      "uma_authorization",
      "default-roles-lv-426"
    ]
}
```

To check the roles in the API, I added a new `JwtAuthenticationConverter` bean, convert the roles in the JWT to a 
collection of `GrantedAuthority` objects. Refer to the `SecurityConfig` class in the API for more info.

Use http://localhost:8080/realms/lv-426/.well-known/openid-configuration to view the configuration.

### Get a JWT

To get a JWT, we make a post to the `/realms/lv-426/protocol/openid-connect/token` endpoint of the Keycloak API: 

```shell
curl -L -X POST 'http://localhost:8080/realms/lv-426/protocol/openid-connect/token' \
    -H 'Content-Type: application/x-www-form-urlencoded' \
    --data-urlencode 'client_id=${KEYCLOAD_CLIENT_ID}' \
    --data-urlencode 'grant_type=password' 
    --data-urlencode 'client_secret=${KEYCLOAD_CLIENT_SECRET}' \
    --data-urlencode 'scope=openid' \
    --data-urlencode 'username=${KEYCLOAD_USERNAME}' \
    --data-urlencode 'password=${KEYCLOAD_USER_PASSWORD}'
```

This will return JSON containing the `access_token`. The `access_token` is the JWT. We can then use the JWT in calls to the API:

```shell
curl -v -H "Authorization: Bearer $JWT" http://lv-426.restapi/api/organizations
```

*NOTE*: I added `lv-426.restapi` to my `/etc/hosts` so I can call it without using `localhost`.

## Secrets

Since this is a docker-compose project, secret values are handled in separate files. I added `secrets_*` to the
`.gitignore` file so we can create simple text files that would contain our secrets, and it wouldn't be committed to Git. 
In this case, the `docker-compose.yaml` is expecting a file called `secrets_db_password` that would contain the password
for the DB.

Additionally, secrets can be added to a `.env` file that would be as environment variables.

## Related Repos:

* https://github.com/glmartin/msa-example-rest-api
* https://github.com/glmartin/msa-example-db-mgmt


## References

* https://spring.io/guides/tutorials/rest/
* https://oauth.net/2/
* https://rest-assured.io/
* https://testcontainers.com/guides/testing-spring-boot-rest-api-using-testcontainers/
* https://ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm
* https://stateless.group/hal_specification.html
* https://spring.io/guides/topicals/spring-boot-docker/
* https://developers.redhat.com/blog/2020/01/29/api-login-and-jwt-token-generation-using-keycloak#
