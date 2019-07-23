## PID 1

### Even containerized services want to exit gracefully

### Only containerized PID 1 receives signals

Use exec when starting from scripts

### Multiple processes require an init process

Choices include supervisor, dumb-init, tini

### How to prevent init processes

Isolate in sidekicks

--

## Readability beats size

### Myth: More layers reduce access time

My own tests prove otherwise

### Layers improve performance on pull (parallel downloads)

### Recommendation: One layer per installed tool

--

## Base and derived images

### Separate functionality into chains of images

```
dind -> dind-gocd-agent
        linux-agent     -> linux-agent-gocd
                        -> linux-agent-jenkins
                        -> linux-agent-gitlab
```

### Enables code reuse

### Isolates changes

--

## Order of statements

### Build arguments

Used for controlling version pinning

### Environment variables

Used for tweaking runtime behaviour

### Tools and dependencies

E.g. Install distribution packages

### New functionality

Packages and scripts required for the purpose of the image

### The build cache helps you build faster!

--

## Validate downloads

### Distribution packages are validated

### Most package manager include validation

### Downloads from the web

Obtain file hash from the web

Create file hash after manual download

Check file hash during image build

```
$ echo “${HASH} *${FILENAME}“ | sha256sum
```

--

## Run as USER

### By default everything as root

Bad idea™

### Force user context

Add USER statement after setting up image

Some services handle this for you (nginx)

```
FROM ubuntu
# install
USER go
```

### Downstream images

Change to root

Install more tools

Change back to user

```
FROM derived
USER root
# install
USER go
```

--

## Microlabeling

### Mark images with information about origin

Easily find corresponding code

## Use image annotations by the OCI

Deprecated: https://label-schema.org

### Also breaks the build cache :-(

--

## Microlabeling

### Dockerfile

```
FROM ubuntu:xenial-20180123

LABEL \
org.opencontainers.image.created=“2018-01-31T20:00:00Z+01:00“ \
org.opencontainers.image.authors=“nicholas@dille.name“ \
org.opencontainers.image.source=“https://github.com/nicholasdille/docker“ \
org.opencontainers.image.revision=“566a5e0“ \
org.opencontainers.image.vendor=“Nicholas Dille“
```

--

## XXX

### Pull during build

Prevent usage of outdated images

```
docker build --pull ...
```

### Timezones

Synchronize time

```
docker run -v /etc/localtime:/etc/localtime ...
```

### Derive dynamically

Build argument defines default base image

```
ARG VERSION=xenial-20180123
FROM ubuntu:${VERSION}
```