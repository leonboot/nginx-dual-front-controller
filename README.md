# Docker app with static and dynamic front controller and scalable FPM instances

This is a proof-of-concept for a web service with two front controllers:

* One PHP front controller for handling back-end calls (the API)
* One client-side front controller for handling the UI calls (the front-end)

As a bonus, the web server is able to use multiple PHP-FPM containers to
balance the load on these servers.

## Running the app

Make sure you have Docker and Docker Compose installed. Docker Compose should
be able to parse at least version 2 YAML files.

To start the app, type:

```shell
$ docker-compose up
```

This app plays nice with the `code-kitchen/dinghy-http-proxy` container, so
after all containers have started, you should be able to access the app by
pointing your browser to *http://web.yourprojectname.docker/*, provided the
code lives in the directory `yourprojectname` and you've kept the default TLD
`.docker`.

### Static front controller
By default, your request should be handled by the client-side code front
controller. The header should indicate **Static content!** and the current URL should be displayed in the body (this is done using Javascript).

### Dynamic front controller
Any URL should be handled by the static front controller, except those starting with `/api` and `/admin`. These URLs will be routed to the back-end
front controller. So accessing a URL like *http://web.yourprojectname.docker/api/foo* show a header stating **Dynamic content!** The host name of the FPM container is also printed in the body.
Since there are two FPM containers in this app, refreshing the page should
return a different hostname. These two hostnames should alternate. If you
take a log at the console, where you ran the `docker-compose up` command, you
should see that the requests are alternately handled by both FPM contiainers.

### Static files
This setup uses Nginx's `try_files` directive. This basically says: "If the
URL requested is not an actual existing file, let this script handle it".
So by default, static files are directly served by Nginx, so no bootstrapping
of your framework is done on each static file request. Try accessing
*http://web.yourprojectname.docker/test.xt*, this is a static file directly
served by Nginx.
