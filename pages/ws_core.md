---
layout: page
title: InsureRight® Predict Web Services
permalink: webservices_guide.html
sidebar: mydoc_sidebar
last_updated: December, 2016
summary: Intro to Valen Webservices integration
---

# Valen WebServices Implementation Guide

### Integrating webservices for Predict on InsureRight® Platform 3.0

The InsureRight® Predict Web Service (PWS) allows clients to
send input data, in the form of a prediction request, to the InsureRight platform for scoring with Valen’s models. Once received, the system validates the XML and data content of the transmission, scores the data, then returns a prediction response.

In addition to the score results the system also returns information to
explain the predictions in terms of influential data elements and,
optionally, Valen can incorporate custom business rules to provide
support for automated decision making based on the prediction results.

##### Audience

This guide is intended for use by developers who are building
web-enabled applications that will use the PWS to invoke an application
to score a set of input data. To use the PWS, clients are required to
have a valid PWS account. Please contact <support@valen.com> to ensure
that access is properly configured.

##### Required Knowledge and Skills

Use of this guide assumes you are familiar with the following:

-   XML
-   Basic understanding of Web Services
-   Representational State Transfer (RESTful) architecture
-   A programming language for consuming a web service and any related
    tool

### Web Services Application 

Valen's web services address interoperability concerns by giving clients a way to easily send their data to the InsureRight production system and receive
scores and predictions generated using their data via HTTP.

#### Web Services Application Flow

When valid data is submitted, the Web Services works as follows:
1.  The client sends a request to a given solution containing
    input data to score.
2.  System validates the input data.
3.  System scores the input data.
4.  System returns the scores and additional information explaining the scores.

Submitting data that is invalid or improperly formatted will return an error message. A list of common errors is provided below. For detailed responses, see [Common Errors](#common-errors). The errors in a failed scoring request will need to be addressed and the request will have to be resubmitted for scoring.

#### Application Processing Requirements
Requests to the Web Services are designed as RESTful requests. Responses are
provided for all models supported, e.g., risk, misclassification and
premium impact. The user cannot select independent models to be scored
and or partial responses to be returned.

Web service requests are submitted as a POST request to the following
URL:

`[server]/solutions/[solution]/[submission]`

For example:
`https://insureright.valen.com/solutions/insureright/scoring`
`https://insureright.valen.com/solutions/ca/scoring`
`https://insureright.valentech.com/solutions/batch/5/profile`

In the first example the **solution** is InsureRight, our workers compensation product. A number of different
types of submissions may be made to the InsureRight solution, but in this case we are making a **scoring** submission, which will score the inputs against all InsureRight Predict models.

The second example is similar in that it's a scoring request, but the **solution** is our Commercial Auto product.

The third example is a batch request. This would be used to upload a group of files containing data records to be scored.

#### Request Contents

Two headers are required for successful request.

Two headers are required:
```http
Content-Type: Application/xml
Authorization: username/password in base64
```

The body of the request must conform to the standards defined in the Data Dictionary found in the solution specific subsections. These dictionaries describe the input data names, types, and constraints for a given solution. The *Field Name* is used as a unique identifier when sending input data. The web-service WSDL also defines the input data names and constraints but we discourage its use due to the brittle nature of WSDL development.

If the XML is a well-formed, UTF-8 request, the request will be
validated.

#### Web Service Style

The web service uses HTTPS and supports RESTful requests.

The web service processes requests and returns results synchronously. The
formatting and encoding style are document and literal, respectively.
All requests must be secure requests.

### Common Errors

A list of responses to common errors followed by some more detailed information about the response messages.

##### Bad Request

This will return a status code of 400 with a body containing the
request.

##### Constraint Violation

This will return a status code of 500 with an invalidInputValue tag.

##### Duplicate Class Code

This will return a status code of 500 with an uniqueKey tag marked as a
validation error.

##### Required Field Violation

This will return a status code of 500 with a description field that contains the name of the field that was missing a value.

##### Credential violation

The request should contain a HTTP Basic Authentication header containing
a user name and valid password.

If credentials are invalid an Unauthorized (HTTP 401) status code will
be returned.

If the credentials are valid, but the user is not authorized to make a
web service request for the specified customer, solution and submission,
then a Forbidden (HTTP 403) status code will be returned.

##### Non-escaped special character

Special characters in XML: The following are special characters and how
to address them in XML (note the Failed Examples will throw a **500:
Internal Server Error**):

|Special Character|Escape code|Failed example|Proper Example|
|-----------------|-----------|--------------|--------------|
|&|`&amp;`|`<app:insured\_name value="Joe Rockhead & Sons">`|`<app:insured_name value="Joe Rockhead &amp; Sons"/>`|
|" (quote)|`&quot;`|`<app:insured_name value="Joe Rockhead "DBA Rockhead Quarry""/>`|`<app:insured_name value="Joe Rockhead &quot;DBA Rockhead Quarry&quot;"/>`|

##### Content Type

If the Content Type of the request is not application/json,
application/xml, or text/xml an Unsupported Media Type (HTTP415) status
code will be returned.

#### Predict RESTful Request – Generated Error Examples

##### Bad Request

If the request XML is not well-formed a Bad Request (HTTP 400) status
code containing an illFormedRequest body will be returned including the
ill-formed request:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
   <info>
      <scoreKey>65497dc3-8064-46ce-9234-a1f0d9ee0f00</scoreKey>
   </info>
   <illFormedRequest>
     <request>
        <![CDATA[<score>
            <!-- literal request -->
        </score>]]>
     </request>
   </illFormedRequest>
</response>
```

##### Constraint Violation

```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <info>
        <scoreKey>56ab2352-b9ba-49c8-be28-0f3b2e484d79</scoreKey>
    </info>
    <invalidRequest>
        <request>
        <![CDATA[
            <!-- literal request -->
            ]]>
        </request>
    </invalidRequest>
    <errors count="1">
        <validationError>
            <name>invalidInputValue</name>
            <description>The value for 'non_zero_claim_count_3' is 'Y' which is not the specified type: 'integer'</description>
            <level>insured</level>
            <key>
                <vglItm>:term_effective_date</vglItm>
                <vglItm>2014-01-01</vglItm>
                <vglItm>:original_policy_term_number</vglItm>
                <vglItm>test 3.0</vglItm>
            </key>
            <field>nonZeroClaimCount3</field>
            <value>Y</value>
        </validationError>
    </errors>
</response>
```

##### Duplicate Class Code

```xml
<?xml version='1.0' encoding='UTF-8'?>
<response>
    <info>
        <scoreKey>74785202-1851-4350-bc30-23e132611f60</scoreKey>
    </info>
    <invalidRequest>
        <request>
        <![CDATA[
        <!-- literal request -->
           ]]>
        </request>
    </invalidRequest>
    <errors count="1">
        <validationError>
            <name>uniqueKey</name>
            <description>keys are repeated multiple(x) times</description>
            <field/>
            <value/>
        </validationError>
    </errors>
</response>
```

##### Required Field Violation

```xml
<?xml version='1.0' encoding='UTF-8'?>
<response>
    <info>
        <scoreKey>746c4a66-f1cf-4bc4-9064-0bd877a8001d</scoreKey>
    </info>
    <invalidRequest>
        <request><![CDATA[
            <!-- literal request -->
            ]]></request>
    </invalidRequest>
    <errors count="3">
        <validationError>
            <name>required</name>
            <description>A required value for 'term_effective_date' was not provided</description>
            <level>class</level>
            <key>
                <vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>lr_2.55-plrr_adj 2.30-plrr_adj_flr 2.30</vglItm>
                    </vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>lr_2.55-plrr_adj 2.30-plrr_adj_flr 2.30</vglItm>
                    </vglItm>
                    <vglItm>
                        <vglItm>:class_code</vglItm>
                        <vglItm>8810</vglItm>
                    </vglItm>
                    <vglItm>
                        <vglItm>:state_code</vglItm>
                        <vglItm>KY</vglItm>
                    </vglItm>
                </vglItm>
            </key>
            <field>termEffectiveDate</field>
            <constraint>requiredField</constraint>
        </validationError>
        <validationError>
            <name>required</name>
            <description>A required value for 'term_effective_date' was not provided</description>
            <level>class</level>
            <key>
                <vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>lr_2.55-plrr_adj 2.30-plrr_adj_flr 2.30</vglItm>
                    </vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>lr_2.55-plrr_adj 2.30-plrr_adj_flr 2.30</vglItm>
                    </vglItm>
                    <vglItm>
                        <vglItm>:class_code</vglItm>
                        <vglItm>2587</vglItm>
                    </vglItm>
                    <vglItm>
                        <vglItm>:state_code</vglItm>
                        <vglItm>KY</vglItm>
                    </vglItm>
                </vglItm>
            </key>
            <field>termEffectiveDate</field>
            <constraint>requiredField</constraint>
        </validationError>
        <validationError>
            <name>required</name>
            <description>A required value for 'term_effective_date' was not provided</description>
            <level>insured</level>
            <key>
                <vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>lr_2.55-plrr_adj 2.30-plrr_adj_flr 2.30</vglItm>
                    </vglItm>
                </vglItm>
            </key>
            <field>termEffectiveDate</field>
            <constraint>requiredField</constraint>
        </validationError>
    </errors>
</response>
```

##### Credential Violation

```xml
<data contentType="null" contentLength="16"><![CDATA[401:Unauthorized]]></data>
```

### Appendix A – Test Tools

We use cURL or Postman to test our request content.

The curl utility can be downloaded at
<http://curl.haxx.se/download.html> for various operating systems.

Postman is an app for Chrome or Mac and can be found at
<https://www.getpostman.com/>

##### Curl

Given a valid xml file (see the example request above), this is a sample request.

curl –u \[username\]:\[password\] -X POST -d @valid-request.xml -H
'Content-Type: application/xml'
[https://insureright.valen.com/solutions/&lt;customer&gt;insureright/scoring](https://insureright.valen.com/solutions/%3ccustomer%3einsureright/scoring)

###### Postman

We have a libraries of sample requests that can be found under the different solution pages. If you want to try it out, here's the collection for InsureRight. Follow the steps below to get started.
<https://www.getpostman.com/collections/da164d2bbcfb55623635>

Import the library into Postman, click on the Authorization tab then
follow these steps.

1.  Enter the username and password for your Valen webservices user
    account

2.  Hit Update Request

3.  Click the “Body” tab

While on the “Body” Tab, enter valid values into the xml (you may need
to select the “Raw” radio tab to see the xml).

Hit Send.

### Appendix B - Disclaimers and Legalese

InsureRight® Platform Predict Web Service Implementation Guide – Valen®
Analytics, February 2015

All rights reserved. The copyright and trade secret laws of the United States and other countries protect this material. No part of this material or software covered by the copyrights herein may be reproduced, distributed or altered in any fashion without prior contractual or written consent of Valen® Analytics. Content is subject to change without notification.

##### Accuracy

Every effort was made to ensure the material contained in this document
was complete and accurate at the time of publication. However,
information is subject to change with or without notice.

##### Trademarks

All products, trademarks, registered trademarks, trade names, service
marks and company names mentioned herein are the property of their
respective owners.

-   InsureRight® Platform is a trademark of Valen® Analytics.
-   Linux® is the registered trademark of Linus Torvalds in the U.S. and other countries.
-   Microsoft®, Windows is a registered trademark of Microsoft
    Corporation in the United States and/or other countries.
-   W3Schools.com© is copyrighted by Refsnes Data 1999-2009. All Rights
    Reserved
-   cURL and libcurl are Open Source/Free Software. The cURL license is
    copyrighted by Daniel Stenberg 1996 - 2013, <https://curl.haxx.se/docs/copyright.html>

Disclaimer

This guide is not intended to provide or substitute for managerial or
legal advice. Users with legal or managerial concerns should seek the
advice of a qualified professional.