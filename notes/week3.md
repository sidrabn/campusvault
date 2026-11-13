# Questions – Week 3

**1. Why does the server need `--host 0.0.0.0` inside the container, but we publish it with `-p 127.0.0.1:8000:8000` on the laptop?**

`0.0.0.0` allows the server to accept connections from outside the container. `127.0.0.1` makes the API accessible only from my laptop.

**2. The curl call with `"owner": "admin"` was rejected. Which line of code did that? Describe what could happen in a real app with user accounts if that line were missing.**

The line `model_config = ConfigDict(extra="forbid")` rejected the unknown field. Without it, users could send unauthorized fields and possibly modify data they should not control.

**3. `?limit=1000` was rejected. Why is an unlimited list endpoint a security problem, not only a performance problem?**

An unlimited endpoint can expose too much private data and allow attackers to collect large amounts of information.

**4. `docker exec cv id` shows which user? Why will we change that next week?**

It shows the root user. We will change it next week to use a non-root user for better security and to reduce damage if the application is attacked.

**5. Open `http://127.0.0.1:8000/openapi.json`. Give one reason a company might not publish this file in production, and one reason it might.**

A company might hide it to avoid exposing API details to attackers. However, it might publish it to help developers and partners understand and use the API.

**6. A classmate writes `GET /deleteNote?id=3`. Give two reasons why this is a bad idea.**

1. GET requests should retrieve data, not delete it.
2. It can be triggered accidentally by links, browsers or automated tools. DELETE is the correct HTTP method.