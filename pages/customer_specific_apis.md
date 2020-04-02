---
layout: page
title: InsureRight® Predict Web Services
permalink: web_services.html
sidebar: nav_sidebar
last_updated: November, 2016
summary: The webservices guide for making scoring requests to the 3.0 platform.
---

# Valen WebServices Implementation Guide

### Integrating webservices for Predict on InsureRight® Platform 3.0

The InsureRight® Predict Web Service (PWS) allows clients to send input data, in the form of a prediction request, to the InsureRight platform for scoring with Valen’s models. Once received, the system validates the XML and data content of the transmission, scores the data, then returns a prediction response.

In addition to the score results, the system also returns information to explain the predictions in terms of influential data elements. Optionally, Valen can incorporate custom business rules to provide support for automated decision making based on the prediction results.

##### Audience

This guide is intended for use by developers who are building web-enabled applications that will use the PWS to invoke an application to score a set of input data. To use the PWS, clients are required to have a valid  account with Valen. Please contact <support@valen.com> to ensure that access is properly configured.

##### Required Knowledge and Skills

Use of this guide assumes you are familiar with the following:

-   XML
-   Basic understanding of Web Services
-   Representational State Transfer (RESTful) architecture
-   A programming language for consuming a web service and any related
    tool

#### WebServices Application Flow

1.  Submit a request to a given application containing input data to score.
2.  Predict production system validates the input data and returns an error if problems are found.
3.  Predict production system scores the input data.
4.  Predict production system returns the scores and additional information explaining the scores.

Submitting data that is invalid or improperly formatted will return an error message. A list of common errors is provided below. For detailed responses, see [APPENDIX C: Error Handling](#appendixC). The errors in a failed scoring request will need to be addressed and the request will have to be resubmitted for scoring.

#### Application Processing Requirements
Requests to the PWS are designed as RESTful requests. Responses are provided for all models supported, i.e.*,* risk, misclassification and premium impact. The user cannot select independent models to be scored and or partial responses to be returned.

Web service requests are submitted as a POST request to the following URI in the Valen Domain:

`[server]/solutions/[solution]/[submission]`

For example:
`https://insureright.valen.com/solutions/insureright/scoring` - _a submission for workers compensation_
`https://insureright.valen.com/solutions/ca/scoring` - _a submission for commercial auto_

In the first example the **solution** is InsureRight. A number of different types of submissions may be made to the InsureRight solution, but in this case we are making a **scoring** submission, which will score the inputs against all InsureRight Predict models.

#### Request Contents

Two headers are required for successful request.

-   Content-Type – this can be either application/xml or text/xml
-   Authorization – username and password

The body of the request must conform to the standards defined in the Data Dictionary available in the Batch->Prepare page of your Valen account . This section describes the input data names, types, and constraints. The *Field Name* is used as a unique identifier when sending input data.

If the XML is a well-formed, UTF-8 request, the request will be validated.

#### Web Service Style

The PWS uses HTTPS and supports RESTful requests.

The PWS processes requests and returns results synchronously. The formatting and encoding style are document and literal, respectively. All requests must be secure requests.

### Common Validation Errors

A list of responses to common errors. Detailed information about the response messages can be found in [APPENDIX C: Error Handling](#appendixC).

|Code|Message|Probable Cause|
|----|-------|--------------|
|400|Bad Request|The xml is malformed or one of the root tags (<score> or <request>) is missing|
|500|InvalidInputValue|Some value is out of range or in the wrong format|
|500|Duplicate Class Key|There are two or more entries where the class code and state code match|
|500|Required Field Violation|A value is needed for a required field|
|500|Non-escaped special character|XML requires some special characters to be escaped|

### Common System Error Codes

The errors listed above all rely on the request reach the service and failing, usually during the validation of the request XML. There are other errors that will occur.

|Code|Message|Probable Cause|
|----|-------|-----------|
|400|SSL certificate error|Request is attempting authentication with a client certificate|
|401|Unauthorized|Bad username or password|
|403|Forbidden|User lacks the proper permissions to access feature|
|404|Not Found|Usually means the API doesn't exist|
|429|Too Many Requests|Too many requests have been made against the system in a short period of time|
|502|Bad Gateway|Valen Server is not responding|
|504|Gateway Timeout|Valen system is down|



### Building a Predict Web Service Scoring Request
