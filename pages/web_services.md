---
layout: page
title: InsureRight® Predict Web Services
permalink: webservices_guide.html
sidebar: nav_sidebar
last_updated: November, 2016
summary: The webservices guide for the 3.0 platform.
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

### PWS Application 

The PWS addresses interoperability concerns by giving clients a way to
easily send their data to the InsureRight production system and receive
scores and predictions generated using their data via HTTP.

#### PWS Application Flow

When valid data is submitted, the PWS works as follows:
1.  The client sends a PWS request to a given application containing
    input data to score.
2.  Predict production system validates the input data.
3.  Predict production system scores the input data.
4.  Predict production system returns the scores and additional
    information explaining the scores.

Submitting data that is invalid or improperly formatted will return an error message. A list of common errors is provided below. For detailed responses, see [APPENDIX C: Error Handling](#appendixC). The errors in a failed scoring request will need to be addressed and the request will have to be resubmitted for scoring.

#### PWS Application Processing Requirements
Requests to the PWS are designed as RESTful requests. Responses are
provided for all models supported, i.e.*,* risk, misclassification and
premium impact. The user cannot select independent models to be scored
and or partial responses to be returned.

Web service requests are submitted as a POST request to the following
URL:

`[server]/solutions/[solution]/[submission]`

For example:
`https://insureright.valen.com/solutions/insureright/scoring`

In this example the **solution** is InsureRight. A number of different
types of submissions may be made to the InsureRight solution, but in
this case we are making a **scoring** submission, which will score the
inputs against all InsureRight Predict models.

#### Request Contents

Two headers are required for successful request.

-   Content-Type – this can be either application/xml or text/xml
-   Authorization – username and password

The body of the request must conform to the standards defined in the [Data Dictionary Requirements](#_Data_Dictionary_Requirements_1) section of this document. This section describes the input data names, types, and constraints. The *Field Name* is used as a unique identifier when sending input data. The web-service WSDL also defines the input data names and constraints.

If the XML is a well-formed, UTF-8 request, the request will be
validated.

#### Web Service Style

The PWS uses HTTPS and supports RESTful requests.

The PWS processes requests and returns results synchronously. The
formatting and encoding style are document and literal, respectively.
All requests must be secure requests.

### Common Errors

A list of responses to common errors. Detailed information about the response messages can be found in [APPENDIX C: Error Handling](#appendixC).

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

This will return a 401 Unauthorized

##### Non-escaped special character

Special characters in the XML will return a status code of 500.


### Building a Predict Web Service Scoring Request

PWS Requests are **POST** requests to the URL constructed above.

The example a reminder - `https://insureright.valen.com/solutions/insureright/scoring`

Two headers are required:
```http
Content-Type: Application/xml
Authorization: username/password in base64
```

The PWS Application requires data in a minimum of 2 nodes, **insured** and **class**. Those are nested inside the **input node**. In order to handle multiple classes, the class information is further nested inside the **input_child** node. The sequence of field names defined in the input files are not important, providing the field names defined in the columns match Valen’s field names. A full example is provided in [Appendix A](#appendixA). More information about the fields is provided in the [Data Dictionary](#dataDict). The basic layout for Request and Response packets is this:

##### Request Requirements
```xml
<inputs>
	<insured></insured>
	<inputChildren>
		<class></class>
	</inputChildren>
</inputs>
```

### Data Dictionary

##### Input Data Specification

<!-- Create tooltip for values of audit method. Need explainer for P/T/M/I/W/E/N-->

**Insured Node**

|Field Name|Data Type|Required|Valid Values|
|----------|---------|--------|------------| 
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.original_policy_term_number }}">original_policy_term_number</a>|char (key)|Yes|Maximum of 50 alphanumeric and special characters.|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.term_effective_date }}">term_effective_date</a>|date (key)|Yes|YYYY-MM-DD|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.audit_method_code }}">audit_method_code</a>|char|No|P/T/M/I/W/E/N|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.underwriter }}">underwriter</a>|char|No|Maximum of 100 alphanumeric and special characters, including spaces.|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.agency }}">agency</a>|char|No|Maximum of 100 alphanumeric and special characters, including spaces.|
|insured_name|char|No|Maximum of 100 alphanumeric and special characters, including spaces.|
|policy_address|char|No|Maximum of 100 alphanumeric and special characters, including spaces.|
|policy_city_name|char|No|Maximum of 50 alphanumeric characters, including spaces.|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.agency }}">policy_state_code</a>|char (key)|Yes|Maximum of 2 characters|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.agency }}">policy_zip_code</a>|char (key)|Yes|Must be 5-digit ZIP Code in accordance with U.S. Postal Service standards.|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.available_history }}">available_history_1</a>|boolean|Yes|Y or N|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.available_history }}">available_history_2</a>|boolean|Yes|Y or N|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.available_history }}">available_history_3</a>|boolean|Yes|Y or N|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.non_zero_claim_count }}">non_zero_claim_count_1</a>|integer|Dependency|If the ‘available_history_1’ field is “Y”, this field must have a value greater than or equal to 0 (zero). If the 'available_history_1' field is 'N,' this field must be left blank|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.non_zero_claim_count }}">non_zero_claim_count_2</a>|integer|Dependency|If the ‘available_history_2’ field is “Y”, this field must have a value greater than or equal to 0 (zero). If the 'available_history_2' field is 'N,' this field must be left blank|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.non_zero_claim_count }}">non_zero_claim_count_3</a>|integer|Dependency|If the ‘available_history_3’ field is “Y”, this field must have a value greater than or equal to 0 (zero). If the 'available_history_3' field is 'N,' this field must be left blank|

**Class Node**

|Field Name|Data Type|Required|Valid Values|
|----------|---------|--------|------------|
|policy_state_code|char|No|Maximum of 2 characters|
|state_code|char (key)|Yes|Maximum of 2 characters|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.non_zero_claim_count }}">class_code</a>|char (key)|Yes|Constrained to 4 digits, including leading zeroes|
|payroll_amount_initial|double|Yes|Must be greater than or equal to 0 (zero)|

##### Output Data Specification

**Info Node**

|Field Name|Data Type|Generated By|Purpose|
|----------|---------|------------|-------| 
|scoreKey|char (key)|Valen|Valen internal record identification|
|scoreId|char (key)|Valen|The value created by Valen to uniquely identify the score (ReportId)|

**Inputs Node**

|Field Name|Data Type|Generated By|Purpose|
|----------|---------|------------|-------| 
|policy_city_name|char|Original Submission|Pass through value from original submission|
|underwriter|char|Original Submission|Pass through value from original submission|
|non_zero_claim_count_1|integer|Original Submission|Pass through value from original submission|
|new_renew_flag|char|Original Submission|Pass through value from original submission|
|policy_zip_code|char|Original Submission|Pass through value from original submission|
|non_zero_claim_count_2|integer|Original Submission|Pass through value from original submission|
|available_history_2|integer|Original Submission|Pass through value from original submission|
|term_effective_date|date (key)|Original Submission|Pass through value from original submission|
|available_history_3|integer|Original Submission|Pass through value from original submission|
|agency|char|Original Submission|Pass through value from original submission|
|original_policy_term_number|char (key)|Original Submission|Pass through value from original submission|
|non_zero_claim_count_3|integer|Original Submission|Pass through value from original submission|
|policy_address|char|Original Submission|Pass through value from original submission|
|available_history_1|integer|Original Submission|Pass through value from original submission|
|audit_method_code|char|Original Submission|Pass through value from original submission|
|experience_mod_factor_intial|double|Original Submission|Pass through value from original submission|
|policy_state_code|char|Original Submission|Pass through value from original submission|
|insured_name|char|Original Submission|Pass through value from original submission|

**Input Children Node - Class subnode**

|Field Name|Data Type|Generated By|Purpose|
|----------|---------|------------|-------|
|Description|char|Valen|Textual description of the class code.|
|class_code|double|Original Submission|Pass through value from original submission|
|payroll_amount_initial|char|Original Submission|Pass through value from original submission|
|policy_state_code|char|Original Submission|Pass through value from original submission|

**Insured Node**

|Field Name|Data Type|Generated By|Purpose|
|----------|---------|------------|-------|
|pi_interpretation|char|Valen|Textual description of Premium Impact Model results|
|loss_ratio_relativity_prediction|double|Valen|Calculation related to the Risk Model|
|risk_lower_bound|double|Valen|Lower bound of the risk|
|term_effective_date|date|Original Submission|Pass through value from original submission|
|pi_bin|integer|Valen|Premium Impact bin value|
|pi_upper_bound|char|Valen|Upper bound of the Premium Impact bin|
|original_policy_term_number|char|Original Submission|Pass through value from original submission|
|pi_lower_bound|integer|Valen|Lower bound of the Premium Impact bin|
|misclass_lower_bound|double|Valen|Lower bound of the Misclassification bin|
|risk_upper_bound|double|Valen|Upper limit of the InsureRight Risk prediction|
|pi_prediction|double|Valen|Premium Impact prediction|
|misclass_interpretation|char|Valen|Textual interpretation related to the Misclassification bin|
|risk_bin|integer|Valen|InsureRight Risk bin value|
|misclass_bin|integer|Valen|Misclassification bin value|
|risk_interpretation|char|Valen|Textual interpretation of the InsureRight Risk prediction|

**Explain Nodes**

These nodes are often customized to reflect a specific model. Here are some generic explain descriptions.

|Node|Description|
|----|---------|
|Misclassification|Up to 5 occurrences of messages detailing the reasoning for a misclassification score|
|Premium Impact|Up to 5 occurrences of messages detailing the reasoning for a premium impact score|
|Risk|Up to 5 occurrences of messages detailing the reasoning for a risk score|

**Explanations**

For each explanations node the data will include:

explanation name=*a text description of the reason*

Description=*text description as displayed on Valen’s report* 

Importance=*a numeric indicator of the importance of this variable in the overall score* 

|Title|Description|
|-----|-----------|
|exposure_shift_prediction|Up to 5 descriptions and measures, associated with the explain nodes, providing information on the relative importance of reasons for a misclassification score|
|loss_ratio_relativity_prediction|Up to 5 descriptions and measures, associated with the explain nodes, providing information on the relative importance of reasons for a risk score|
|pi_score|Up to 5 descriptions and measures, associated with the explain nodes, providing information on the relative importance of reasons for a premium impact score|

**Report Data**

There will be a section or sections similar to the following. If there are more than one, the sort order will increment accordingly.

*classCodeComparison*

```xml
  <class sortOrder="0" type="governing"> 
    <stateCode>[state abbreviation]</stateCode> 
    <classCode>[numeric class code]</classCode> 
    <description>[textual class code description]</description> 
    <percentOfPayroll>[ double]</percentOfPayroll> 
    <percentOfObservations>[double]</percentOfObservations> 
  </class>
```

*governingClassSwapResults*

```xml
  <class sortOrder="0" type="governing"> 
    <agencyCode>[WC Agency]</agencyCode> 
    <classCode>[numeric class code]</classCode> 
    <description>[textual class code description]</description> 
    <percentOfOccurrence>[double]</percentOfOccurrence> 
    <percentOfPremiumImpact>[double]</percentOfPremiumImpact> 
  </class>
```
  
### Building a Predict Web Service Batch Request

### Appendix A - RESTful Request / Response Example

<span id="_Toc412556468" class="anchor"></span>We use cURL or Postman to
test our response content. See [Appendix E](#_Appendix_E_) for some
examples.

##### Predict RESTful Request Example: 

The following example describes a valid Valen® PWS request.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <score version="1.0">
        <inputs level="insured">
            <policy_city_name>A CITY IN</policy_city_name>
            <agency>111777</agency>
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
                    <policy_state_code>CO</policy_state_code>
                </class>
                <class>
                    <class_code>2220</class_code>
                    <payroll_amount_initial>50000</payroll_amount_initial>
                    <state_code>UT</state_code>
                    <policy_state_code>CO</policy_state_code>
                </class>
                <class>
                    <class_code>8742</class_code>
                    <payroll_amount_initial>50000</payroll_amount_initial>
                    <state_code>UT</state_code>
                    <policy_state_code>CO</policy_state_code>
                </class>
            </inputChildren>
        </inputs>
    </score>
</request>
```


##### Predict Valid Response Example

If the request is well-formed, the input values can be converted to the
required types and the data passes all constraints and exclusions, a
response including all results is synchronously returned.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<response xmlns="http://www.valentech.com/2013/11/prediction/response">
  <score version="1.0">
    <info>
      <scoreKey>125340a9-8a90-4844-8a10-defc8f5f3420</scoreKey>
      <scoreId>1405194</scoreId>
    </info>
    <inputs xmlns="http://www.valentech.com/2013/11/prediction/inputs" level="insured">
      <underwriter>UNDER WRITER</underwriter>
      <non_zero_claim_count_1>0</non_zero_claim_count_1>
      <new_renew_flag>N</new_renew_flag>
      <policy_zip_code>81601</policy_zip_code>
      <non_zero_claim_count_2>0</non_zero_claim_count_2>
      <available_history_2>Y</available_history_2>
      <term_effective_date>2015-07-04</term_effective_date>
      <available_history_3>Y</available_history_3>
      <agency>111777</agency>
      <original_policy_term_number>2015022404</original_policy_term_number>
      <non_zero_claim_count_3>0</non_zero_claim_count_3>
      <available_history_1>Y</available_history_1>
      <audit_method_code>P</audit_method_code>
      <experience_mod_factor_initial>0.25</experience_mod_factor_initial>
      <policy_state_code>CO</policy_state_code>
      <insured_name>TEST SUBMISSION</insured_name>
      <inputChildren level="class">
        <class xmlns="http://www.valentech.com/2013/11/prediction/class">
          <description>YARN DYEING OR FINISHING.</description>
          <class_code>2416</class_code>
          <payroll_amount_initial>250000.0</payroll_amount_initial>
          <policy_state_code>CO</policy_state_code>
          <state_code>CO</state_code>
        </class>
        <class xmlns="http://www.valentech.com/2013/11/prediction/class">
          <description>YARN OR THREAD MFG.-COTTON</description>
          <class_code>2220</class_code>
          <payroll_amount_initial>50000.0</payroll_amount_initial>
          <policy_state_code>CO</policy_state_code>
          <state_code>UT</state_code>
        </class>
        <class xmlns="http://www.valentech.com/2013/11/prediction/class">
          <description>SALESPERSONS OR COLLECTORS-OUTSIDE(N/A MA)</description>
          <class_code>8742</class_code>
          <payroll_amount_initial>50000.0</payroll_amount_initial>
          <policy_state_code>CO</policy_state_code>
          <state_code>UT</state_code>
        </class>
        <class xmlns="http://www.valentech.com/2013/11/prediction/class">
          <description>CLERICAL OFFICE EMPLOYEES NOC</description>
          <class_code>8810</class_code>
          <payroll_amount_initial>87108.0</payroll_amount_initial>
          <policy_state_code>CO</policy_state_code>
          <state_code>FL</state_code>
        </class>
      </inputChildren>
    </inputs>
    <outputs xmlns="http://www.valentech.com/2013/11/prediction/outputs" level="insured">
      <pi_interpretation>Premium impact due to exposure misclassification is generally Additional Premium between $71 and $223</pi_interpretation>
      <loss_ratio_relativity_prediction>1.1201785113099543</loss_ratio_relativity_prediction>
      <risk_lower_bound>0.075</risk_lower_bound>
      <term_effective_date>2015-07-04</term_effective_date>
      <misclass_upper_bound>0.15</misclass_upper_bound>
      <pi_bin>8</pi_bin>
      <pi_upper_bound>223</pi_upper_bound>
      <original_policy_term_number>2015022404</original_policy_term_number>
      <pi_lower_bound>71</pi_lower_bound>
      <misclass_lower_bound>0.12</misclass_lower_bound>
      <risk_upper_bound>0.15</risk_upper_bound>
      <pi_prediction>127.1539884664539</pi_prediction>
      <misclass_interpretation>% exposure misclassified for bin 9 generally falls between 12% - 15%</misclass_interpretation>
      <risk_bin>7</risk_bin>
      <misclass_bin>9</misclass_bin>
      <risk_interpretation>Prospective term loss ratios of policies in bin 7 are typically 7.5% to 15% higher than average within your data</risk_interpretation>
    </outputs>
    <explanations level="insured">
      <explanations xmlns="http://www.valentech.com/2013/11/prediction/explanations" name="exposure_shift_prediction">
        <explanation name="misclass_exp_class_count_onlevel_initial">
          <description>Class Count</description>
          <importance>0.27119261423539837</importance>
        </explanation>
        <explanation name="misclass_exp_governing_industry_group">
          <description>NCCI Governing Industry Group</description>
          <importance>0.26255110702512885</importance>
        </explanation>
        <explanation name="misclass_exp_loss_cost_versus_governing_class_loss_cost">
          <description>Policy Weighted Average Loss Cost Relative to Governing Class Loss Cost</description>
          <importance>-0.23430855927372504</importance>
        </explanation>
        <explanation name="misclass_exp_combined_hazard_score">
          <description>Combined Weighted Average Hazard Score</description>
          <importance>0.03981215362239914</importance>
        </explanation>
        <explanation name="misclass_exp_governing_loss_cost_initial_onlevel">
          <description>Policy Weighted Average Loss Cost Relative to Governing Class Loss Cost</description>
          <importance>0.02631521388817334</importance>
        </explanation>
      </explanations>
      <explanations xmlns="http://www.valentech.com/2013/11/prediction/explanations" name="loss_ratio_relativity_prediction">
        <explanation name="risk2_exp_lngn1lf_lrr_250">
          <description>Loss Ratio Relative to National for like industry and historical claim profile</description>
          <importance>-0.0</importance>
        </explanation>
        <explanation name="risk2_exp_lf_mod_4">
          <description>Valen Loss Free Model Score</description>
          <importance>-0.0</importance>
        </explanation>
        <explanation name="risk2_exp_one_yr_manprem_freq">
          <description>1 Yr Frequency</description>
          <importance>-0.0</importance>
        </explanation>
        <explanation name="risk2_exp_lsrblf_claim_frequency">
          <description>Claim Frequency of like State Rate Band and historical claim profile</description>
          <importance>-0.0</importance>
        </explanation>
        <explanation name="risk2_exp_lnghlcbw_lrr_250">
          <description>Loss Ratio for policies with like State policy rate And Hazard</description>
          <importance>-0.0</importance>
        </explanation>
      </explanations>
      <explanations xmlns="http://www.valentech.com/2013/11/prediction/explanations" name="pi_score">
        <explanation name="pi_exp_premium">
          <description>Policy Premium</description>
          <importance>-0.15077391575836302</importance>
        </explanation>
        <explanation name="pi_exp_zaftig2_sc">
          <description>Policy Loss Cost Weighting vs. Gov Class Average: Single-Class</description>
          <importance>-0.14441566997103877</importance>
        </explanation>
        <explanation name="pi_exp_zaftig2">
          <description>Policy Loss Cost Weighting vs. Gov Class Average: Multi-Class</description>
          <importance>0.13031821303487698</importance>
        </explanation>
        <explanation name="pi_exp_weighted_loss_cost">
          <description>Policy Exposure Weighted Loss Cost</description>
          <importance>0.08660492124918047</importance>
        </explanation>
        <explanation name="pi_exp_zaftig1">
          <description>Exposure Weighting Across Lower Loss Cost Classes</description>
          <importance>-0.04484792394062284</importance>
        </explanation>
      </explanations>
    </explanations>
    <reportData level="insured">
      <governingClassSwapResults xmlns="http://www.valentech.com/2013/11/prediction/swap">
        <class type="governing" sortOrder="0">
          <agencyCode>NCCI</agencyCode>
          <classCode>2416</classCode>
          <description>YARN DYEING OR FINISHING.</description>
          <percentOfOccurrence>1.0</percentOfOccurrence>
          <percentOfPremiumImpact>1.1218325</percentOfPremiumImpact>
        </class>
      </governingClassSwapResults>
      <classCodeComparison xmlns="http://www.valentech.com/2013/11/prediction/comparison">
        <class type="governing" sortOrder="0">
          <stateCode>CO</stateCode>
          <classCode>2416</classCode>
          <description>YARN DYEING OR FINISHING.</description>
          <percentOfPayroll />
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
    </reportData>
  </score>
</response>
```

### Appendix B – Interpreting the “Importance” value

The **Importance** value provides the underwriter information describing
the relative influence of the model equation predictive variables on a
specific policy’s prediction, given that policy’s specific predictive
variable values. The importance values may be interpreted as follows:

**Sign:**

-   A negative sign for a variable indicates that this variable’s
    specific value increased the policy’s relative loss ratio
    prediction; a positive sign for a variable indicates that this
    variable’s specific value decreased the policy’s relative loss
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

***Caution in regard to using importance values*:** Improper usage of
the importance values is to judgmentally alter predictive model
indications based on the values of importance values. For example, if an
underwriter believes that a high value of an importance value for one
variable is more meaningful than the same high value of an importance
value for another variable, and judgmentally alters the prediction
indication, that underwriter is implicitly reweighting the model
variables, foregoing some of the benefit of empirically derived,
statistically based predictions.

A refinement can be made to the magnitude interpretation: for a given
policy, the percent contribution for a variable is that variables
importance value divided by the sum of the absolute value of importance
values on that policy.

### <a name="appendixC">Appendix C – Error Handling</a>

If the Content Type of the request is not application/json,
application/xml, or text/xml an Unsupported Media Type (HTTP415) status
code will be returned.

If credentials are invalid an Unauthorized (HTTP 401) status code will
be returned.

If the credentials are valid, but the user is not authorized to make a
web service request for the specified customer, solution and submission,
then a Forbidden (HTTP 403) status code will be returned.

Authentication:

The request should contain a HTTP Basic Authentication header containing
a user name and valid credentials. If credentials are invalid an
Unauthorized (HTTP 401) status code will be returned.

Special characters in XML: The following are special characters and how
to address them in XML (note the Failed Examples will throw a **500:
Internal Server Error**):

|Special Character|Escape code|Failed example|Proper Example|
|-----------------|-----------|--------------|--------------|
|&|`&amp;`|`<app:insured\_name value="Joe Rockhead & Sons">`|`<app:insured_name value="Joe Rockhead &amp; Sons"/>`|
|" (quote)|`&quot;`|`<app:insured_name value="Joe Rockhead "DBA Rockhead Quarry""/>`|`<app:insured_name value="Joe Rockhead &quot;DBA Rockhead Quarry&quot;"/>`|


#### Predict RESTful Request – Generated Error Examples

##### Bad Request

If the request XML is not well-formed a Bad Request (HTTP 400) status
code containing an illFormedRequest body will be returned including the
ill-formed request:

&lt;response&gt;

&lt;info&gt;

&lt;scoreKey&gt;65497dc3-8064-46ce-9234-a1f0d9ee0f00&lt;/scoreKey&gt;

&lt;/info&gt;

&lt;illFormedRequest&gt;

&lt;request&gt;&lt;!\[CDATA\[

&lt;score&gt;

&lt;!-- literal request --&gt;

&lt;/score&gt;

\]\]&gt;

&lt;/request&gt;

&lt;/illFormedRequest&gt;

&lt;/response&gt;

##### Constraint Violation

&lt;response&gt;

&lt;info&gt;

&lt;scoreKey&gt;56ab2352-b9ba-49c8-be28-0f3b2e484d79&lt;/scoreKey&gt;

&lt;/info&gt;

&lt;invalidRequest&gt;

&lt;request&gt;&lt;!\[CDATA\[&lt;?xml version='1.0'
encoding='UTF-8'?&gt;&lt;score
version="1.0"&gt;&lt;info&gt;&lt;customer&gt;fwci&lt;/customer&gt;&lt;solution&gt;insureright&lt;/solution&gt;&lt;submission&gt;scoring&lt;/submission&gt;&lt;scoreKey&gt;56ab2352-b9ba-49c8-be28-0f3b2e484d79&lt;/scoreKey&gt;&lt;/info&gt;&lt;inputs
level="insured"&gt;&lt;agency&gt;111777&lt;/agency&gt;&lt;underwriter&gt;UNDER
WRITER&lt;/underwriter&gt;&lt;audit\_method\_code&gt;P&lt;/audit\_method\_code&gt;&lt;available\_history\_1&gt;Y&lt;/available\_history\_1&gt;&lt;available\_history\_2&gt;Y&lt;/available\_history\_2&gt;&lt;available\_history\_3&gt;Y&lt;/available\_history\_3&gt;&lt;experience\_mod\_factor\_initial&gt;2.43&lt;/experience\_mod\_factor\_initial&gt;&lt;insured\_name&gt;Test&lt;/insured\_name&gt;&lt;new\_renew\_flag&gt;R&lt;/new\_renew\_flag&gt;&lt;non\_zero\_claim\_count\_1&gt;9&lt;/non\_zero\_claim\_count\_1&gt;&lt;non\_zero\_claim\_count\_2&gt;9&lt;/non\_zero\_claim\_count\_2&gt;&lt;non\_zero\_claim\_count\_3&gt;8&lt;/non\_zero\_claim\_count\_3&gt;&lt;original\_policy\_term\_number&gt;test
3.0&lt;/original\_policy\_term\_number&gt;&lt;policy\_state\_code&gt;HI&lt;/policy\_state\_code&gt;&lt;policy\_zip\_code&gt;96817&lt;/policy\_zip\_code&gt;&lt;term\_effective\_date&gt;2014-01-01&lt;/term\_effective\_date&gt;&lt;inputChildren
level="class"&gt;&lt;class&gt;&lt;term\_effective\_date&gt;2014-01-01&lt;/term\_effective\_date&gt;&lt;original\_policy\_term\_number&gt;test
3.0&lt;/original\_policy\_term\_number&gt;&lt;class\_code&gt;2587&lt;/class\_code&gt;&lt;payroll\_amount\_initial&gt;35858&lt;/payroll\_amount\_initial&gt;&lt;state\_code&gt;HI&lt;/state\_code&gt;&lt;/class&gt;&lt;class&gt;&lt;term\_effective\_date&gt;2014-01-01&lt;/term\_effective\_date&gt;&lt;original\_policy\_term\_number&gt;test
3.0&lt;/original\_policy\_term\_number&gt;&lt;class\_code&gt;8810&lt;/class\_code&gt;&lt;payroll\_amount\_initial&gt;10000&lt;/payroll\_amount\_initial&gt;&lt;state\_code&gt;KY&lt;/state\_code&gt;&lt;/class&gt;&lt;/inputChildren&gt;&lt;/inputs&gt;&lt;/score&gt;\]\]&gt;&lt;/request&gt;

&lt;/invalidRequest&gt;

&lt;errors count="1"&gt;

&lt;validationError&gt;

&lt;name&gt;invalidInputValue&lt;/name&gt;

&lt;description&gt;The value for 'non\_zero\_claim\_count\_3' is 'Y'
which is not the specified type: 'integer'&lt;/description&gt;

&lt;level&gt;insured&lt;/level&gt;

&lt;key&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:term\_effective\_date&lt;/vglItm&gt;

&lt;vglItm&gt;2014-01-01&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:original\_policy\_term\_number&lt;/vglItm&gt;

&lt;vglItm&gt;test 3.0&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;/key&gt;

&lt;field&gt;nonZeroClaimCount3&lt;/field&gt;

&lt;value&gt;Y&lt;/value&gt;

&lt;/validationError&gt;

&lt;/errors&gt;

&lt;/response&gt;

##### Duplicate Class Code

&lt;response&gt;

&lt;info&gt;

&lt;scoreKey&gt;74785202-1851-4350-bc30-23e132611f60&lt;/scoreKey&gt;

&lt;/info&gt;

&lt;invalidRequest&gt;

&lt;request&gt;&lt;!\[CDATA\[&lt;?xml version='1.0'
encoding='UTF-8'?&gt;&lt;score
version="1.0"&gt;&lt;info&gt;&lt;customer&gt;fwci&lt;/customer&gt;&lt;solution&gt;insureright&lt;/solution&gt;&lt;submission&gt;scoring&lt;/submission&gt;&lt;scoreKey&gt;74785202-1851-4350-bc30-23e132611f60&lt;/scoreKey&gt;&lt;/info&gt;&lt;inputs
level="insured"&gt;&lt;agency&gt;111777&lt;/agency&gt;&lt;underwriter&gt;LUMIERE
CLARE&lt;/underwriter&gt;&lt;audit\_method\_code&gt;P&lt;/audit\_method\_code&gt;&lt;available\_history\_1&gt;Y&lt;/available\_history\_1&gt;&lt;available\_history\_2&gt;Y&lt;/available\_history\_2&gt;&lt;available\_history\_3&gt;Y&lt;/available\_history\_3&gt;&lt;available\_history\_4&gt;Y&lt;/available\_history\_4&gt;&lt;available\_history\_indemnity\_1&gt;Y&lt;/available\_history\_indemnity\_1&gt;&lt;available\_history\_indemnity\_2&gt;Y&lt;/available\_history\_indemnity\_2&gt;&lt;available\_history\_indemnity\_3&gt;Y&lt;/available\_history\_indemnity\_3&gt;&lt;available\_history\_indemnity\_4&gt;Y&lt;/available\_history\_indemnity\_4&gt;&lt;claim\_amount\_total\_incurred\_1&gt;9&lt;/claim\_amount\_total\_incurred\_1&gt;&lt;claim\_amount\_total\_incurred\_2&gt;9&lt;/claim\_amount\_total\_incurred\_2&gt;&lt;claim\_amount\_total\_incurred\_3&gt;8&lt;/claim\_amount\_total\_incurred\_3&gt;&lt;claim\_amount\_total\_incurred\_4&gt;0&lt;/claim\_amount\_total\_incurred\_4&gt;&lt;experience\_mod\_factor\_initial&gt;2.43&lt;/experience\_mod\_factor\_initial&gt;&lt;insured\_name&gt;Risk
9 Misclass 6 PI
8&lt;/insured\_name&gt;&lt;new\_renew\_flag&gt;R&lt;/new\_renew\_flag&gt;&lt;non\_zero\_claim\_count\_1&gt;9&lt;/non\_zero\_claim\_count\_1&gt;&lt;non\_zero\_claim\_count\_2&gt;9&lt;/non\_zero\_claim\_count\_2&gt;&lt;non\_zero\_claim\_count\_3&gt;8&lt;/non\_zero\_claim\_count\_3&gt;&lt;non\_zero\_claim\_count\_4&gt;0&lt;/non\_zero\_claim\_count\_4&gt;&lt;non\_zero\_claim\_count\_indemnity\_1&gt;9&lt;/non\_zero\_claim\_count\_indemnity\_1&gt;&lt;non\_zero\_claim\_count\_indemnity\_2&gt;9&lt;/non\_zero\_claim\_count\_indemnity\_2&gt;&lt;non\_zero\_claim\_count\_indemnity\_3&gt;8&lt;/non\_zero\_claim\_count\_indemnity\_3&gt;&lt;non\_zero\_claim\_count\_indemnity\_4&gt;0&lt;/non\_zero\_claim\_count\_indemnity\_4&gt;&lt;original\_policy\_term\_number&gt;lr\_2.55-plrr\_adj
2.30-plrr\_adj\_flr
2.30&lt;/original\_policy\_term\_number&gt;&lt;policy\_state\_code&gt;KY&lt;/policy\_state\_code&gt;&lt;policy\_zip\_code&gt;40143&lt;/policy\_zip\_code&gt;&lt;term\_effective\_date&gt;2014-01-01&lt;/term\_effective\_date&gt;&lt;inputChildren
level="class"&gt;&lt;class&gt;&lt;term\_effective\_date&gt;2014-01-01&lt;/term\_effective\_date&gt;&lt;original\_policy\_term\_number&gt;lr\_2.55-plrr\_adj
2.30-plrr\_adj\_flr
2.30&lt;/original\_policy\_term\_number&gt;&lt;class\_code&gt;2587&lt;/class\_code&gt;&lt;payroll\_amount\_initial&gt;35858&lt;/payroll\_amount\_initial&gt;&lt;state\_code&gt;KY&lt;/state\_code&gt;&lt;/class&gt;&lt;class&gt;&lt;term\_effective\_date&gt;2014-01-01&lt;/term\_effective\_date&gt;&lt;original\_policy\_term\_number&gt;lr\_2.55-plrr\_adj
2.30-plrr\_adj\_flr
2.30&lt;/original\_policy\_term\_number&gt;&lt;class\_code&gt;8810&lt;/class\_code&gt;&lt;payroll\_amount\_initial&gt;10000&lt;/payroll\_amount\_initial&gt;&lt;state\_code&gt;KY&lt;/state\_code&gt;&lt;/class&gt;&lt;class&gt;&lt;term\_effective\_date&gt;2014-01-01&lt;/term\_effective\_date&gt;&lt;original\_policy\_term\_number&gt;lr\_2.55-plrr\_adj
2.30-plrr\_adj\_flr
2.30&lt;/original\_policy\_term\_number&gt;&lt;class\_code&gt;8810&lt;/class\_code&gt;&lt;payroll\_amount\_initial&gt;10000&lt;/payroll\_amount\_initial&gt;&lt;state\_code&gt;KY&lt;/state\_code&gt;&lt;/class&gt;&lt;/inputChildren&gt;&lt;/inputs&gt;&lt;/score&gt;\]\]&gt;&lt;/request&gt;

&lt;/invalidRequest&gt;

&lt;errors count="1"&gt;

&lt;validationError&gt;

&lt;name&gt;uniqueKey&lt;/name&gt;

&lt;description&gt;keys are repeated multiple(0)
times&lt;/description&gt;

&lt;field/&gt;

&lt;value/&gt;

&lt;/validationError&gt;

&lt;/errors&gt;

&lt;/response&gt;

##### Required Field Violation

&lt;response&gt;

&lt;info&gt;

&lt;scoreKey&gt;746c4a66-f1cf-4bc4-9064-0bd877a8001d&lt;/scoreKey&gt;

&lt;/info&gt;

&lt;invalidRequest&gt;

&lt;request&gt;&lt;!\[CDATA\[&lt;?xml version='1.0'
encoding='UTF-8'?&gt;&lt;score
version="1.0"&gt;&lt;info&gt;&lt;customer&gt;fwci&lt;/customer&gt;&lt;solution&gt;insureright&lt;/solution&gt;&lt;submission&gt;scoring&lt;/submission&gt;&lt;scoreKey&gt;746c4a66-f1cf-4bc4-9064-0bd877a8001d&lt;/scoreKey&gt;&lt;/info&gt;&lt;inputs
level="insured"&gt;&lt;agency&gt;111777&lt;/agency&gt;&lt;underwriter&gt;LUMIERE
CLARE&lt;/underwriter&gt;&lt;audit\_method\_code&gt;P&lt;/audit\_method\_code&gt;&lt;available\_history\_1&gt;Y&lt;/available\_history\_1&gt;&lt;available\_history\_2&gt;Y&lt;/available\_history\_2&gt;&lt;available\_history\_3&gt;Y&lt;/available\_history\_3&gt;&lt;available\_history\_4&gt;Y&lt;/available\_history\_4&gt;&lt;available\_history\_indemnity\_1&gt;Y&lt;/available\_history\_indemnity\_1&gt;&lt;available\_history\_indemnity\_2&gt;Y&lt;/available\_history\_indemnity\_2&gt;&lt;available\_history\_indemnity\_3&gt;Y&lt;/available\_history\_indemnity\_3&gt;&lt;available\_history\_indemnity\_4&gt;Y&lt;/available\_history\_indemnity\_4&gt;&lt;claim\_amount\_total\_incurred\_1&gt;9&lt;/claim\_amount\_total\_incurred\_1&gt;&lt;claim\_amount\_total\_incurred\_2&gt;9&lt;/claim\_amount\_total\_incurred\_2&gt;&lt;claim\_amount\_total\_incurred\_3&gt;8&lt;/claim\_amount\_total\_incurred\_3&gt;&lt;claim\_amount\_total\_incurred\_4&gt;0&lt;/claim\_amount\_total\_incurred\_4&gt;&lt;experience\_mod\_factor\_initial&gt;2.43&lt;/experience\_mod\_factor\_initial&gt;&lt;insured\_name&gt;Risk
9 Misclass 6 PI
8&lt;/insured\_name&gt;&lt;new\_renew\_flag&gt;R&lt;/new\_renew\_flag&gt;&lt;non\_zero\_claim\_count\_1&gt;9&lt;/non\_zero\_claim\_count\_1&gt;&lt;non\_zero\_claim\_count\_2&gt;9&lt;/non\_zero\_claim\_count\_2&gt;&lt;non\_zero\_claim\_count\_3&gt;8&lt;/non\_zero\_claim\_count\_3&gt;&lt;non\_zero\_claim\_count\_4&gt;0&lt;/non\_zero\_claim\_count\_4&gt;&lt;non\_zero\_claim\_count\_indemnity\_1&gt;9&lt;/non\_zero\_claim\_count\_indemnity\_1&gt;&lt;non\_zero\_claim\_count\_indemnity\_2&gt;9&lt;/non\_zero\_claim\_count\_indemnity\_2&gt;&lt;non\_zero\_claim\_count\_indemnity\_3&gt;8&lt;/non\_zero\_claim\_count\_indemnity\_3&gt;&lt;non\_zero\_claim\_count\_indemnity\_4&gt;0&lt;/non\_zero\_claim\_count\_indemnity\_4&gt;&lt;original\_policy\_term\_number&gt;lr\_2.55-plrr\_adj
2.30-plrr\_adj\_flr
2.30&lt;/original\_policy\_term\_number&gt;&lt;policy\_state\_code&gt;KY&lt;/policy\_state\_code&gt;&lt;policy\_zip\_code&gt;40143&lt;/policy\_zip\_code&gt;&lt;term\_effective\_date&gt;&lt;/term\_effective\_date&gt;&lt;inputChildren
level="class"&gt;&lt;class&gt;&lt;term\_effective\_date&gt;&lt;/term\_effective\_date&gt;&lt;original\_policy\_term\_number&gt;lr\_2.55-plrr\_adj
2.30-plrr\_adj\_flr
2.30&lt;/original\_policy\_term\_number&gt;&lt;class\_code&gt;2587&lt;/class\_code&gt;&lt;payroll\_amount\_initial&gt;35858&lt;/payroll\_amount\_initial&gt;&lt;state\_code&gt;KY&lt;/state\_code&gt;&lt;/class&gt;&lt;class&gt;&lt;term\_effective\_date&gt;&lt;/term\_effective\_date&gt;&lt;original\_policy\_term\_number&gt;lr\_2.55-plrr\_adj
2.30-plrr\_adj\_flr
2.30&lt;/original\_policy\_term\_number&gt;&lt;class\_code&gt;8810&lt;/class\_code&gt;&lt;payroll\_amount\_initial&gt;10000&lt;/payroll\_amount\_initial&gt;&lt;state\_code&gt;KY&lt;/state\_code&gt;&lt;/class&gt;&lt;/inputChildren&gt;&lt;/inputs&gt;&lt;/score&gt;\]\]&gt;&lt;/request&gt;

&lt;/invalidRequest&gt;

&lt;errors count="3"&gt;

&lt;validationError&gt;

&lt;name&gt;required&lt;/name&gt;

&lt;description&gt;A required value for 'term\_effective\_date' was not
provided&lt;/description&gt;

&lt;level&gt;class&lt;/level&gt;

&lt;key&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:original\_policy\_term\_number&lt;/vglItm&gt;

&lt;vglItm&gt;lr\_2.55-plrr\_adj 2.30-plrr\_adj\_flr 2.30&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:original\_policy\_term\_number&lt;/vglItm&gt;

&lt;vglItm&gt;lr\_2.55-plrr\_adj 2.30-plrr\_adj\_flr 2.30&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:class\_code&lt;/vglItm&gt;

&lt;vglItm&gt;8810&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:state\_code&lt;/vglItm&gt;

&lt;vglItm&gt;KY&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;/key&gt;

&lt;field&gt;termEffectiveDate&lt;/field&gt;

&lt;constraint&gt;requiredField&lt;/constraint&gt;

&lt;/validationError&gt;

&lt;validationError&gt;

&lt;name&gt;required&lt;/name&gt;

&lt;description&gt;A required value for 'term\_effective\_date' was not
provided&lt;/description&gt;

&lt;level&gt;class&lt;/level&gt;

&lt;key&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:original\_policy\_term\_number&lt;/vglItm&gt;

&lt;vglItm&gt;lr\_2.55-plrr\_adj 2.30-plrr\_adj\_flr 2.30&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:original\_policy\_term\_number&lt;/vglItm&gt;

&lt;vglItm&gt;lr\_2.55-plrr\_adj 2.30-plrr\_adj\_flr 2.30&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:class\_code&lt;/vglItm&gt;

&lt;vglItm&gt;2587&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:state\_code&lt;/vglItm&gt;

&lt;vglItm&gt;KY&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;/key&gt;

&lt;field&gt;termEffectiveDate&lt;/field&gt;

&lt;constraint&gt;requiredField&lt;/constraint&gt;

&lt;/validationError&gt;

&lt;validationError&gt;

&lt;name&gt;required&lt;/name&gt;

&lt;description&gt;A required value for 'term\_effective\_date' was not
provided&lt;/description&gt;

&lt;level&gt;insured&lt;/level&gt;

&lt;key&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;

&lt;vglItm&gt;:original\_policy\_term\_number&lt;/vglItm&gt;

&lt;vglItm&gt;lr\_2.55-plrr\_adj 2.30-plrr\_adj\_flr 2.30&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;/vglItm&gt;

&lt;/key&gt;

&lt;field&gt;termEffectiveDate&lt;/field&gt;

&lt;constraint&gt;requiredField&lt;/constraint&gt;

&lt;/validationError&gt;

&lt;/errors&gt;

&lt;/response&gt;

##### Credential Violation

&lt;data contentType="null"
contentLength="16"&gt;&lt;!\[CDATA\[401:Unauthorized\]\]&gt;&lt;/data&gt;

### Appendix D – *Predict* Report Download

The PWS *Predict* Report Download gives clients a way to easily download
their Insureright® *Predict* Reports from the InsureRight production
system and store them on their local environment in PDF format.

##### PWS Predict Report Download Flow

Once a client has submitted and scored valid data, the PWS *Predict*
Report Download works as follows:

1.  The client extracts the scoreID from the scoring response.

<!-- -->

1.  The client submits a web service request for the *Predict* Report.

2.  The *Predict* Report is delivered as a PWS response.

3.  The client uses a Base64 decoder to translate the file.

4.  The client saves the file in PDF format to their local environment.

##### PWS Predict Report Download Requirements

Requests to the PWS are designed as RESTful requests. Responses contain
the report data in binary format.

Web service requests are submitted as an HTTP GET to the following URL:

`[server]/solutions/[solution]/scoring/[reportID or scoreID]`

For example:

`https://insureright.valen.com/solutions/insureright/scoring/12345?format=pdf`

In this example the **solution** is InsureRight. A number of different
types of submissions may be made to the InsureRight solution, but in
this case we are making a **scoring** submission, which requires the
‘reportID’ or ‘scoreID’ be sent to the production system.

##### Request Data

The PWS request must conform to the following standards:

Method = GET

Endpoint = `<https://insureright.valen.com>`

Resource = /solutions/insureright/scoring/{scoreID}

Parameters = ?format=pdf

**Web Service Style:** The PWS uses HTTPS and supports RESTful requests.
The PWS processes requests and returns results synchronously.

**Authentication:** The request should contain a HTTP Basic
Authentication header containing a user name and valid credentials. If
credentials are invalid an Unauthorized (HTTP 403) status code will be
returned.

### Appendix E – Test Tools

We use cURL or Postman to test our request content.

The curl utility can be downloaded at
<http://curl.haxx.se/download.html> for various operating systems.

Postman is an app for Chrome or Mac and can be found at
<https://www.getpostman.com/>

##### Curl

Given a valid xml file (see the example request in [Appendix
A](#_Appendix_A_-)), this is a sample request.

curl –u \[username\]:\[password\] -X POST -d @valid-request.xml -H
'Content-Type: application/xml'
[https://insureright.valen.com/solutions/&lt;customer&gt;insureright/scoring](https://insureright.valen.com/solutions/%3ccustomer%3einsureright/scoring)

###### Postman

We have a library of sample requests at
<https://www.getpostman.com/collections/da164d2bbcfb55623635>

Import the library into Postman, click on the Authorization tab then
follow these steps.

1.  Enter the username and password for your Valen webservices user
    account

2.  Hit Update Request

3.  Click the “Body” tab

![](media/image5.png){width="6.5in" height="2.7708333333333335in"}

While on the “Body” Tab, enter valid values into the xml (you may need
to select the “Raw” radio tab to see the xml).

Hit Send.


### Appendix Q - Disclaimers and Legalese

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

##### Publication Date

February 24, 2015

| Revision | Date | Author(s) | Description |
|----------|------|-----------|-------------|
|1|02/24/2015|Valen Support|Initial version for InsureRight 3.0 Predict Platform|
|1.1|7/6/2016|Valen Support|Updated the examples to reflect code improvements.|
|1.2|8/2/2016|Valen Support|Updated the error messaging|
|1.3|11/14/2016|Valen Support|Converted to web document|

