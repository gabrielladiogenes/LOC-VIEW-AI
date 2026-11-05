# LOC VIEW AI
## 05 — Testing and Validation

**Project:** LOC VIEW AI  
**Evaluated Version:** v5.9.3  
**Category:** Geolocation, Data Validation, and Geographic Intelligence  
**Technologies:** Python · Gradio · OpenStreetMap Nominatim · REST API  
**Development Environment:** Google Colab

---

### 1. Introduction

The functional validation of LOC VIEW AI was guided by a central question: whether the application can interpret Brazilian addresses and distinguish confirmed geographic matches from incomplete or inconsistent results.

Unlike an application that simply displays coordinates returned by an external service, LOC VIEW AI incorporates interpretation and verification rules before classifying a location as an exact match.

This characteristic makes it necessary to evaluate not only the ability to retrieve results, but also how the system responds to invalid inputs, conflicting information, and situations where the available data is insufficient.

This chapter presents the testing strategy, evaluated functional scenarios, reported results, and limitations of the available evidence for version v5.9.3.

### 2. Validation Objectives

The validation strategy was organized around five objectives.

#### 2.1 Verify Address Interpretation

Evaluate whether the application can process different representations of Brazilian addresses, identifying components such as street name, building number, municipality, state, and postal code.

#### 2.2 Verify Input Consistency

Evaluate whether invalid or contradictory information is identified before potentially incorrect results are produced.

#### 2.3 Verify Matching Criteria

Observe whether the system distinguishes results compatible with a complete address from matches limited to the street level.

#### 2.4 Verify Uncertainty Handling

Evaluate whether the application avoids presenting a property as precisely located when the requested building number cannot be confirmed.

#### 2.5 Verify Result Communication

Observe whether the interface presents responses and navigation options consistent with the validation status.

### 3. Testing Strategy

The verification process combines two complementary approaches: local tests of deterministic rules and functional scenarios executed through the application.

#### 3.1 Local Tests

Version v5.9.3 contains the `run_offline_tests()` function, designed to verify specific processing logic behaviors.

This approach allows certain rules to be evaluated without relying on a real-time request to the Nominatim API.

Local tests are relevant to normalization, interpretation, and comparison functions, where expected results can be established from controlled inputs.

The presence of this function in the source code should not be confused with evidence of complete test coverage or continuous automated execution.

#### 3.2 Manual Functional Tests

The second approach involves executing representative scenarios through the application's functional workflow.

These scenarios cover valid, invalid, incomplete, and potentially ambiguous inputs.

During development, ten functional scenarios were reported as passed in version v5.9.3.

The results presented in this document correspond to that manual evaluation record.

#### 3.3 Evaluation Scope

The documented validation focuses on the prototype's functional behavior.

This documentation does not establish formal code coverage metrics, load tests, security tests, statistical assessments of geographic accuracy, or systematic accessibility testing.

These evaluations are considered potential areas for future development.

### 4. Evaluated Functional Scenarios

The following matrix summarizes the ten functional scenarios reported during the consolidation of version v5.9.3.

| ID | Scenario | Primary Objective | Reported Result |
|---|---|---|---|
| T01 | Complete address in Fortaleza | Evaluate a query with complete address components | Passed |
| T02 | Complete address in São Paulo | Evaluate processing in another geographic context | Passed |
| T03 | Building number with an alphabetic suffix | Evaluate interpretation of numbers containing letters | Passed |
| T04 | Address with a correct postal code | Evaluate the use of consistent postal information | Passed |
| T05 | Invalid postal code | Evaluate rejection of malformed postal information | Passed |
| T06 | Conflicting postal codes | Evaluate detection of contradictory postal information | Passed |
| T07 | Street without a building number | Evaluate handling of an incomplete address | Passed |
| T08 | Avenue with a number in its name | Evaluate the distinction between street names and building numbers | Passed |
| T09 | Unconfirmed building number | Evaluate handling of insufficient matches | Passed |
| T10 | Incorrect municipality and state | Evaluate detection of geographic inconsistencies | Passed |

**Consolidated reported result: 10 out of 10 selected scenarios passed.**

This result describes the success of the selected cases, not a statistical accuracy rate for all Brazilian addresses.

### 5. Scenario Analysis

#### 5.1 T01 and T02 — Complete Addresses in Different Cities

Scenarios T01 and T02 evaluated queries containing complete addresses in Fortaleza and São Paulo.

The objective was to observe the application's behavior when processing inputs containing sufficient geographic information to search for a specific match.

Using different cities helped verify the workflow in more than one geographic context.

**Reported result:** both scenarios passed.

These results do not establish that all addresses in either city have adequate coverage in the geographic database.

#### 5.2 T03 — Building Number with an Alphabetic Suffix

Some addresses contain building numbers accompanied by letters, such as identifiers in the form `120A`.

This scenario was selected to evaluate whether the processing logic adequately preserves and interprets this characteristic.

The objective is to prevent the number's structure from being modified in a way that is incompatible with the requested address.

**Reported result:** scenario passed.

#### 5.3 T04 — Correct Postal Code

Scenario T04 evaluated the application's behavior when processing an address accompanied by consistent postal information.

The verification focused on using the postal code as a supporting component for interpretation and validation.

**Reported result:** scenario passed.

This result does not represent independent validation against the official Brazilian postal service database.

#### 5.4 T05 — Invalid Postal Code

Scenario T05 evaluated an input containing invalid postal information.

The expected behavior is for the application to identify the inconsistency and avoid proceeding with a query based on malformed data.

**Reported result:** scenario passed.

This type of verification helps reduce unnecessary queries and potentially unsuitable responses.

#### 5.5 T06 — Conflicting Postal Codes

Scenario T06 evaluated a situation in which the address contains a postal code different from the one entered in the dedicated field.

The objective was to verify whether the application identifies the contradiction between the supplied information.

**Reported result:** scenario passed.

Early conflict detection is relevant because individually valid information may become incompatible when used together.

#### 5.6 T07 — Street Without a Building Number

Scenario T07 evaluated an input that identifies the street but does not include a building number.

In this context, the system may have sufficient information to recognize a street but not to confirm a specific property.

The evaluation focused on handling this distinction.

**Reported result:** scenario passed.

#### 5.7 T08 — Avenue with a Number in Its Name

Scenario T08 evaluated an address whose street name contains a numerical expression.

This case is relevant because numbers may represent part of an official street or avenue name rather than identifying a property.

The objective was to verify the interpretation of these components without confusing their roles.

**Reported result:** scenario passed.

#### 5.8 T09 — Unconfirmed Building Number

Scenario T09 evaluated a situation in which the available geographic data cannot confirm the requested building number.

This is particularly relevant to the reliability principle adopted by the project.

The expected behavior is to avoid presenting an incomplete match as the property's exact location.

**Reported result:** scenario passed.

#### 5.9 T10 — Incorrect Municipality and State

Scenario T10 evaluated an input containing inconsistent geographic information.

The objective was to observe whether the application can identify incompatibilities between the requested components and the retrieved geographic results.

**Reported result:** scenario passed.

### 6. Interpretation of Results

The success of the ten selected scenarios indicates that, under the conditions observed during manual evaluation, the application demonstrated the expected behavior for the tested cases.

The scenarios cover different input categories:

- **Complete inputs:** addresses containing sufficient geographic components for searching and validation.
- **Format variations:** building numbers with suffixes and street names containing numerical expressions.
- **Postal inconsistencies:** invalid or conflicting postal codes.
- **Incomplete information:** missing building numbers or inability to confirm a specific property.
- **Geographic inconsistencies:** incompatible municipality or state information.

This diversity is relevant because it allows observation of both successful situations and situations in which the application should reject or limit a confirmation.

However, the results do not establish the proportion of real-world addresses that would be correctly identified across a broad population.

Establishing such a measure would require a reference dataset, sampling criteria, and appropriate quantitative metrics.

### 7. Evidence and Traceability

Technical documentation should distinguish between three types of evidence.

#### 7.1 Implementation Evidence

The source code for version v5.9.3 contains the functions and rules responsible for address processing and match evaluation.

This material allows the implementation to be inspected and the programmed criteria to be identified.

#### 7.2 Local Testing Evidence

The `run_offline_tests()` function documents the existence of local checks incorporated into the project.

A detailed assessment of their results requires executing the code and recording the resulting outputs.

#### 7.3 Functional Testing Evidence

The ten scenarios presented correspond to results reported during development.

This chapter does not include complete individual records of input values, API responses, application outputs, and execution timestamps for each scenario.

Therefore, the test matrix should be interpreted as a summarized functional evaluation record rather than an independent audit report.

### 8. Validation Limitations

The documented strategy has several important limitations.

#### 8.1 Small Sample Size

Ten scenarios allow specific behaviors to be observed but do not represent the full diversity of Brazilian addresses.

#### 8.2 Dependence on External Data

Geocoding results depend on the coverage and currency of OpenStreetMap data.

Future changes to this database may modify the responses obtained for the same query.

#### 8.3 Absence of Statistical Metrics

Formal metrics for precision, recall, false positive rates, or false negative rates were not calculated.

#### 8.4 Unquantified Automated Coverage

The existence of local tests does not establish that every execution path or error condition has been evaluated.

#### 8.5 Absence of Performance Testing

No systematic measurements of latency, maximum concurrent query volume, or behavior under load were documented.

#### 8.6 Absence of Independent Auditing

The functional results presented originate from the development process, without a documented formal external evaluation.

These limitations do not invalidate the observed results, but they define the boundaries of the conclusions that can be drawn from them.

### 9. Recommendations for Testing Improvements

A future validation phase could include the following improvements.

#### 9.1 Build a Reference Dataset

Organize a collection of addresses with previously verified expected results, covering different cities, states, and street types.

#### 9.2 Expand Automated Testing

Use a Python testing framework to verify interpretation, normalization, validation, and exception-handling rules.

#### 9.3 Simulate API Responses

Create tests using controlled responses to reproduce situations involving incomplete records, conflicting data, and communication failures.

#### 9.4 Maintain Reproducible Scenario Records

Document the input, expected result, observed response, and executed version for each test.

#### 9.5 Quantitatively Evaluate Matching Performance

Measure validation performance against a reference dataset, distinguishing correct matches, appropriate rejections, and classification errors.

#### 9.6 Test Performance and Availability

Evaluate response times, integration failures, and behavior under more demanding usage conditions while respecting the geocoding service's policies.

These recommendations represent proposed
