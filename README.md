# Intro
Repository contains code that represent issue with accessing angular app (Petclinic) deployed to docker container where traffic is handled by Traefic (reverse proxy).
To deploy the containers to the localhost just run the command in the project root: docker-compose up

There are three containers:
- web that is an Apache web server that contains simple pages just to show the url path composition for the whole UI
- [petclinic-api](https://github.com/spring-petclinic/spring-petclinic-rest) container that server as REST API for the Petclinic web (also contains runtime DB)
- [petclinic-ui](https://github.com/spring-petclinic/spring-petclinic-angular) container that contains the Petclinic Angular web app

## The solution I want to configure:
1. web container needs to be redirected to port 80 (localhost) - this is working fine
2. petclinic-ui container needs to be also redirected to port 80 (localhost) but only on the url path: `/practice/petclinic/` 

## My configuration ideas
1. Idea was to use Nginx web server config to redirect incoming traffic on the url path `/practice/petclinic/` to the main angular `\index.html file`. This was done by configuring app web server (nginx) to listen on the url prefix `\practice\petclinic\` and redirecting the trafic to the file `\index.html` inside the container. This is configured in the file:
`issue\petclinic-ui\nginx\angular.conf` where the file is added to the container during the build in the file:
`issue\petclinic-ui\Dockerfile` line 21. Also the angular property APP_BASE_HREF is configured in `issue\petclinic-ui\src\app\app.module.ts` on line 62. Solution is working fine on the exposed port: 82 without the redirect (you can test it like this: `localhost:82/practice/petclinic/`). Unfortunately if I try the redirect to port 80 the angular app with start but it will not load the script files (404 response). You can test it by providing the url in the browser: `localhost/practice/petclinic/`

2. Another idea is to remove the angular nginx config and run the petclinic app inside the container without the url prefix (so just on `localhost:82/`) and configure Traefic to listen on the url prefix but remove it when redirecting to the target container. Unfortunately I was not able to configure it successfully.
