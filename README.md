# Spring Pipeline Actions

This is a simple Spring Boot project with an example of Continuous Integration   
and Continuous Delivery (CI/CD) using GitHub Actions.

### Overview

This project demonstrates how to set up GitHub Actions to automatically build, test, and   
deploy a Spring Boot application. When changes are pushed to the **main** branch, GitHub   
Actions will automatically run a pipeline to build a Docker image, run the tests, and deploy the application to a Docker Hub.

### Prerequisites
- JDK 17 or higher
- Docker

### Getting Started
1- Clone the repository
```bash
git clone https://github.com/hassanrefaat9/spring-pipeline-actions
```
2- Create a main.yml file  for the application workflow
3- Copy and paste the following yml into main.yml to create the CI/CD workflow
```yml
name: Build and Deploy
# Controls when the action will run. Triggers the workflow on push
on:
  push:
    branches:
      - master
# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  build-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest # the type of machine to run on
    steps:
      - name: Checkout code # checks out a copy of your repository on the ubuntu-latest machine "copy of the repo to the runner"
        uses: actions/checkout@v3 # uses the built-in checkout action you can git from github actions site

      - name: Setup JDK 17 # sets up the JDK version 17
        uses: actions/setup-java@v3
        with:
          java-version: 17
          distribution: 'corretto' # distribution of the JDK

      - name: Unit Tests # runs the unit tests
        run: mvn -B test --file pom.xml # runs the maven command to run the unit tests

      - name: Build  # builds the project
        run: |
          mvn clean
          mvn -B package --file pom.xml

      - name: Build Docker Image
        uses: docker/build-push-action@v2 # uses the build-push-action to build the docker image
        with:
          context: . # the context of the docker image . means the root of the project
          dockerfile: Dockerfile # the dockerfile to use
          push: false
          tags: ${{ secrets.DOCKER_HUB_USERNAME }}/spring-pipeline-actions:latest # the tag of the docker image
      - name: Login to Docker Hub
        uses: docker/login-action@v1 # uses the docker login action to login to docker hub
        with:
          username: ${{ secrets.DOCKER_HUB_USERNAME }} # the username of the docker hub
          password: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }} # the password of the docker hub

      - name: Push to Docker Hub
        uses: docker/build-push-action@v2 # uses the docker build-push-action to push the docker image to docker hub
        with:
          context: .
          dockerfile: Dockerfile
          push: true
          tags: ${{ secrets.DOCKER_HUB_USERNAME }}/spring-pipeline-actions:latest
```
3- Add environment variables to the repository settings
```bash
DOCKER_HUB_USERNAME
DOCKER_HUB_ACCESS_TOKEN
```
4 - get the information of the docker hub username and access token from the docker hub

### Conclusion
This project provides an example of how to use GitHub Actions to set up a simple CI/CD   
pipeline for a Spring Boot application. You can use this as a starting point for your own   
projects and customize it as needed to fit your requirements.