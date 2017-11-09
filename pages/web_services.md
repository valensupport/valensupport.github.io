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

The body of the request must conform to the standards defined in the [Data Dictionary Requirements](#data-dictionary) section of this document. This section describes the input data names, types, and constraints. The *Field Name* is used as a unique identifier when sending input data. The web-service WSDL also defines the input data names and constraints.

If the XML is a well-formed, UTF-8 request, the request will be validated.

#### Web Service Style

The PWS uses HTTPS and supports RESTful requests.

The PWS processes requests and returns results synchronously. The formatting and encoding style are document and literal, respectively. All requests must be secure requests.

### Common Errors

A list of responses to common errors. Detailed information about the response messages can be found in [APPENDIX C: Error Handling](#appendixC).

##### Bad Request

This will return a status code of 400 with a body containing the request.

##### Constraint Violation

This will return a status code of 500 with an invalidInputValue tag.

##### Duplicate Class Code

This will return a status code of 500 with an uniqueKey tag marked as a validation error.

##### Required Field Violation

This will return a status code of 500 with a description field that contains the name of the field that was missing a value.

##### Credential violation

This will return a 401 Unauthorized

##### Non-escaped special character

Special characters in the XML will return a status code of 500.


### Building a Predict Web Service Scoring Request

PWS Requests are **POST** requests to the URL constructed above.

The URL example as a reminder - `https://insureright.valen.com/solutions/insureright/scoring`

Two headers are required:
```http
Content-Type: Application/xml
Authorization: username/password in base64
```

The PWS Application requires data for each required relation inside an **inputs** node. For example, in Workers Compensation, we look for Insured and Class information. In order to handle multiple classes, the class information is nested inside the **input_child** node. A full example is provided in [Appendix A](#appendixA). More information about the fields is provided in the [Data Dictionary](#dataDict). The basic layout for Request and Response packets is this:

##### Request Requirements
```xml
<inputs level="insured">
	<inputChildren>
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
            <experience_mod_factor_initial>0.25</experience_mod_factor_initial>
            <insured_name>TEST SUBMISSION</insured_name>
            <new_renew_flag>N</new_renew_flag>
            <non_zero_claim_count_1>0</non_zero_claim_count_1>
            <non_zero_claim_count_2>0</non_zero_claim_count_2>
            <non_zero_claim_count_3>0</non_zero_claim_count_3>
            <original_policy_term_number>2015022404</original_policy_term_number>
            <policy_address>890 W MAIN AVE</policy_address>
            <policy_state_code>CO</policy_state_code>
            <policy_zip_code>81601</policy_zip_code>
            <term_effective_date>2015-07-04</term_effective_date>
            <inputChildren level="class">
                <class>
                    <class_code>2416</class_code>
                    <payroll_amount_initial>250000</payroll_amount_initial>
                    <state_code>CO</state_code>
                </class>
                <class>
                    <class_code>2220</class_code>
                    <payroll_amount_initial>50000</payroll_amount_initial>
                    <state_code>UT</state_code>
                </class>
                <class>
                    <class_code>8742</class_code>
                    <payroll_amount_initial>50000</payroll_amount_initial>
                    <state_code>UT</state_code>
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
            <scoreKey>b7266a49-1afa-4090-b81c-4ec2456ac097</scoreKey>
            <scoreId>8051346</scoreId>
        </info>
        <inputs xmlns="http://www.valentech.com/2013/11/prediction/inputs" level="insured">
            <policy_city_name>A CITY IN</policy_city_name>
            <underwriter>UNDER WRITER</underwriter>
            <non_zero_claim_count_1>0</non_zero_claim_count_1>
            <new_renew_flag>N</new_renew_flag>
            <policy_zip_code>81601</policy_zip_code>
            <non_zero_claim_count_2>0</non_zero_claim_count_2>
            <available_history_2>Y</available_history_2>
            <term_effective_date>2015-07-04</term_effective_date>
            <available_history_3>Y</available_history_3>
            <agency>test123</agency>
            <original_policy_term_number>2015022404</original_policy_term_number>
            <non_zero_claim_count_3>0</non_zero_claim_count_3>
            <policy_address>890 W MAIN AVE</policy_address>
            <available_history_1>Y</available_history_1>
            <audit_method_code>P</audit_method_code>
            <inputChildren level="class">
                <class xmlns="http://www.valentech.com/2013/11/prediction/class">
                    <description>YARN DYEING OR FINISHING.</description>
                    <class_code>2416</class_code>
                    <payroll_amount_initial>250000.0</payroll_amount_initial>
                    <state_code>CO</state_code>
                </class>
                <class xmlns="http://www.valentech.com/2013/11/prediction/class">
                    <description>YARN OR THREAD MFG.-COTTON</description>
                    <class_code>2220</class_code>
                    <payroll_amount_initial>50000.0</payroll_amount_initial>
                    <state_code>UT</state_code>
                </class>
                <class xmlns="http://www.valentech.com/2013/11/prediction/class">
                    <description>SALESPERSONS OR COLLECTORS-OUTSIDE(N/A MA)</description>
                    <class_code>8742</class_code>
                    <payroll_amount_initial>50000.0</payroll_amount_initial>
                    <state_code>UT</state_code>
                </class>
            </inputChildren>
            <experience_mod_factor_initial>0.25</experience_mod_factor_initial>
            <policy_state_code>CO</policy_state_code>
            <insured_name>TEST SUBMISSION</insured_name>
        </inputs>
        <outputs xmlns="http://www.valentech.com/2013/11/prediction/outputs" level="insured">
            <pi_interpretation>Premium impact due to exposure misclassification is generally Neutral Premium between -$23 and $71</pi_interpretation>
            <loss_ratio_relativity_prediction>0.9342303384131115</loss_ratio_relativity_prediction>
            <risk_lower_bound>-0.1</risk_lower_bound>
            <term_effective_date>2015-07-04</term_effective_date>
            <misclass_upper_bound>0.15</misclass_upper_bound>
            <pi_bin>7</pi_bin>
            <pi_upper_bound>71</pi_upper_bound>
            <original_policy_term_number>2015022404</original_policy_term_number>
            <pi_lower_bound>-23</pi_lower_bound>
            <misclass_lower_bound>0.12</misclass_lower_bound>
            <risk_upper_bound>-0.05</risk_upper_bound>
            <pi_prediction>17.442202309508698</pi_prediction>
            <misclass_interpretation>% exposure misclassified for bin 9 generally falls between 12% - 15%</misclass_interpretation>
            <risk_bin>4</risk_bin>
            <misclass_bin>9</misclass_bin>
            <risk_interpretation>Prospective term loss ratios of policies in bin 4 are typically 5% to 10% lower than average within your data</risk_interpretation>
        </outputs>
        <explanations level="insured">
            <explanations xmlns="http://www.valentech.com/2013/11/prediction/explanations" name="exposure_shift_prediction">
                <explanation name="misclass_exp_class_count_onlevel_initial">
                    <description>Class Count</description>
                    <importance>0.4037634867159326</importance>
                </explanation>
                <explanation name="misclass_exp_governing_industry_group">
                    <description>NCCI Governing Industry Group</description>
                    <importance>0.2258528541727219</importance>
                </explanation>
                <explanation name="misclass_exp_loss_cost_versus_governing_class_loss_cost">
                    <description>Policy Weighted Avg Loss Cost vs. Governing Class Loss Cost</description>
                    <importance>-0.20155792702105918</importance>
                </explanation>
                <explanation name="misclass_exp_governing_loss_cost_initial_onlevel">
                    <description>Governing Class Loss Cost</description>
                    <importance>0.02263698764081296</importance>
                </explanation>
                <explanation name="misclass_exp_combined_hazard_score">
                    <description>Combined Weighted Average Hazard Score</description>
                    <importance>0.0035460407361076764</importance>
                </explanation>
            </explanations>
            <explanations xmlns="http://www.valentech.com/2013/11/prediction/explanations" name="loss_ratio_relativity_prediction">
                <explanation name="risk_exp_prem_per_claim_0">
                    <description>Claim History</description>
                    <importance>0.47841691998613384</importance>
                </explanation>
                <explanation name="risk_exp_log_prem_per_claim">
                    <description>Policy Size to Claim History Relativity</description>
                    <importance>-0.4272386145452476</importance>
                </explanation>
                <explanation name="risk_exp_scf_3yr_lr_cred">
                    <description>Regional Loss Ratio History</description>
                    <importance>-0.045573416854520286</importance>
                </explanation>
                <explanation name="risk_exp_indus_3yr_lr">
                    <description>Industry and State Loss Ratio History</description>
                    <importance>-0.013856934443070191</importance>
                </explanation>
                <explanation name="risk_exp_class_scf_3yr_freq_rel">
                    <description>Class to Region Claim Frequency Relativity</description>
                    <importance>0.011334509108306719</importance>
                </explanation>
            </explanations>
            <explanations xmlns="http://www.valentech.com/2013/11/prediction/explanations" name="pi_score">
                <explanation name="pi_exp_pct_std_except">
                    <description>Standard Exception Class Exposure Weighting</description>
                    <importance>0.24170140340562737</importance>
                </explanation>
                <explanation name="pi_exp_premium">
                    <description>Policy Premium</description>
                    <importance>-0.20899761842907172</importance>
                </explanation>
                <explanation name="pi_exp_zaftig2_sc">
                    <description>Policy Loss Cost Weighting vs. Gov Class Average: Single-Class</description>
                    <importance>-0.19158874402073914</importance>
                </explanation>
                <explanation name="pi_exp_zaftig2">
                    <description>Policy Loss Cost Weighting vs. Gov Class Average: Multi-Class</description>
                    <importance>0.17288638250534885</importance>
                </explanation>
                <explanation name="pi_exp_weighted_loss_cost">
                    <description>Policy Exposure Weighted Loss Cost</description>
                    <importance>0.0965228221745559</importance>
                </explanation>
            </explanations>
        </explanations>
        <reportData level="insured">
            <classCodeComparison xmlns="http://www.valentech.com/2013/11/prediction/comparison">
                <class type="governing" sortOrder="0">
                    <stateCode>CO</stateCode>
                    <classCode>2416</classCode>
                    <description>YARN DYEING OR FINISHING.</description>
                    <percentOfPayroll>0.774131</percentOfPayroll>
                    <percentOfObservations>1</percentOfObservations>
                </class>
                <class type="associated" sortOrder="1">
                    <stateCode>CO</stateCode>
                    <classCode>8810</classCode>
                    <description>CLERICAL OFFICE EMPLOYEES NOC</description>
                    <percentOfPayroll>0.22586876</percentOfPayroll>
                    <percentOfObservations>1.0</percentOfObservations>
                </class>
            </classCodeComparison>
            <governingClassSwapResults xmlns="http://www.valentech.com/2013/11/prediction/swap">
                <class type="governing" sortOrder="0">
                    <agencyCode>NCCI</agencyCode>
                    <classCode>2416</classCode>
                    <description>YARN DYEING OR FINISHING.</description>
                    <percentOfOccurrence>1.0</percentOfOccurrence>
                    <percentOfPremiumImpact>1.1218325</percentOfPremiumImpact>
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

##### Special characters in XML: 

The following are special characters and how to address them in XML (note the Failed Examples will throw a **500: Internal Server Error**):

|Special Character|Escape code|Failed example|Proper Example|
|-----------------|-----------|--------------|--------------|
|&|`&amp;`|`<app:insured\_name value="Joe Rockhead & Sons">`|`<app:insured_name value="Joe Rockhead &amp; Sons"/>`|
|" (quote)|`&quot;`|`<app:insured_name value="Joe Rockhead "DBA Rockhead Quarry""/>`|`<app:insured_name value="Joe Rockhead &quot;DBA Rockhead Quarry&quot;"/>`|


#### Predict RESTful Request – Generated Error Examples

##### Bad Request

If the request XML is not well-formed a Bad Request (HTTP 400) status code containing an illFormedRequest body will be returned including the ill-formed request:

```xml
<response>
    <info>
        <scoreKey>[guid goes here]</scoreKey>
    </info>
    <illFormedRequest>
        <request>
            <![CDATA[
            <score>
                <!-- literal request -->
            </score>
            ]]>
        </request>
    </illFormedRequest>
</response>
```

##### Constraint Violation

```xml
<response>
    <info>
        <scoreKey>[guid goes here]</scoreKey>
    </info>
    <invalidRequest>
        <request>
            <![CDATA[<?xml version='1.0' encoding='UTF-8'?>
            <score version="1.0">
                <info>
                    <customer>valen</customer>
                    <solution>insureright</solution>
                    <submission>scoring</submission>
                    <scoreKey>[guid goes here]</scoreKey>
                </info>
                <inputs level="insured">
                    <agency>Test123</agency>
                    <underwriter>UNDER WRITER</underwriter>
                    <audit_method_code>P</audit_method_code>
                    <available_history_1>Y</available_history_1>
                    <available_history_2>Y</available_history_2>
                    <available_history_3>Y</available_history_3>
                    <experience_mod_factor_initial>2.43</experience_mod_factor_initial>
                    <insured_name>Test</insured_name>
                    <new_renew_flag>R</new_renew_flag>
                    <non_zero_claim_count_1>9</non_zero_claim_count_1>
                    <non_zero_claim_count_2>9</non_zero_claim_count_2>
                    <non_zero_claim_count_3>8</non_zero_claim_count_3>
                    <original_policy_term_number>test 3.0</original_policy_term_number>
                    <policy_state_code>HI</policy_state_code>
                    <policy_zip_code>96817</policy_zip_code>
                    <term_effective_date>2014-01-01</term_effective_date>
                    <inputChildren level="class">
                        <class>
                            <class_code>2587</class_code>
                            <payroll_amount_initial>35858</payroll_amount_initial>
                            <state_code>HI</state_code>
                        </class>
                        <class>
                            <class_code>8810</class_code>
                            <payroll_amount_initial>10000</payroll_amount_initial>
                            <state_code>KY</state_code>
                        </class>
                    </inputChildren>
                </inputs>
            </score>]]></request>
    </invalidRequest>
    <errors count="1">
        <validationError>
            <name>invalidInputValue</name>
            <description>The value for 'non_zero_claim_count_3' is 'Y' which is not the specified type: 'integer'</description>
            <level>insured</level>
            <key>
                <vglItm>
                    <vglItm>
                        <vglItm>:term_effective_date</vglItm>
                        <vglItm>2014-01-01</vglItm>
                    </vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>test 3.0</vglItm>
                    </vglItm>
                </vglItm>
            </key>
            <field>nonZeroClaimCount3</field>
            <value>Y</value>
        </validationError>
    </errors>
</response>
```

##### Duplicate Class Code

```xml
<response>
    <info>
        <scoreKey>[guid goes here]</scoreKey>
    </info>
    <invalidRequest>
        <request>
            <![CDATA[<?xml version='1.0' encoding='UTF-8'?>
            <score version="1.0">
                <info>
                    <customer>valen</customer>
                    <solution>insureright</solution>
                    <submission>scoring</submission>
                    <scoreKey>[guid goes here]</scoreKey>
                </info>
                <inputs level="insured">
                    <agency>123456</agency>
                    <underwriter>Underwriter</underwriter>
                    <audit_method_code>P</audit_method_code>
                    <available_history_1>Y</available_history_1>
                    <available_history_2>Y</available_history_2>
                    <available_history_3>Y</available_history_3>
                    <available_history_4>Y</available_history_4>
                    <available_history_indemnity_1>Y</available_history_indemnity_1>
                    <available_history_indemnity_2>Y</available_history_indemnity_2>
                    <available_history_indemnity_3>Y</available_history_indemnity_3>
                    <available_history_indemnity_4>Y</available_history_indemnity_4>
                    <claim_amount_total_incurred_1>9</claim_amount_total_incurred_1>
                    <claim_amount_total_incurred_2>9</claim_amount_total_incurred_2>
                    <claim_amount_total_incurred_3>8</claim_amount_total_incurred_3>
                    <claim_amount_total_incurred_4>0</claim_amount_total_incurred_4>
                    <experience_mod_factor_initial>2.43</experience_mod_factor_initial>
                    <insured_name>example data</insured_name>
                    <new_renew_flag>R</new_renew_flag>
                    <non_zero_claim_count_1>9</non_zero_claim_count_1>
                    <non_zero_claim_count_2>9</non_zero_claim_count_2>
                    <non_zero_claim_count_3>8</non_zero_claim_count_3>
                    <non_zero_claim_count_4>0</non_zero_claim_count_4>
                    <non_zero_claim_count_indemnity_1>9</non_zero_claim_count_indemnity_1>
                    <non_zero_claim_count_indemnity_2>9</non_zero_claim_count_indemnity_2>
                    <non_zero_claim_count_indemnity_3>8</non_zero_claim_count_indemnity_3>
                    <non_zero_claim_count_indemnity_4>0</non_zero_claim_count_indemnity_4>
                    <original_policy_term_number>test123</original_policy_term_number>
                    <policy_state_code>KY</policy_state_code>
                    <policy_zip_code>40143</policy_zip_code>
                    <term_effective_date>2014-01-01</term_effective_date>
                    <inputChildren level="class">
                        <class>
                            <class_code>2587</class_code>
                            <payroll_amount_initial>35858</payroll_amount_initial>
                            <state_code>KY</state_code>
                        </class>
                        <class>
                            <class_code>8810</class_code>
                            <payroll_amount_initial>10000</payroll_amount_initial>
                            <state_code>KY</state_code>
                        </class>
                        <class>
                            <class_code>8810</class_code>
                            <payroll_amount_initial>10000</payroll_amount_initial>
                            <state_code>KY</state_code>
                        </class>
                    </inputChildren>
                </inputs>
            </score>]]></request>
    </invalidRequest>
    <errors count="1">
        <validationError>
            <name>uniqueKey</name>
            <description>keys are repeated multiple(0)times</description>
            <field/>
            <value/>
        </validationError>
    </errors>
</response>
```

##### Required Field Violation

```xml
<response>
    <info>
        <scoreKey>[guid goes here]</scoreKey>
    </info>
    <invalidRequest>
        <request>
            <![CDATA[<?xml version='1.0'encoding='UTF-8'?>
            <scoreversion="1.0">
                <info>
                    <customer>valen</customer>
                    <solution>insureright</solution>
                    <submission>scoring</submission>
                    <scoreKey>[guid goes here]</scoreKey>
                </info>
                <inputslevel="insured">
                    <agency>Test123</agency>
                    <underwriter>Test Person</underwriter>
                    <audit_method_code>P</audit_method_code>
                    <available_history_1>Y</available_history_1>
                    <available_history_2>Y</available_history_2>
                    <available_history_3>Y</available_history_3>
                    <available_history_4>Y</available_history_4>
                    <available_history_indemnity_1>Y</available_history_indemnity_1>
                    <available_history_indemnity_2>Y</available_history_indemnity_2>
                    <available_history_indemnity_3>Y</available_history_indemnity_3>
                    <available_history_indemnity_4>Y</available_history_indemnity_4>
                    <claim_amount_total_incurred_1>9</claim_amount_total_incurred_1>
                    <claim_amount_total_incurred_2>9</claim_amount_total_incurred_2>
                    <claim_amount_total_incurred_3>8</claim_amount_total_incurred_3>
                    <claim_amount_total_incurred_4>0</claim_amount_total_incurred_4>
                    <experience_mod_factor_initial>2.43</experience_mod_factor_initial>
                    <insured_name>Error Submit</insured_name>
                    <new_renew_flag>R</new_renew_flag>
                    <non_zero_claim_count_1>9</non_zero_claim_count_1>
                    <non_zero_claim_count_2>9</non_zero_claim_count_2>
                    <non_zero_claim_count_3>8</non_zero_claim_count_3>
                    <non_zero_claim_count_4>0</non_zero_claim_count_4>
                    <non_zero_claim_count_indemnity_1>9</non_zero_claim_count_indemnity_1>
                    <non_zero_claim_count_indemnity_2>9</non_zero_claim_count_indemnity_2>
                    <non_zero_claim_count_indemnity_3>8</non_zero_claim_count_indemnity_3>
                    <non_zero_claim_count_indemnity_4>0</non_zero_claim_count_indemnity_4>
                    <original_policy_term_number>Test12/original_policy_term_number>
                    <policy_state_code>KY</policy_state_code>
                    <policy_zip_code>40143</policy_zip_code>
                    <term_effective_date/>
                    <inputChildrenlevel="class">
                        <class>
                            <class_code>2587</class_code>
                            <payroll_amount_initial>35858</payroll_amount_initial>
                            <state_code>KY</state_code>
                        </class>
                        <class>
                            <class_code>8810</class_code>
                            <payroll_amount_initial>10000</payroll_amount_initial>
                            <state_code>KY</state_code>
                        </class>
                    </inputChildren>
                </inputs>
            </score>]]></request>
    </invalidRequest>
    <errors count="3">
        <validationError>
            <name>required</name>
            <description>A required value for 'term_effective_date' was notprovided</description>
            <level>class</level>
            <key>
                <vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>Test12</vglItm>
                    </vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>Test12</vglItm>
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
            <description>A required value for 'term_effective_date' was notprovided</description>
            <level>class</level>
            <key>
                <vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>Test12</vglItm>
                    </vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>Test12</vglItm>
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
            <description>A required value for 'term_effective_date' was notprovided</description>
            <level>insured</level>
            <key>
                <vglItm>
                    <vglItm>
                        <vglItm>:original_policy_term_number</vglItm>
                        <vglItm>Test12</vglItm>
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
<data contentType="null"
contentLength="16"><![CDATA[401:Unauthorized]]></data>
```

### Appendix D – *Predict* Report Download

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

### Appendix E – Test Tools

We use cURL or Postman to test our request content.

The curl utility can be downloaded at <http://curl.haxx.se/download.html> for various operating systems.

Postman is an app for Chrome or Mac and can be found at <https://www.getpostman.com/>

##### Curl

Given a valid xml file (see the example request in [Appendix A](#_Appendix_A_-)), this is a sample request.

curl –u \[username\]:\[password\] -X POST -d @valid-request.xml -H 'Content-Type: application/xml' https://insureright.valen.com/solutions/<customer>/insureright/scoring

###### Postman

We have a library of sample requests at <https://www.getpostman.com/collections/da164d2bbcfb55623635>

Import the library into Postman, click on the Authorization tab then follow these steps.

1.  Enter the username and password for your Valen webservices user
    account

2.  Hit Update Request

3.  Click the “Body” tab

While on the “Body” Tab, enter valid values into the xml (you may need to select the “Raw” radio tab to see the xml).

Hit Send.


### Appendix F - Disclaimers

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

February 24, 2015

| Revision | Date | Author(s) | Description |
|----------|------|-----------|-------------|
|1|02/24/2015|Valen Support|Initial version for InsureRight 3.0 Predict Platform|
|1.1|7/6/2016|Valen Support|Updated the examples to reflect code improvements.|
|1.2|8/2/2016|Valen Support|Updated the error messaging|
|1.3|11/14/2016|Valen Support|Converted to web document|
|1.4|7/7/2017|Valen Support|Updated the sample responses|