= Erudio API OAUTH2

image::https://travis-ci.org/royclarkson/spring-rest-service-oauth.svg[Build Status, link=https://travis-ci.org/royclarkson/spring-rest-service-oauth/]

This is a simple REST service that provides a single RESTful endpoint protected by OAuth 2. The REST service is based on the https://github.com/royclarkson/spring-rest-service-oauth[Spring REST Service OAuth - by Roy Clarkson] and in another projects mine like https://github.com/leandrocgsi/erudio-api-with-groovy[Erudio API With Groovy], https://github.com/leandrocgsi/semeru_jsf_groovy_maven[Semeru JSF Groovy Maven] and https://github.com/leandrocgsi/erudio-client-ws-exporter[Erudio Client WS Exporter]. This project incorporates the new Java-based configuration support, now available in Spring Security OAuth 2.0. Please log any issues or feature requests to the https://github.com/spring-projects/spring-security-oauth/issues[Spring Security OAuth project].


== Spring Projects

The following Spring projects are used in this sample app:

* http://projects.spring.io/spring-boot/[Spring Boot]
* http://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html[Spring MVC]
* http://projects.spring.io/spring-security/[Spring Security]
* http://projects.spring.io/spring-security-oauth/[Spring Security OAuth]
* http://projects.spring.io/spring-data-jpa/[Spring Data JPA]
* http://www.groovy-lang.org/[Groovy] (Coming soon)
* http://swagger.io/[Swagger] (Coming soon)


== Build and Run

Use Gradle:

```sh
./gradlew clean build bootRun
```

Or Maven:

```sh
mvn clean package spring-boot:run
```

== Usage

Test the `greeting` endpoint:

```sh
curl http://localhost:8080/api/v1/city/findAll
```

You receive the following JSON response, which indicates you are not authorized to access the resource:

```json
{
  "error": "unauthorized",
  "error_description": "An Authentication object was not found in the SecurityContext"
}
```

In order to access the protected resource, you must first request an access token via the OAuth handshake. Request OAuth authorization:

```sh
curl -X POST -vu clientapp:123456 http://localhost:8080/oauth/token -H "Accept: application/json" -d "password=spring&username=roy&grant_type=password&scope=read%20write&client_secret=123456&client_id=clientapp"
```

A successful authorization results in the following JSON response:

```json
{
  "access_token": "ff16372e-38a7-4e29-88c2-1fb92897f558",
  "token_type": "bearer",
  "refresh_token": "f554d386-0b0a-461b-bdb2-292831cecd57",
  "expires_in": 43199,
  "scope": "read write"
}
```

Use the `access_token` returned in the previous request to make the authorized request to the protected endpoint:

```sh
curl http://localhost:8080/api/v1/city/findAll -H "Authorization: Bearer ff16372e-38a7-4e29-88c2-1fb92897f558"
```

If the request is successful, you will see the following JSON response:

```json
{
  "id": 1,
  "content": "Hello, Roy!"
}
```

After the specified time period, the `access_token` will expire. Use the `refresh_token` that was returned in the original OAuth authorization to retrieve a new `access_token`:

```sh
curl -X POST -vu clientapp:123456 http://localhost:8080/oauth/token -H "Accept: application/json" -d "grant_type=refresh_token&refresh_token=f554d386-0b0a-461b-bdb2-292831cecd57&client_secret=123456&client_id=clientapp"
```


== SSL

To configure the project to run on HTTPS as shown in https://spring.io/guides/tutorials/bookmarks/[Building REST services with Spring], enable the `https` profile. You can do this by uncommenting the appropriate line in the application.properties file of this project. This will change the server port to `8443`. Modify the previous requests as in the following command.

```sh
curl -X POST -k -vu clientapp:123456 https://localhost:8443/oauth/token -H "Accept: application/json" -d "password=spring&username=roy&grant_type=password&scope=read%20write&client_secret=123456&client_id=clientapp"
```

The `-k` parameter is necessary to allow connections to SSL sites without valid certificates or the self signed certificate which is created for this project.

