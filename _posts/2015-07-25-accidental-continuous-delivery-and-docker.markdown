---
layout: post
title:  "Accidental Continuous Delivery and Docker"
date: 2015-07-25 12:00:00 -0500
categories: 
---

When I started at Faraday in the Summer of 2014, the company had begun a transition into a Saas product company. There were several individual apps that had grown into web services and queue workers. One of my first goals was to have a Continuous Integration system to run all the tests on each individual project.

Because many of our services relied on system libraries for GIS work and most of the tests were tightly coupled to a single postgres database, a lot of hosted CI solutions lost their appeal. I considered the various services we had that were written in varying versions of ruby and node. I also recognized that as our team grew and added programmers from different specialties (i.e. data scientists using python or R), I realized self-hosting a CI server configured to run all these different languages and platforms would become a mess. Especially when configuring deployments of our CI system.

But, I thought, what if the CI server could just be a web server and a bunch of dumb agents, and the projects themselves could define the platform they ran on?  What if each test could run in its own sandbox with any external services linked in as needed? This idea is what initially attracted me to docker.

## First steps

In late 2014 I set up a Jenkins server to do this very thing. One by one, I converted each project to define a CI-specific Dockerfile. This mainly involved making each app [12-Factor](http://12factor.net) compatible, configured by environment variables.

I initially waded through the various plugins for Jenkins that dealt with docker, but many of them seemed to be related to strange use cases I wasn’t considering yet. None of them involved simply building images and running them on a docker host. As I looked around at various CI servers, I ran into [GoCD](http://go.cd). GoCD’s pipeline workflows really attracted me, as I recognized that Jenkins was pretty ill equiped to handle the multiple stages needed for building and running test containers along with backing services. Go also relies less on plugins and more on defining per-project shell scripts to handle job setup. Since I would be doing a lot of manual service startup and docker commands anyway, I decided to switch to Go.

## Continuous Delivery is a Thing

One thing I learned while using Go is that it is built quite specifically for supporting a pipelined [Continuous Delivery](http://martinfowler.com/bliki/ContinuousDelivery.html) workflow. It was a concept I was never aware of, but quickly grew to appreciate and adopt. My coworker, Eric, was a big help in bringing CD ideas to fruition and pointed me to the excellent [book on Continuous Delivery](http://www.amazon.com/dp/0321601912?tag=contindelive-20).

Some of the biggest changes and challenges in adopting CD were: Making small changes directly to the mainline. It’s a major change in mindset to stop making huge branches on each service and instead make small, backwards-compatible changes to individual services, deprecating old functionality as older services are upgraded. This meant I no longer needed to worry about how to hammer GoCD around to try and get it to test PRs and branches. Testing master is enough.

Treating CD build errors as “[andon](https://en.wikipedia.org/wiki/Andon_%28manufacturing%29)” events. That is, when tests break, it’s everyone’s immediate job to get the CD pipelines fixed. It’s interesting that the CD pipeline effectively becomes your “car factory” in the Toyota Production System/Lean Software sense. If there’s a problem in the pipeline, it is a blocker that prevents your team from pushing new software.

The docker/CD combination is pretty instrumental in the idea of “immutable infrastructure.” The CD system contains your entire app’s cloud configuration and you can deploy any version of your system (including the specific combinations of service versions) at any time.

## From Test Images to Production

While our work on GoCD started as a way to test services, my team decided to move each service to run in docker in production.  Our initial solution was to use docker-machine to spin up instances and docker-compose to define and run our cluster. This has worked exceedingly well, although it is not a zero-downtime or rapidly scalable solution. Luckily, services like Amazon ECS are providing easier ways to deploy dockerized apps. We provide a docker-compose.yml configuration and they mostly do the rest.

## Development Environments

When I joined the team, all of the services were managed by some shell scripts sitting in a repo called “cage.” These scripts checked out branches on each service and used foreman to start the whole app cluster locally. As we migrated to docker, this cage tool became an app all its own.

One of the most difficult things about docker, I think, is using it in development. There are several tradeoffs involving how to install gems as you develop and making the service available for local testing/browsing.

I wasn’t attracted to the idea of rebuilding docker images and restarting the cluster each time I made a change. Instead, I came up with a way to run each app where local code changes appear immediately and libraries are shared in order to shorten image build/startup time. Initially I wrote separate Dockerfiles for dev, test, CI, and production environments, but later found that a single Dockerfile for all environments is sufficient.

Here’s an example docker-compose.yml and Dockerfile for a Rails app:

```yaml
# docker-compose.yml
myservice:
  volumes:
    - myservice:/app
    - /var/lib/docker/gems:/usr/local/bundle
  environment:
    RAILS_ENV: development
myservicetest:
  volumes:
    - myservice:/app
    - /var/lib/docker/gems:/usr/local/bundle
  environment:
    RAILS_ENV: test
```

```docker
FROM ruby:2.1.5

WORKDIR /app

ADD Gemfile /app/Gemfile
ADD Gemfile.lock /app/Gemfile.lock

RUN bundle

ADD . /app

EXPOSE 5000

CMD rails s -p 5000
```

Lines 5–8 of the Dockerfile allow us to make changes to the app code without forcing a full bundle install. Only a change to Gemfile/Gemfile.lock will trigger a bundle.

When interactively writing tests, all that is necessary is to run them with: `docker-compose run myservicetest rspec`. Now, our internal cage app makes this more succinct, but this is essentially what happens under the hood. Running `docker-compose up -d myservice` will start up a running rails app with local code changes recognized.

While there have been and are some bumps in the road in running a fully dockerized development environment, I think this approach adds several benefits: We can specify every service to run using a CD-tested and approved version, meaning we don’t have to go into each service repo and make sure we’re on the right branch/git commit when running the cluster locally. We also have a tool that modifies the docker-compose.yml to use specific versions of each service (using docker image tags) derived from GoCD’s last good build. This lets a developer work on her/his specific service while resting assured that every other service is at “last known good.” It also lets us select specific release combinations to debug locally.

We don’t need to manage the software configuration for every developer’s virtual machine. This used to be the case, where we had to be careful that the correct GIS libs, postgres version, ruby and node versions, etc. were installed on our vagrant VMs. Now, we just spin up a VM with docker-machine and let the containers do their own configurations.

Backing services like postgres, rabbitmq, etc. are easy to spin up for development. No more init scripts or arcane configs to worry about!  Integration/acceptance tests can be run in a dedicated cluster. Using docker links, we can run an entire test suite (or even multiple concurrent suites) completely separate from the development cluster, without involving manual cluster management. It’s just a `docker-compose up` command away.

## Conclusion

I feel a bit like Bilbo going on an Unexpected Journey through all of this, but it has been very rewarding. We’ve gone from weekly/monthly releases, to at-will releases, usually once or twice a week (non-zero downtime deployment is our biggest blocker at the moment). I love how the Continuous Delivery philosophy/tooling forces us to break our changes into smaller, independent units. While it seems like an impediment, I think it has made us more able to quickly address critical issues when we don’t have to wait for huge new feature branches to be merged in.
