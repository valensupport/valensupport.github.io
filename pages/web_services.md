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

### Http Status Codes

A list of responses to common errors. Detailed information about the response messages can be found by following the instructions in [OpenAPI 3 Definitions for Customer Specific API](customer_specific_apis.html).



### Building a Predict Web Service Scoring Request

PWS Requests are **POST** requests to the URL constructed above.

The URL example as a reminder - `https://insureright.valen.com/solutions/insureright/scoring`

Two headers are required:
```http
Content-Type: Application/xml
Authorization: username/password in base64
```

The PWS Application requires data for each required relation inside an **inputs** node. For example, in Workers Compensation, we look for Insured and Class information. In order to handle multiple classes, the class information is nested inside the **input_child** node. A full example is provided in [Appendix A](#appendixA). More information about the fields is provided in the Data Dictionary available in the Batch->Prepare page of your Valen account. The basic layout for Request and Response packets is this:

##### InsureRight Request Requirements
```xml
<inputs level="insured">
	<inputChildren level="class">
		<class></class>
	</inputChildren>
</inputs>
```

##### Commercial Auto Request Requirements
```xml
<inputs level="insured">
    <inputChildren level="unit">
        <class></class>
    </inputChildren>
</inputs>
```

### Appendix A 

#### RESTful Request/Response Examples

<span id="_Toc412556468" class="anchor"></span>We use cURL or Postman to test our response content. See [Appendix E](#_Appendix_E_) for some examples.

##### Predict RESTful Request Example: 

The following example describes a valid Valen® PWS request.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
	<score version="1.0">
		<inputs level="insured">
			<policy_city_name>A CITY IN</policy_city_name>
			<agency>test123</agency>
			<underwriter>UNDER WRITER</underwriter>
			<audit_method_code>P</audit_method_code>
			<available_history_1>Y</available_history_1>
			<available_history_2>Y</available_history_2>
			<available_history_3>Y</available_history_3>
			<experience_mod_factor_initial>0.95</experience_mod_factor_initial>
			<insured_name>TEST SUBMISSION</insured_name>
			<new_renew_flag>N</new_renew_flag>
			<non_zero_claim_count_1>2</non_zero_claim_count_1>
			<non_zero_claim_count_2>3</non_zero_claim_count_2>
			<non_zero_claim_count_3>5</non_zero_claim_count_3>
			<original_policy_term_number>2015022404</original_policy_term_number>
			<policy_address>890 W MAIN AVE</policy_address>
			<policy_state_code>NC</policy_state_code>
			<policy_zip_code>27513</policy_zip_code>
			<term_effective_date>2019-02-01</term_effective_date>
			<inputChildren level="class">
				<class>
					<class_code>5645</class_code>
					<payroll_amount_initial>500000.0</payroll_amount_initial>
					<state_code>NC</state_code>
				</class>
				<class>
					<class_code>8810</class_code>
					<payroll_amount_initial>80000.0</payroll_amount_initial>
					<state_code>NC</state_code>
				</class>
			</inputChildren>
		</inputs>
	</score>
</request>
```


##### Predict Valid Response Example

If the request is well-formed, the input values can be converted to the required types and the data passes all constraints and exclusions, a response including all results is synchronously returned.

```xml

<?xml version='1.0' encoding='UTF-8'?>
<response xmlns="http://www.valentech.com/2013/11/prediction/response">
	<score version="1.0">
		<info>
			<score_key>40a45ce5-96f8-4195-9d6c-72bbc55f8384</score_key>
			<scoreId>26089282</scoreId>
		</info>
		<inputs xmlns="http://www.valentech.com/2013/11/prediction/inputs" level="insured">
			<policy_city_name>A CITY IN</policy_city_name>
			<underwriter>UNDER WRITER</underwriter>
			<non_zero_claim_count_1>2</non_zero_claim_count_1>
			<new_renew_flag>N</new_renew_flag>
			<policy_zip_code>27513</policy_zip_code>
			<non_zero_claim_count_2>3</non_zero_claim_count_2>
			<available_history_2>Y</available_history_2>
			<term_effective_date>2019-02-01</term_effective_date>
			<available_history_3>Y</available_history_3>
			<agency>test123</agency>
			<original_policy_term_number>2015022404</original_policy_term_number>
			<non_zero_claim_count_3>5</non_zero_claim_count_3>
			<policy_address>890 W MAIN AVE</policy_address>
			<available_history_1>Y</available_history_1>
			<audit_method_code>P</audit_method_code>
			<inputChildren level="class">
				<class xmlns="http://www.valentech.com/2013/11/prediction/class">
					<description>CARPENTRY-DETACHED ONE OR TWO FAMILY DWELLINGS</description>
					<class_code>5645</class_code>
					<payroll_amount_initial>500000.0</payroll_amount_initial>
					<state_code>NC</state_code>
				</class>
				<class xmlns="http://www.valentech.com/2013/11/prediction/class">
					<description>CLERICAL OFFICE EMPLOYEES NOC</description>
					<class_code>8810</class_code>
					<payroll_amount_initial>80000.0</payroll_amount_initial>
					<state_code>NC</state_code>
				</class>
			</inputChildren>
			<experience_mod_factor_initial>0.95</experience_mod_factor_initial>
			<policy_state_code>NC</policy_state_code>
			<insured_name>TEST SUBMISSION</insured_name>
		</inputs>
		<outputs xmlns="http://www.valentech.com/2013/11/prediction/outputs" level="insured">
			<pi_interpretation>Premium impact due to exposure misclassification is generally Return Premium greater than -$589</pi_interpretation>
			<loss_ratio_relativity_prediction>0.9035926479579973</loss_ratio_relativity_prediction>
			<risk_lower_bound>-0.1</risk_lower_bound>
			<term_effective_date>2019-02-01</term_effective_date>
			<misclass_upper_bound>0.12</misclass_upper_bound>
			<pi_bin>1</pi_bin>
			<pi_upper_bound>-589</pi_upper_bound>
			<original_policy_term_number>2015022404</original_policy_term_number>
			<misclass_lower_bound>0.1</misclass_lower_bound>
			<risk_upper_bound>-0.05</risk_upper_bound>
			<pi_prediction>-632.1288763500248</pi_prediction>
			<misclass_interpretation>% exposure misclassified for bin 8 generally falls between 10% - 12%</misclass_interpretation>
			<risk_bin>4</risk_bin>
			<misclass_bin>8</misclass_bin>
			<risk_interpretation>Prospective term loss ratios of policies in bin 4 are typically 5% to 10% lower than average within your data</risk_interpretation>
		</outputs>
		<explanations level="insured">
			<explanations xmlns="http://www.valentech.com/2013/11/prediction/explanations" name="exposure_shift_prediction">
				<explanation name="misclass_exp_class_count_onlevel_initial">
					<description>Class Count</description>
					<importance>0.44890255370224885</importance>
				</explanation>
				<explanation name="misclass_exp_governing_industry_group">
					<description>NCCI Governing Industry Group</description>
					<importance>0.14950016603175595</importance>
				</explanation>
				<explanation name="misclass_exp_governing_loss_cost_initial_onlevel">
					<description>Governing Class Loss Cost</description>
					<importance>0.12250271605389917</importance>
				</explanation>
				<explanation name="misclass_exp_combined_hazard_score">
					<description>Combined Weighted Average Hazard Score</description>
					<importance>-0.09579559669477344</importance>
				</explanation>
				<explanation name="misclass_exp_loss_cost_versus_governing_class_loss_cost">
					<description>Policy Weighted Avg Loss Cost vs. Governing Class Loss Cost</description>
					<importance>-0.021732211076713772</importance>
				</explanation>
			</explanations>
			<explanations xmlns="http://www.valentech.com/2013/11/prediction/explanations" name="loss_ratio_relativity_prediction">
				<explanation name="risk_exp_prem_per_claim_0">
					<description>Claim History</description>
					<importance>-0.46121601318194005</importance>
				</explanation>
				<explanation name="risk_exp_log_prem_per_claim">
					<description>Policy Size to Claim History Relativity</description>
					<importance>0.44401637296920254</importance>
				</explanation>
				<explanation name="risk_exp_scf_3yr_lr_cred">
					<description>Regional Loss Ratio History</description>
					<importance>0.043402629115432995</importance>
				</explanation>
				<explanation name="risk_exp_class_scf_3yr_freq_rel">
					<description>Class to Region Claim Frequency Relativity</description>
					<importance>0.026770181882594002</importance>
				</explanation>
				<explanation name="risk_exp_indus_3yr_manprem_freq_cred">
					<description>Industry and State Claim Frequency</description>
					<importance>-0.012569093808782612</importance>
				</explanation>
			</explanations>
			<explanations xmlns="http://www.valentech.com/2013/11/prediction/explanations" name="pi_score">
				<explanation name="pi_exp_zaftig1">
					<description>Exposure Weighting Across Lower Loss Cost Classes</description>
					<importance>0.496995729609913</importance>
				</explanation>
				<explanation name="pi_exp_premium">
					<description>Policy Premium</description>
					<importance>0.20875749040364214</importance>
				</explanation>
				<explanation name="pi_exp_weighted_loss_cost">
					<description>Policy Exposure Weighted Loss Cost</description>
					<importance>-0.19773409012033152</importance>
				</explanation>
				<explanation name="pi_exp_zaftig2">
					<description>Policy Loss Cost Weighting vs. Gov Class Average: Multi-Class</description>
					<importance>0.054389089200617136</importance>
				</explanation>
				<explanation name="pi_exp_zaftig2_sc">
					<description>Policy Loss Cost Weighting vs. Gov Class Average: Single-Class</description>
					<importance>-0.02166618593217182</importance>
				</explanation>
			</explanations>
		</explanations>
		<reportData level="insured">
			
			**** Note that Class Code Comparison corresponds to the section of the 
			**** InsureRight Predict report labeled "Companion Class Results"
			
			<classCodeComparison xmlns="http://www.valentech.com/2013/11/prediction/comparison">
				<class type="governing" sortOrder="0">
					<stateCode>NC</stateCode>
					<classCode>5645</classCode>
					<description>CARPENTRY-DETACHED ONE OR TWO FAMILY DWELLINGS</description>
					<percentOfPayroll>0.7708548</percentOfPayroll>
					<percentOfObservations>1</percentOfObservations>
				</class>
				<class type="associated" sortOrder="1">
					<stateCode>NC</stateCode>
					<classCode>8810</classCode>
					<description>CLERICAL OFFICE EMPLOYEES NOC</description>
					<percentOfPayroll>0.25097278</percentOfPayroll>
					<percentOfObservations>0.32135373</percentOfObservations>
				</class>
				<class type="associated" sortOrder="2">
					<stateCode>NC</stateCode>
					<classCode>5474</classCode>
					<description>PAINTING OR PAPERHANGING NOC &amp; SHOP OPERATIONS, DRIVERS.</description>
					<percentOfPayroll>0.06497217</percentOfPayroll>
					<percentOfObservations>0.094911575</percentOfObservations>
				</class>
				<class type="associated" sortOrder="3">
					<stateCode>NC</stateCode>
					<classCode>5606</classCode>
					<description>CONTRACTOR-PROJECT MANAGER, CONSTRUCTION EXECUTIVE, CONSTRUCTION MANAGER OR CONSTRUCTION SUPERINTENDENT(N/A AK, MA, MT, OR)</description>
					<percentOfPayroll>0.11701692</percentOfPayroll>
					<percentOfObservations>0.0934777</percentOfObservations>
				</class>
				<class type="associated" sortOrder="4">
					<stateCode>NC</stateCode>
					<classCode>8742</classCode>
					<description>SALESPERSONS OR COLLECTORS-OUTSIDE(N/A MA)</description>
					<percentOfPayroll>0.23289026</percentOfPayroll>
					<percentOfObservations>0.06926725</percentOfObservations>
				</class>
				<class type="associated" sortOrder="5">
					<stateCode>NC</stateCode>
					<classCode>5437</classCode>
					<description>CARPENTRY-INSTALLATION OF CABINET WORK OR INTERIOR TRIM</description>
					<percentOfPayroll>0.059734803</percentOfPayroll>
					<percentOfObservations>0.063825876</percentOfObservations>
				</class>
				<class type="associated" sortOrder="6">
					<stateCode>NC</stateCode>
					<classCode>5022</classCode>
					<description>MASONRY NOC</description>
					<percentOfPayroll>0.04138014</percentOfPayroll>
					<percentOfObservations>0.06373396</percentOfObservations>
				</class>
				<class type="associated" sortOrder="7">
					<stateCode>NC</stateCode>
					<classCode>5403</classCode>
					<description>CARPENTRY-NOC</description>
					<percentOfPayroll>0.03959958</percentOfPayroll>
					<percentOfObservations>0.05814552</percentOfObservations>
				</class>
			</classCodeComparison>
			
			**** Note that governingClassSwapResults corresponds to the section of the 
			**** InsureRight Predict report labeled "Governing Class Swap Results"
			<governingClassSwapResults xmlns="http://www.valentech.com/2013/11/prediction/swap">
				<class type="governing" sortOrder="0">
					<agencyCode>NCCI</agencyCode>
					<classCode>5645</classCode>
					<description>CARPENTRY-DETACHED ONE OR TWO FAMILY DWELLINGS</description>
					<percentOfOccurrence>0.92935354</percentOfOccurrence>
					<percentOfPremiumImpact>0.03213718</percentOfPremiumImpact>
				</class>
				<class type="associated" sortOrder="1">
					<agencyCode>NCCI</agencyCode>
					<classCode>5606</classCode>
					<description>CONTRACTOR-PROJECT MANAGER, CONSTRUCTION EXECUTIVE, CONSTRUCTION MANAGER OR CONSTRUCTION SUPERINTENDENT(N/A AK, MA, MT, OR)</description>
					<percentOfOccurrence>0.012896067</percentOfOccurrence>
					<percentOfPremiumImpact>-0.3268211</percentOfPremiumImpact>
				</class>
				<class type="associated" sortOrder="2">
					<agencyCode>NCCI</agencyCode>
					<classCode>5437</classCode>
					<description>CARPENTRY-INSTALLATION OF CABINET WORK OR INTERIOR TRIM</description>
					<percentOfOccurrence>0.009512896</percentOfOccurrence>
					<percentOfPremiumImpact>-0.23217556</percentOfPremiumImpact>
				</class>
				<class type="associated" sortOrder="3">
					<agencyCode>NCCI</agencyCode>
					<classCode>5403</classCode>
					<description>CARPENTRY-NOC</description>
					<percentOfOccurrence>0.008512496</percentOfOccurrence>
					<percentOfPremiumImpact>-0.03509642</percentOfPremiumImpact>
				</class>
				<class type="associated" sortOrder="4">
					<agencyCode>NCCI</agencyCode>
					<classCode>5474</classCode>
					<description>PAINTING OR PAPERHANGING NOC &amp; SHOP OPERATIONS, DRIVERS.</description>
					<percentOfOccurrence>0.0058932663</percentOfOccurrence>
					<percentOfPremiumImpact>-0.1855608</percentOfPremiumImpact>
				</class>
				<class type="associated" sortOrder="5">
					<agencyCode>NCCI</agencyCode>
					<classCode>5221</classCode>
					<description>CONCRETE OR CEMENT WORK-FLOORS, DRIVEWAYS, YARDS OR SIDEWALKS &amp; DRIVERS(N/A MA)</description>
					<percentOfOccurrence>0.0031649023</percentOfOccurrence>
					<percentOfPremiumImpact>-0.06355467</percentOfPremiumImpact>
				</class>
				<class type="associated" sortOrder="6">
					<agencyCode>NCCI</agencyCode>
					<classCode>5022</classCode>
					<description>MASONRY NOC</description>
					<percentOfOccurrence>0.0026010405</percentOfOccurrence>
					<percentOfPremiumImpact>-0.011365756</percentOfPremiumImpact>
				</class>
				<class type="associated" sortOrder="7">
					<agencyCode>NCCI</agencyCode>
					<classCode>OTHR</classCode>
					<description/>
					<percentOfOccurrence>0.02806577</percentOfOccurrence>
					<percentOfPremiumImpact>-0.14356913</percentOfPremiumImpact>
				</class>
			</governingClassSwapResults>
		</reportData>
	</score>
</response>
```



### Appendix B – Interpreting the “Importance” value

The **Importance** value provides the underwriter information describing the relative influence of the model equation predictive variables on a specific policy’s prediction, given that policy’s specific predictive variable values. The importance values may be interpreted as follows:

**Sign:**

-   A negative sign for a variable indicates that this variable’s
    specific value increased the policy’s relative loss ratio
    prediction; a positive sign for a variable indicates that this
    variable’s specific value decreased the policy’s     relative loss
    ratio prediction.
-   Semantically, one can think of sign as follows: “negative is bad,
    positive is good”.

**Magnitude:**
-   The magnitude of a given importance value for a given variable can
    be thought of as a relative measure for a given policy: given a
    policy’s prediction, how much did a given variable’s value
    contribute to that policy’s prediction, compared to the
    contributions of the other variables and their values for that
    policy?
-   Because the importance values are normalized separately for each
    policy:
--   Importance values can be loosely thought of as that variable’s
        percent of impact on the prediction for that policy\*.
--   Importance values should not be compared from policy to policy.

**Example 1:**

-   A value of -12 for a given variable may be loosely interpreted as
    follows: the specific scoring value of this predictive variable
    accounted for about 12% of the prediction outcome that occurred for
    this policy, and the value of this variable indicated a higher
    relative loss ratio along this variable’s predictive dimension.

**Example 2:**

-   Suppose policy ABC with predictor variables X1 and X2 has importance
    values for X1 and X2 of 6 and -12, respectively. Then the scoring
    value of variable X2 had approximately twice the influence on the
    policy ABC’s prediction than did the scoring value of variable X1,
    for this particular policy. Note from the respective signs of the
    importance values that the scoring value for X1 indicated that the
    policy is lower risk than average along X1’s predictive dimension,
    while the scoring value for X2 indicated that the policy is higher
    risk than average along X2’s predictive dimension.

<a name="appendixC" id="appendixC"></a>

### Appendix C – Error Handling

If the Content Type of the request is not application/json, application/xml, or text/xml an Unsupported Media Type (HTTP415) status code will be returned.

If credentials are invalid an Unauthorized (HTTP 401) status code will be returned.

If the credentials are valid, but the user is not authorized to make a web service request for the specified customer, solution and submission, then a Forbidden (HTTP 403) status code will be returned.

##### Authentication:

The request should contain a HTTP Basic Authentication header containing a user name and valid credentials. If credentials are invalid an Unauthorized (HTTP 401) status code will be returned.

The form of the authentication header will be like this: `Authentication: Basic ZmFrZW5hbWVAZmFrZS5jb206d2h5ZGlkeW91ZGVjb2RldGhpcw==`

The string after Basic is encoding in Base64. Most HTTP request libraries will be able to handle the creation of this header from the username and password but make sure the form is being passed correctly in the request.

##### Special characters in XML: 

The following are special characters and how to address them in XML (note the Failed Examples will throw a **500: Internal Server Error**):

|Special Character|Escape code|Failed example|Proper Example|
|-----------------|-----------|--------------|--------------|
|&|`&amp;`|`<app:insured\_name value="Joe Rockhead & Sons">`|`<app:insured_name value="Joe Rockhead &amp; Sons"/>`|
|" (quote)|`&quot;`|`<app:insured_name value="Joe Rockhead at "DBA Rockhead Quarry""/>`|`<app:insured_name value="Joe Rockhead at &quot;DBA Rockhead Quarry&quot;"/>`|
|>|`&gt;`|`<app:insured\_name value="Joe Rockhead > Sons">`|`<app:insured_name value="Joe Rockhead &gt; Sons"/>`|
|<|`&lt;`|`<app:insured\_name value="Joe Rockhead < Sons">`|`<app:insured_name value="Joe Rockhead &lt; Sons"/>`|
|' (apostrophe)|`&apos;`|`<app:insured\_name value="Bob's Bar and Grill">`|`<app:insured_name value="Bob&apos;s Bar and Grill"/>`|


#### Predict RESTful Request –  Error Examples

Refer to [OpenAPI 3 Definitions for Customer Specific API](customer_specific_apis.html) for how to access up to date information about error examples.



### Appendix D – *Predict* Report Download ( Legacy API, see Appendix E for the current API )

It is possible to pull Predict Reports from the InsureRight system and store them locally in PDF format.

##### PWS Predict Report Download Flow

In the response to a valid submission there is a Score ID.


1.  The client submits a web service request for the report with a format=pdf parameter

2.  The report is delivered as base64 text in the response.

3.  Decoding the Base64 decoder translates the file to binary which can be saved in PDF format


##### PWS Predict Report Download Requirements

Requests to the PWS are designed as RESTful requests. Responses contain the report data in binary format.

Web service requests are submitted as an HTTP GET to the following URL:

`[server]/solutions/[solution]/scoring/[reportID or scoreID]`

For example:

`https://insureright.valen.com/solutions/insureright/scoring/12345?format=pdf`

In this example the **solution** is InsureRight. A number of different types of submissions may be made to the InsureRight solution, but in this case we are making a **scoring** submission, which requires the ‘reportID’ or ‘scoreID’ be sent to the production system.

##### Request Data

The PWS request must conform to the following standards:

Method = GET

Endpoint = `<https://insureright.valen.com>`

Resource = /solutions/insureright/scoring/{scoreID}

Parameters = ?format=pdf

**Web Service Style:** 
The PWS uses HTTPS and supports RESTful requests. The PWS processes requests and returns results synchronously.

**Authentication:** 
The request should contain a HTTP Basic Authentication header containing a user name and valid credentials. If credentials are invalid an Unauthorized (HTTP 403) status code will be returned.

##### PDF Request example

This is a simple retrieval script written in Python.

```python
import requests
import base64
import xml.etree.ElementTree as ET 
import sys

if len(sys.argv) < 4:
    print "Please username, password, and a list of scoreIDs"
    sys.exit(1)

user = sys.argv[1]
pwd = sys.argv[2]
id_list = sys.argv[3:]

for item in id_list:
    req_url = 'https://insureright.valen.com/solutions/insureright/scoring/%s?format=pdf' % item
    filename = "retrieved_%s.pdf" % item

    r = requests.get(req_url, auth=(user, pwd))
    print r.status_code

    root = ET.fromstring(r.text)
    for child in root:
        print child.tag, child.attrib

    with open(filename, 'wb') as fout:
        fout.write(base64.b64decode(root[0].text))
```

### Appendix E – *Predict* Report Download ( Current API recommended for new development )

It is possible to pull Predict Reports from the InsureRight system and store them locally in PDF format.   A full 1 or 2 page report is available using the 'predict' option, and an abberviated report is available using the 'lite' option using the 'api/3/reports' URL. The 'lite' option is only available for Workers' Compensation and Commercial Auto lines of business. Visit the API documentation located under the Tools option at insureright.valen.com for more examples on using the reports API.

##### PWS Predict Report Download Flow

In the response to a valid submission there is a score_id or score_key.


1.  The client submits a web service request for the report with a format=pdf parameter

2.  The report is delivered as base64 text in the response.

3.  Decoding the Base64 decoder translates the file to binary which can be saved in PDF format


##### PWS Predict Report Download Requirements

Requests to the PWS are designed as RESTful requests. Responses contain the report data in binary format.

Web service requests are submitted as an HTTP GET to the following URL:

`[server]/api/2/reports/[solution]/scoring/[score_id]/predict` OR
`[server]/api/3/reports/[solution]/scoring/[score_key]/[predict][lite]`

For example:

`https://insureright.valen.com/api/2/reports/insureright/scoring/12345/predict`

In this example the **solution** is InsureRight. A number of different types of submissions may be made to the InsureRight solution, but in this case we are making a **scoring** submission, which requires the ‘score_id’ be sent to the production system.

##### Request Data

The PWS request must conform to the following standards:

Method = GET

Endpoint = `<https://insureright.valen.com>`

Resources = `/solutions/insureright/scoring/{score_id}/lite` OR
            `/solutions/insureright/scoring/{score_key}/predict`

**Web Service Style:** 
The PWS uses HTTPS and supports RESTful requests. The PWS processes requests and returns results synchronously.

**Authentication:** 
The request should contain a HTTP Basic Authentication header containing a user name and valid credentials. If credentials are invalid an Unauthorized (HTTP 403) status code will be returned.

### Appendix F – Test Tools

We use cURL or Postman to test our request content.

The curl utility can be downloaded at <http://curl.haxx.se/download.html> for various operating systems.

Postman is an app for Chrome or Mac and can be found at <https://www.getpostman.com/>

##### Curl

Given a valid xml file (see the example request in [Appendix A](#_Appendix_A_-)), this is a sample request.

`curl –u \[username\]:\[password\]` `-X POST -d @valid-request.xml -H 'Content-Type: application/xml'` `https://insureright.valen.com/solutions/insureright/scoring`

###### PostmFn

We have a library of sample requests at <https://www.getpostman.com/collections/da164d2bbcfb55623635>

Import the library into Postman, click on the Authorization tab then follow these steps.

1.  Enter the username and password for your Valen webservices user
    account

2.  Hit Update Request

3.  Click the “Body” tab

While on the “Body” Tab, enter valid values into the xml (you may need to select the “Raw” radio tab to see the xml).

Hit Send.

### Appendix F – Testing Valen Future Dated Releases

Valen supports future dated releases in the insureright.valen.com environment.  This allows two different releases to be available at the same time in this production environment. The future date refers to a date and time that the release will automatically become the release available to all users of the customer.

A common use case involves some customer acceptance testing of a release before it becomes available to all end users of the customer. A future dated release is only available via the Web services interface. The User Interface for InsureRight is not capable of accessing a future dated release.

To access a future dated release via a web service:

`curl -u \[username\]:\[password\]` `-X POST -d @valid-request.xml -H 'Content-Type: application/xml'` `https://insureright.valen.com/solutions/insureright/scoring?solutionVersion=1.0.7`
	
To access the current active release available to all users:

`curl –u \[username\]:\[password\]` `-X POST -d @valid-request.xml -H 'Content-Type: application/xml'` `https://insureright.valen.com/solutions/insureright/scoring`
	
All of the URL's referenced in this document can take the optional parameter of solutionVersion. If you are testing a future dated release, it is a best practive to authenticate with a test user so any scores created are not considered production scores.
	
Your Valen customer engagement manager will provide the version to use, as well as inform you of the date a future dated release will become active for all of your users.

### Appendix G - Disclaimers

InsureRight® Platform Predict Web Service Implementation Guide – Valen® Analytics, February 2015

All rights reserved. The copyright and trade secret laws of the United States and other countries protect this material. No part of this material or software covered by the copyrights herein may be reproduced, distributed or altered in any fashion without prior contractual or written consent of Valen® Analytics. Content is subject to change without notification.

##### Accuracy

Every effort was made to ensure the material contained in this document was complete and accurate at the time of publication. However, information is subject to change with or without notice.

##### Trademarks

All products, trademarks, registered trademarks, trade names, service marks and company names mentioned herein are the property of their respective owners.

-   InsureRight® Platform is a trademark of Valen® Analytics.
-   Linux® is the registered trademark of Linus Torvalds in the U.S. and other countries.
-   Microsoft®, Windows is a registered trademark of Microsoft
    Corporation in the United States and/or other countries.
-   W3Schools.com© is copyrighted by Refsnes Data 1999-2009. All Rights
    Reserved
-   cURL and libcurl are Open Source/Free Software. The cURL license is
    copyrighted by Daniel Stenberg 1996 - 2013, <https://curl.haxx.se/docs/copyright.html>

Disclaimer

This guide is not intended to provide or substitute for managerial or legal advice. Users with legal or managerial concerns should seek the advice of a qualified professional.

##### Publication Date

November 13, 2018

| Revision | Date | Author(s) | Description |
|----------|------|-----------|-------------|
|1|02/24/2015|Valen Support|Initial version for InsureRight 3.0 Predict Platform|
|1.1|7/6/2016|Valen Support|Updated the examples to reflect code improvements.|
|1.2|8/2/2016|Valen Support|Updated the error messaging|
|1.3|11/14/2016|Valen Support|Converted to web document|
|1.4|7/7/2017|Valen Support|Updated the sample responses|
|1.5|11/13/2018|Valen Support|Added WC Claims and Future Dated Releases|
|1.6|6/16/2020|Valen Support|Referring to OpenAPI 3 online docs for current https status codes|
