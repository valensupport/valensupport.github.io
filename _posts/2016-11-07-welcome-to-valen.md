---
layout: post
title:  Postman and Testing
date:   2025/12/15
sidebar: nav_sidebar
permalink: postman_post.html
---

Hello! Welcome to Insurity Predict's support blog. Here we hope to highlight some of the interesting things we run into while working on things like API integrations. Let's dive into a topic near and dear to my heart, testing. Hopefully this will point some of you in the right direction in regard to getting started with your integration of Insurity Predict's web services.

### Testing

We spend quite a bit of time testing our APIs. We have an automated framework that's written in Clojure. It's pretty useful since we can put code directly into our request data in order to randomize or generalize the requests easily. 

We really like [Postman-client]. If you haven't heard of it, it's pretty nifty. It's got a friendly user interface, can convert your requests into cURL or other languages, and can support collections. The collections are handy if you're testing a lot of the same APIs over and over again. No need to keep a file with cURL requests that you copy and paste into a terminal window, just click the request, make sure the parameters are correct (are you using a different auth or a different environment?) and hit send. The collections can also be shared. 
