FROM gitlab/gitlab-runner:alpine
ARG DOCKER_GID
RUN addgroup -g ${DOCKER_GID} docker
RUN addgroup gitlab-runner docker 

RUN apk --no-cache add docker
