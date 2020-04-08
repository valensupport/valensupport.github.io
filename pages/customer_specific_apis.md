---
layout: page
title: InsureRight® Predict OpenAPI 3 Scoring Definitions
permalink: customer_specific_apis.html
sidebar: nav_sidebar
last_updated: April, 2020
summary: The webservices guide for the details on the exact schema for requests and responses.
---

# Valen Customer Specific OpenAPI 3 definition

### Integrating webservices on InsureRight® Platform 3.0

The InsureRight® Predict Web Service (PWS) allows clients to send input data, in the form of a prediction request, to the InsureRight platform for scoring with Valen’s models. Once received, the system validates the XML content of the transmission, scores the data, then returns a prediction response.

In addition to the score results, the system also returns information to explain the predictions in terms of influential data elements. Optionally, Valen can incorporate custom business rules to provide support for automated decision making based on the prediction results.

##### Audience

This guide is intended for use by developers who are building web-enabled applications that will use the PWS to invoke an application to score a set of input data. To use the PWS, clients are required to have a valid  account with Valen. Please contact <support@valen.com> to ensure that access is properly configured.

#### How to get access to your specific OpenAPI 3 definition

The payload definition for inputs and outputs will vary by customer.  In order to find out the exact inputs required, and the expected outputs in a response, a valid login to the Valen InsureRight platform is required.  Using your favorite browser, go to the Valen website at https://insureight.valen.com and login with your credentials.  Under the Tools option, select the API Documentation option.
Note the "Try it Out" option is only available for users that are designated as "test" users.  This prevents users with "production" credentials generating scores that would be included in analyzing model performance when using Manage.

![OpenAPI 3 Landing Page]({{ "/images/API1EntryPoint.PNG" | absolute_url }})

This is the complete set of API's available for integration purposes.

#### Navigating the OpenAPI 3 page

Valen has created 2 major revisions of the of the Valen API.  By default the api1 defintion will be displayed. For realtime scoring, all customers use the api1 version as of April 2020.  This API will not be depracated, and Valen will continue to support this interface.

The avaiable scoring API's will be presented based upon the Line of Business (LOB) a given customer has subscribed to. Click on any of the LOB's such as /solutions/insureright/scoring (which is for Workers' Compensation), and the schema for this particular scoring interface will be presented.

![Sample Workers' Compensation API 1]({{ "/images/SampleWCAPI.PNG" | absolute_url }})

##### Version: 
Valen maintains a complete version history of any solution presented to a customer.  By default, the API always selects the most recent version of the solution.  The only time the solution version should ever be incporated in a request is for future dated solutions.  See section <https://valensupport.github.io/web_services.html#appendix-f--testing-valen-future-dated-releases> for more information on using future dated solutions.

##### Format string
For api1, always leave this field empty and let the system default to XML. 

##### Request body
The OpenAPI3 supports two modes: the Schema mode and the Example Value mode.  The schema section defines each element, defines the data type, annotates with a "\*" if the element is required, and shows any constraints applied to the element.

![Sample Workers Compensation Request Body]({{ "/images/SchemaAndExample.PNG" | absolute_url }})

#### Schema

The Schema section consists of:

##### The request section
The following screen shots lists the first couple of inputs for a Workers' Compensation Request. The "down arrow" character can be toggled to collapse a section and hide information about that section. The ">" character can be toggled to expand a section to reveal more details acout that section.
   
![Sample Workers Compensation Inputs]({{ "/images/WCSampleInputs.PNG" | absolute_url }})
   
##### The response section
   The response section contains a list of all possible http return codes the content for each return code. The most common response is a return code of 200, representing a successful transaction. The stucture of any response follows the following format:
   
 Inputs: ( note: this repeats all inputs that were passed in the request )
   
![Sample Workers Compensation Response]({{ "/images/WCSampleOutputInput.PNG" | absolute_url }})
   
Outputs:  This the complete list of output elements sent back with each successful scoring transaction.
   
![Sample Workers Compensation Response]({{ "/images/WCSampleOutputOutputs.PNG" | absolute_url }})

Explanations:
   
![Sample Workers Compensation Response]({{ "/images/WCSampleOutputExplains.PNG" | absolute_url }})

Report Data:
   
![Sample Workers Compensation Response]({{ "/images/WCSampleOutputReportData.PNG" | absolute_url }})


#### Try it Out and Execute

The "Try it Out" button toggles from "Schema" mode into "Example Value" mode.  The "Example value" mode enables the user to manually edit the sample request with any value for each available input.  The "Execute" button will submit the request, and display the response directly below the request section. This enables the user of the API to try out inputs, and observe both sucessful and failing transactions.

![Sample Workers Compensation Response]({{ "/images/WCSampleTryitOutInputs.PNG" | absolute_url }})

The "Execute" button ( shown in the screen shot above ) submits the Request body with the example inputs to the InsureRight platform as a scoring request.  

#### Curl request 

The curl request can be copied from screen, and pasted into a command line tool to execute the exact same command the "Execute" button.

![Sample Workers Compensation Response]({{ "/images/WCSampleCurl.PNG" | absolute_url }})

#### Execution Response

![Sample Workers Compensation Response]({{ "/images/WCSampleTryitOutOutputs.PNG" | absolute_url }})

#### Appendix A - Disclaimers

InsureRight® Platform Predict Web Service Implementation Guide – Valen® Analytics, April 2020

All rights reserved. The copyright and trade secret laws of the United States and other countries protect this material. No part of this material or software covered by the copyrights herein may be reproduced, distributed or altered in any fashion without prior contractual or written consent of Valen® Analytics. Content is subject to change without notification.

##### Accuracy

Every effort was made to ensure the material contained in this document was complete and accurate at the time of publication. However, information is subject to change with or without notice.

##### Trademarks

All products, trademarks, registered trademarks, trade names, service marks and company names mentioned herein are the property of their respective owners.

-   InsureRight® Platform is a trademark of Valen® Analytics.
-   cURL and libcurl are Open Source/Free Software. The cURL license is
    copyrighted by Daniel Stenberg 1996 - 2013, <https://curl.haxx.se/docs/copyright.html>

Disclaimer

This guide is not intended to provide or substitute for managerial or legal advice. Users with legal or managerial concerns should seek the advice of a qualified professional.

##### Publication Date

April 7, 2020

| Revision | Date | Author(s) | Description |
|----------|------|-----------|-------------|
|1|04/07/2020|Valen Support|Initial version for Customer Specific OpenAPI 3 documentation|

