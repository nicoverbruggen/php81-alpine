# php81-alpine

## Why Alpine?

Using Alpine results in a much smaller Docker image!

## Quick start

* To build: `docker build .`
* To build and tag: `docker build -t tagname .`
* To run and get interactive terminal: `docker run -it tagname sh`

## What is this?

This is a custom build based on PHP 8.1's alpine docker image, with changes to make Laravel back-end testing easily possible.

## Where can I find it?

You can find the image on Docker Hub here: https://hub.docker.com/r/nicoverbruggen/php81-alpine.

## GitLab CI

For example, if you are running GitLab, you can use `.gitlab-ci` on your custom GitLab instance:

```
image: nicoverbruggen/php81-alpine:latest

cache:
  paths:
  - vendor/
  - node_modules/

tests:
  script:
  - curl -sS https://getcomposer.org/installer | php
  - php composer.phar install
  - npm install
  - npm run dev
  - vendor/bin/phpunit -v --configuration phpunit.ci.xml --coverage-text --colors=never
  after_script:
  - cat storage/logs/laravel.log 2>/dev/null
```

This will allow automatic tests of your application to occur.

A few notes:

- Front-end testing w/ Laravel Dusk is not supported in this version.
- This container ships with `npm` (NOT `yarn`!).

## How can I build this myself?

### Building on Arch Linux

Make sure that `docker` is installed:

    sudo pacman -Syy
    sudo pacman -S docker

Make sure the `docker` service is running:

    systemctl start docker.service

Make the build:

    sudo docker build -t nicoverbruggen/php81-alpine .

Test the build:

    sudo docker run -it nicoverbruggen/php81-alpine sh

Make sure you're signed in to Docker Hub:

    sudo docker login

Push the build:

    sudo docker push nicoverbruggen/php81-alpine

### Building on Apple Silicon *for* amd64/x86

**This is quite slow since emulation is used.**

To build for x86 specifically on Apple Silicon: 

    docker buildx build . --platform linux/amd64 -t nicoverbruggen/php81-alpine

### Building for your current architecture

Use the Dockerfile, customize it as desired and build it!

    docker build -t nicoverbruggen/php81-alpine . && docker push nicoverbruggen/php81-alpine
  
You could then push that to Docker Hub if you need x86 support. (You can also use `--push` with `buildx`!)

If you want to tag the current version (let's say... `1.0`) based on the latest version you just pushed:

    docker image tag nicoverbruggen/php81-alpine:latest nicoverbruggen/php81:1.0
    docker push nicoverbruggen/php81-alpine:1.0

Anyone can run it afterwards:

    docker run nicoverbruggen/php81-alpine

You can also attach to the container with shell:

    docker run -it nicoverbruggen/php81-alpine sh
