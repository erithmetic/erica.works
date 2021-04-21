---
layout: post
title:  "How to run docker containers in Intel/x86 mode on an M1 Mac"
date: 2021-04-20 22:27:17 -0500
categories: docker
---

I'm working on a node.js/blitz.js/web project that uses pact via the pact-node
package. Unfortunately, this package isn't compatible with Apple's M1 (aarm64)
chip due to the standalone ruby (Intel/x86-only) that's packaged with it.
Instead of trying to have two node installations for each platform, I decided to
try and get docker to build and run my app images using the amd64 platform. And
it works!

First, make sure you have the latest [docker
desktop](https://docs.docker.com/docker-for-mac/apple-silicon/) that supports
aarm64 (M1).

Next, create a docker-compose.yml for your app (for now I'm setting up compose
so that I have to rebuild docker images on every code change, but you do have
the option of mounting your local source directory, though, there are
performance penalties to this).

You'll need to specify that you want to build and run the image/container with
the linux/amd64 platform.

```yaml
# docker-compose.yml

version: "3.8"
services:
  app:
    build:
      context: .
    platform: "linux/amd64"
    # ...
```

The tricky part is that you can't use docker-compose. First, it won't use
docker buildx which has cross-platform building capabilities. Second, for some
weird reason, docker-compose 2.4 supported the `platform` setting but subsequent
versions did not. 

You have to use the new `docker compose`
[command](https://docs.docker.com/compose/cli-command/) and not the classic
`docker-compose`:

```bash
docker compose run app
```

#### Possible tie-in with docker buildx

I did this on my local system but not 100% sure if it's necessary. But it's a
cool feature for managing build environments. Create a docker buildx builder
for your local app context:

```bash
docker buildx create --platform linux/amd64 --name my-app
docker buildx use my-app
```

Now when you run `docker buildx build -t my-app .` it will build the `my-app`
image using the platform specified in the builder you created.
