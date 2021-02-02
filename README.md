```
________________________________________________________________ 
\______   \_   _____/\__    ___/\__    ___/\_   _____/\______   \
 |       _/|    __)_   |    |     |    |    |    __)_  |       _/
 |    |   \|        \  |    |     |    |    |        \ |    |   \
 |____|_  /_______  /  |____|     |____|   /_______  / |____|_  /
        \/        \/github.com/hyperjumptech/retter\/         \/ 
```
# RETTER

- **RETTER** is ... a Great Savior for your great web solution.
- **RETTER** is ... a German word for "Savior"
- **RETTER** is ... technically its a Circuit Breaker server. Yes. [Circuit Breaker as explained by Marti Fowler](https://martinfowler.com/bliki/CircuitBreaker.html)

## Problem Statement

- My HTTP server now being hit by gazillion request per-second.
- When this happen, my user start to suffer. As their experience becoming worse and worse overtime.
- As response time grew longer, request time-out started to creep to my user.
- My Web App logs shows errors like "DB Error : Too many connection open" appears like hell.
- People told me to do a Horizontal or Vertical scaling toward my web or database server. But I only have a budget for none, may be I can squeeze 1 small server.
- I need my user to slowdown a bit. To give my server time to breathe. But I don't want to sacrifice my user experiences.
- I don't want to code my Web App further since I already optimize the hell out of it.

## How RETTER can help?

- Retter is a server application that implements Circuit Breaker pattern.
- It sits infront of your Web Server receiving HTTP requests and forward them to your server.
- When it sees that your server start to get overwhelmed by requests, for the next incoming request, RETTER will reply back to the requester with the last known success response.
- This way, RETTER give time for your Web App (and all connected server, eg. DB Server) to finish its piling tasks and recover back resources such as memory and database connections.

In normal condition (CIRCUIT CLOSE):

```text
                        RETTER forwards request and response to your webapp
  _______               +--------+            +--------+          +--------+
/         \ --request-->|        |--request-->|        |--query-->|        |
| Internet |            | RETTER |            | WEBAPP |          |   DB   |
\_________/ <--request--| cache  |<-response--|        |<-result--|        |
                        +--------+            +--------+          +--------+
```

In ugly condition (CIRCUIT OPEN):

```text
 RETTER immediately respond to new requests with last known success response 
  _______               +--------+            +--------+          +--------+
/         \ --request-->|        |-----+      |        |--query-->|        |
| Internet |            | RETTER |     v      | WEBAPP |          |   DB   |
\_________/ <--request--| cache  |<-response--|        |<-result--|        |
                        +--------+            +--------+          +--------+
                           Thus gives time to your server to finish their works
```

# Installation

{install doc in progress}

# Benchmark

{no benchmark yet}

# FAQ

**Q1** : Is it guaranteed that my web performance increases ?<br>
**A1** : Slight increase, Yes!. What is more important is "Resilience" to your web server.

**Q2** : Is it good for streaming server? Or servers that provides huge content (eg. movie)?<br>
**A2** : Nope. RETTER will easily running out of memory and it give delays as it need to get the full response first for caching.

**Q3** : I have multiple WebApp, its like a cluster. Can 1 RETTER server instance serve them all?<br>
**A3** : Nope. RETTER can sit infront of a WEB balancer though. It will forward all headers such as `X-Real-IP` or `X-Forwarded-For`

**Q4** : Do RETTER remember HTTP sessions (eg `PHPSESSID`)? I do *sticky session* and content are delivered per-user basis, different content for different user.<br>
**A4** : Yup. RETTER caches responses based on the URL Paths and Cookie of `PHPSESSID`, `JSESSIONID` and `ci_session`

**Q5** : Do RETTER support response compression?<br>
**A5** : Yup. Only if the HTTP client requested using `Accept-Encoding: gzip` header.

**Q6** : Do RETTER support response compression?<br>
**A6** : Yup. Only if the HTTP client requested using `Accept-Encoding: gzip` header.

**Q7** : My API-Gateway already have Circuit Breaker, should I use RETTER? or is using RETTER do any better?<br>
**A7** : Go away !! You are just trolling me.

**Q8** : RETTER is buggy, you noob golang programmer, can you do any better?<br>
**A8** : Nope, Until you put an Issue. Or even better, a PR!

**Q9** : What is being cached by RETTER?<br>
**A9** : RETTER will cache all response of all **GET**..., Yes **GET** method, identified by its `URL path + queries + session cookies`. 

**Q10** : If the circuit breaker only works for `GET` request, how about the other?<br>
**A10** : Other `method` WILL NOT BE circuit breaked; that means all `POST`, `PUT`, `DELETE`, `OPTIONS`, `HEAD`, `PATCH` will be forwarded to your web-app normally. 
