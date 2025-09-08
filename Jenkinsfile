variables:
    IMAGE_NAME: kamalfarah/python-demoapp
    IMAGE_TAG: python-app-1.0
    REGISTRY_USER: kamalfarah
    REGISTRY_PASS: Kmeva1982
stages:
  - test
  - build
  - deploy


run_tests:
  stage: test
  image: python:3.9-slim-buster
  before_script:
    - apt-get update && apt-get install make
  scripts:
    - make test

build_image:
  stage: build
  image: docker:20.10.16
  services:
    - docker:20.10.16-dind
  variables:
    DOCKER_TLS_CERTDIR: "/certs"

  
  before_script:
    - docker login -u $REGISTRY_USER -p $REGISTRY_PASS
  scripts:
    - docker build -t $IMAGE_NAME:$IMAGE_TAG .
    
    - docker push $IMAGE_NAME:$IMAGE_TAG 
deploy:
  stage: dedploy
  script:
  
