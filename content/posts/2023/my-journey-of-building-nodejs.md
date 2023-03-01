---
title: "My Journey of Building a Node.js App"
date: 2023-02-28T08:35:55+01:00
tags: [node, javascript, express, ejs, salesforce]
thumbnail: "/images/posts/2023/journey-node/node-logo.png"
summary: ""
draft: true
---

If you've followed me for a while, you know about my [Salesforce OAuth Flows app](https://github.com/NVandenBossche/salesforce-iam-flows). It's an app written in Node.js that I've created. Its goal is to better understand OAuth flows for Salesforce.

In this blog post, we're going to dive into the architecture of the app and its evolution over time. This will give us a good overview of how to build an application using Node.js. Sprinkled with some Salesforce goodness.

## Inheriting Code

I didn't start from scratch when creating this app. Jitendra Zaa had already created a [Node.js app with some flows](https://github.com/JitendraZaa/OAuthFlows). I was inspired by it, cloned it, and started adding my own flows into the mix. Over time, I also used this app to learn more about JavaScript, Node.js and the Express web app framework that Jitendra was using.

The more I learned about these technologies, the more I started to clean up the code. The app was originally built for a quick prototype. My plans are to make it the go-to practical resource for people to learn about Salesforce Identity & Access Management. So I started incorporating best practices from JavaScript and Node.js.

I'm not an expert in any way, and I don't expect anyone to use this in production. However, having a nice codebase comes with a certain sense of fulfilment.

Next, we'll walk through the revised application architecture.

## Understanding the Monolith

The original application had a monolithic design. There was a single JavaScript file to represent the server. There was a single JavaScript file to handle client-side logic. There was one HTML file for each page, just like in the early days of the web.

![A screenshot of the original structure of the architecture. It shows monolithic files.](/images/posts/2023/journey-node/original-structure.jpg)

Modern JavaScript web applications provide much more advanced capabilities. These capabilities allow for adhering to best practices such as separation of concerns. They promote reuse of common components like a library for URL encoding. But they also come with more difficult to manage dependencies from external libraries and frameworks.

The first thing I did was to identify the different frameworks and components this app was using. I knew from the beginning that this app was written in _Node.js_. I found out it adopted the _Express_ JavaScript framework by looking at require statements inside the JavaScript files. And in some of the views, I saw _Embedded JavaScript (EJS)_ files instead of HTML.

It was crucial to understand that Node.js is used to write server-side logic. This is very different from what JavaScript is typically doing: client-side logic. This means that with a Node.js app, you can write both the front-end and the back-end in JavaScript. A single language for both, that took me back to university and Java Server Pages.

Express added a framework on top of Node that is made for single-page applications like the one I was building. Especially the built-in routing turned out to be useful.

Embedded JavaScript (EJS) is the final pieces of the puzzle. This is what's called a templating engine. It allows you to build web applications made out of smaller components. In hindsight that smells a lot like "web components" and [LWC](https://lwc.dev/). That might be another project to see if I can build this app with [LWR on Node.js](https://developer.salesforce.com/docs/platform/lwr/guide).

## Adding More Flows

Once I more or less understood what each technology is used for, I started to make the code my own. At first I didn't change too much about the structure. I focused on adding new flows like the SAML Bearer Token flow.

It took me a while to get all the flows working. There was an upside of this trial-and-error though. I started to better understand how each flow worked. Errors made sense and I was able to troubleshoot them in no time. One by one each flow was working against my personal developer org. I tackled Device, Username-Password, SAML Bearer, Refresh, and even SAML Assertion flow!

There was one other thing that bothered me. There was no way to secure sensitive data like credentials, tokens and keys. I decided to take care of that before putting an actual application online. I leveraged Heroku and its environment variables, since Heroku was at the time still free.

After all this, there was one problem remaining. The monolith had become even bigger by adding all those flows! It became more and more difficult to bugfix or extend the application. I was scrolling through code instead of working on it.

## Breaking Down the Monolith

The Server.js file that represents the Node.js back-end server was becoming enormous. There was a lot of logic seeping into the file. I decided to move all logic into separate classes, then import those classes. I reduced the number of lines in the [main server file](https://github.com/NVandenBossche/salesforce-iam-flows/blob/v1.0/Server.js) by 50%! We can see one of the resulting endpoints below. Nice and clean.

<!-- {{< gist NVandenBossche c083f047c494d57842ffa59c4a96675c "Server.js" >}} -->

{{<highlight js "linenos=true, linenostart=156">}}
app.get('/jwt', function (req, res) {
// Instantiate JWT service and generate post request
authInstance = new JwtService(req.query.isSandbox);
let postRequest = authInstance.generateJwtRequest();

    // Handle the response of the post request
    handlePostRequest(postRequest, res);

});
{{< / highlight >}}

This had the added value of splitting up the codebase into logical files. I could now easily find each flow's logic by navigating to its dedicated file.

![A screenshot of the revised structure of the architecture. It shows modularised files.](/images/posts/2023/journey-node/original-structure.jpg)

And the code inside could also be neatly partitioned into logical components. We can see the example for [JWT](https://github.com/NVandenBossche/salesforce-iam-flows/blob/v1.0/services/jwt.js) below.

{{<highlight js "linenos=true">}}

const { AuthService } = require("./auth");

var base64url = require("base64-url");

class JwtService extends AuthService {
constructor(isSandbox) {
super(isSandbox);
}

    /**
    * Create a JSON Web Token that is signed using the private key stored in 'key.pem'.
    * It first creates the Claims JSON and passes it to the signJwtClaims method.
    */
    getSignedJwt() {
        var claims = {
            iss: this.clientId,
            sub: this.username,
            aud: this.getAudience(),
            exp: Math.floor(Date.now() / 1000) + 60 * 3, // valid for 3 minutes
        };

        return this.signJwtClaims(claims);
    }

    generateJwtRequest() {
        // Set parameters for POST request
        const grantType = "urn:ietf:params:oauth:grant-type:jwt-bearer";
        let endpointUrl = this.getTokenEndpoint();
        let token = this.getSignedJwt();
        let paramBody =
            "grant_type=" + base64url.escape(grantType) + "&assertion=" + token;

        // Construct POST request based on body and endpoint
        return this.createPostRequest(endpointUrl, paramBody);
    }

}

exports.JwtService = JwtService;

{{< / highlight >}}

## Conclusion

We've seen how you can build a web application using Node.js, Express and EJS. It's important to keep such an application maintainable. This means working on modularising and simplifying the architecture. We've walked through how you can achieve this.

Hopefully this will inspire you to create your own Node.js app, or do something cool with our Identity APIs. Of course, I'm not a professional Node.js developer. There are for sure many things that I can improve in this app, so let me know how!

Recently, I've done an overhaul of the app so that the front-end is more flexible, even configurable. This means that I can add new flows to the app by updating a configuration file. Additionally, it makes the source code much easier to understand. That is something for a next blog post.
