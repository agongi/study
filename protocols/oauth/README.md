## OAuth 2.0
OAuth is an open standard for authorization, commonly used as a way for Internet users to log into third party websites using their Microsoft, Google, Facebook or Twitter accounts without exposing their password. Generally, OAuth provides to clients a 'secure delegated access' to server resources on behalf of a resource owner. It specifies a process for resource owners to authorize third-party access to their server resources without sharing their credentials. Designed specifically to work with Hypertext Transfer Protocol (HTTP), OAuth essentially allows access tokens to be issued to third-party clients by an authorization server, with the approval of the resource owner. The third party then uses the access token to access the protected resources hosted by the resource server.

>###### The way to get resource from A from B without user of B credential using access token.

```
ㅁ Author: suktae.choi
- https://msdn.microsoft.com/en-us/library/office/dn631818.aspx
```

#### 1. Implicit grant flow
TBD (Not used secure production system)

#### 2. Explicit grant flow (Authorization code)

<img src="images/Screen%20Shot%202016-02-16%20at%2003.23.09.png" width="75%">

1) The client start OAuth, by using a URL in the following format :

```
[GET] https://{SERVER_DOMAIN}/oauth/authorize?client_id=CLIENT_ID&scope=SCOPE&response_type=code&redirect_uri=REDIRECT_URI
```

> client_id and client_secret is needed to be registered in resource provider before starting OAuth sequence.

> redirect_url is used after generating auth_code for bypassing to resource request server.

2) Resource provider return login form page, and user send login credential. (ID/PW)

3) Server response `HTTP 302 redirect` to user agent with using the redirection URI in header that was provided in the initial request.

```
Location: https://{REDIRECT_URI}?code=AUTHORIZATION_CODE
```

4) The user agent calls the client with the redirection URI, which includes an `authorization code` and any local state that was provided by the client.

```
[GET] https://{REDIRECT_URI}?code=AUTHORIZATION_CODE
```

5) The client requests an access token from the authorization server's token endpoint by using its client credentials for authentication, and includes the authorization code that was received in the previous step. The client includes the redirection URI that was used to obtain the authorization code for verification. The request URL has the following format:

```
[POST] https://{SERVER_DOMAIN}/oauth/token

Content-type: application/x-www-form-urlencoded

client_id=CLIENT_ID&client_secret=CLIENT_SECRET&redirect_uri=REDIRECT_URI&grant_type=refresh_token&refresh_token=REFRESH_TOKEN
```

6) If the credentials are valid, the authorization server responds by returning an `access token` with refresh token.
