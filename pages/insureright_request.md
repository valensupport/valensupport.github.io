---
layout: page
title: Building an InsureRight Request
permalink: insureright_request.html
sidebar: nav_sidebar
last_updated: December, 2016
summary: Building requests specifically for the InsureRight solution
---

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

##### Response Structure

```xml

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
|pi_score                    |Up to 5 descriptions and measures, associated with the explain nodes, providing information on the relative importance of reasons for a premium impact score|

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


### Test Plan

Here is a test plan for integration of Valen's InsureRight Scoring API. Each required test must pass for the integration to be completed. Data and Authentication will need to be provided for each request, sampled here in cURL.

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Scoring|`curl --request POST --url 'https://insureright.valen.com/solutions/insureright/scoring' –u [username]:[password] --header "content-type: application/xml" --data [data here]`| 200 OK: Scoring Response here|
|Retrieve Without Params|`curl --request POST --url 'https://insureright.valen.com/solutions/scores/query/insureright/scoring' –u [username]:[password] --header 'content-type: text/plain'`|200 OK: Response will be a list of scored records. Max is 1000 records long|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.retrieve_with_params }}">Retrieve With Params</a>|`curl --request POST --url 'https://insureright.valen.com/solutions/scores/query/insureright/scoring?element=insured%2Cpolicy_state_code%2Cvalue%2Ccontains%2CKY' –u [username]:[password] --header 'content-type: text/plain'`|200 OK: Response will be a list of scored records limited according to query param|
|Retrieve PDF|`curl --request POST --url 'https://insureright.valen.com/solutions/insureright/scoring/104557?format=pdf' –u [username]:[password] --header 'content-type: application/xml'`| 200 OK: PDF contents will be returned in Base 64 format|