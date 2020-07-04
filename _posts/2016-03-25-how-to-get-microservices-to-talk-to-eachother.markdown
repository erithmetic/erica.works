---
layout: post
title:  "Getting microservices to talk to each other: HTTP vs queues"
date: 2016-03-25 14:09:27 -0500
categories: microservices
---

When you take the microservices plunge, you have to decide how to let them talk to each other. There are two common approaches:

1. Services call out to other services via HTTP requests
1. Services communicate via a central message passing service (queue)

I’ll review both approaches to solve the following problem:

Imagine we need to generate a report and email the user when the process has finished with a link to the report. We also want to post stats and be notified in our chatroom when a user does this. The entire process is kicked off via an HTTP request to the Master Service, which runs the following function:

```
// Master Service handles HTTP request

function build_report() {
  // ...
}
```

## HTTP APIs

Your first intuition might be to have the Master Service send out a slew of HTTP calls:

```javascript
// Master Service handles HTTP request

function build_report() {
  reportBuilder = new HTTPService('http://builder.example.com');
  emailService = new HTTPService('http://emailer.example.com');
  statsService = new HTTPService('http://stats.example.com');
  chatRoomNotifierService = new HTTPService('http://notifications.example.com');

  // Build the report
  var result = reportBuilder.post('/report', user);
  
  // Notify the user
  emailService.post('/report_email', {
    user: user,
    report_url: result.report_url
  });

  // Update stats
  statsService.post('/stat', 'buildReport');

  // Notify devs via chatroom
  chatRoomNotifierService.post('/notify_report', user);
}
```

```javascript
// Inside the Report Builder's codebase:

function build_report() {
  // Handle POST /report
  var result = write_report_file();
  return {
    report_url: result.url
  }
}
```

```javascript
// Inside the Email Service's codebase:

function report_email() {
  // Handle POST /report_email
  var email = "Hello, " + request.params.user + ", your report is ready at " + request.params.report_url;
  send_email(email);
}
```

```javascript
// Inside the Chatroom Notifier's codebase:

function notify_report() {
  // Handle POST /notify_report
  var notification = request.params.user + " built a report";
  post_to_chatroom(notification);
}
```

This seems fine at first, but there are several drawbacks:

1. You’re going to need to add a bunch of error handling around each HTTP request: connection failures, timeouts, etc.
1. If one of the services is too busy, you need to have Master Service decide what to do, like back off its calls or push the request into a background job.
1. When it comes time to deploy each service, they all need to register to listen on certain domain names, like builder.example.com. The Master Service then needs to be configured with each of those domains. This adds a lot of deployment/systems architecture overhead.
1. Master Service is now overly concerned with the entire workflow related to a spreadsheet build, increasing its responsibility and complexity.
1. Any time we want to add a new step to the workflow, that’s one more service and task that Master Service needs to be configured with and coded for.

## Message queues

The alternative is to use the ancient 1990s technology known as a message queue. In this arrangement, the Master Service only posts messages to the queue and it is up to each other service to listen on that queue in order to perform work.

In our example, the Master Service will post a message to the report.build_requested queue. The Report Builder will handle that message, build the report, then post a message to the report.built queue. The notification services will then see that message and act accordingly.

```javascript
// Master Service handles HTTP request

function build_report() {
  var queue = new QueueService('amqp://queue.example.com');
  
  // Build the spreadsheet
  queue.publish('report.build_requested', { user: user });
}
```

```javascript
// Inside the Report Builder's codebase:

var queue = new QueueService('amqp://queue.example.com');

queue.subscribe('report.build_requested', function(message) {
  var result = write_report_file();
  queue.publish('report.built', {
    user: message.user,
    report_url: result.url
  });
});
```

 ```javascript
// Inside the Email Service's codebase:

var queue = new QueueService('amqp://queue.example.com');

queue.subscribe('report.built', function(message) {
  var email = "Hello, " + message.user + ", your report is ready at " + message.report_url;
  send_email(email);
});
```

```javascript
// Inside the Chatroom Notifier's codebase:

var queue = new QueueService('amqp://queue.example.com');

queue.subscribe('report.built', function(message) {
  var notification = message.user + " built a report";
  post_to_chatroom(notification);
});
```

With this approach, there are several advantages:

1. We push error handling to the central message queue. If one of the services fails, the message is retried asynchronously. There are many ways to configure retry strategies.
1. If a service gets too busy, the queue will hold onto the request and the Master Service can go on its merry way. More services to handle more messages can be started at will to handle the extra load. In more advanced scenarios, the Master Service can check the size of the queue and decide whether to publish another or tell the client to slow down.
1. Each service needs only one configuration option: the address of the central message queue.
1. Master Service is completely decoupled from the workflow.
1. Adding a new service to act on each event does not require any code changes to any of the services (except, perhaps if more information needs to be added to queue messages).

## Pros and cons

While the queue approach simplifies many things, it does come with some costs:

1. A scaling and downtime strategy is needed for the message queue itself (in most cases, it’s best to go with a hosted solution).
1. The overall workflow is now scattered among several services. This is where good documentation becomes important in order to know what happens when a message is published.
1. The queue worker services need to be transactional so that if a message is retried, data is left in a consistent state.
1. It’s important to remember that the published message formats are the new API—arbitrarily removing/restructuring them can cause integration problems.
