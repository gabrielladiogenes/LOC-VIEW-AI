# LOC VIEW AI
## 06 — Limitations and Roadmap

**Project:** LOC VIEW AI  
**Documented Version:** v5.9.3  
**Category:** Geolocation, Data Validation, and Geographic Intelligence  
**Technologies:** Python · Gradio · OpenStreetMap Nominatim · REST API  
**Development Environment:** Google Colab

---

### 1. Introduction

LOC VIEW AI was developed as a functional prototype focused on interpreting and validating Brazilian addresses, providing geographic information and access to external navigation resources.

Version v5.9.3 consolidates deterministic processing rules, integration with the Nominatim API, and a Gradio-based interface.

Although the project demonstrated satisfactory results in the selected functional scenarios, its implementation has limitations related to geographic coverage, text interpretation, dependence on external services, scalability, and the scope of validation.

Recognizing these limitations is essential for establishing realistic product expectations and guiding future development decisions.

This chapter presents the known constraints of the current version and proposes a technical and functional development roadmap.

The improvements described represent potential development opportunities, not already implemented features or delivery commitments.

### 2. Limitations of Version v5.9.3

#### 2.1 Dependence on OpenStreetMap Coverage

LOC VIEW AI uses geographic data provided through the Nominatim API, which is based on OpenStreetMap.

Result quality depends on the availability, accuracy, and currency of geographic records.

In some locations, the database may contain complete street and building number information. In others, it may provide only the street or an approximate location.

These differences in coverage limit the application's ability to confirm specific addresses.

**Consequence:** a real address may remain unconfirmed even when entered correctly.

**Potential improvement:** evaluate complementary geographic data sources and compare matching quality across different Brazilian regions.

#### 2.2 Limitations in Natural Language Interpretation

Address processing relies on explicit interpretation and normalization rules.

This approach provides predictability but does not guarantee understanding of every linguistic variation used in Brazil.

Inputs containing unusual abbreviations, spelling mistakes, informal references, or unexpected structures may not be interpreted correctly.

Potentially challenging inputs include landmark-based descriptions, colloquial street names, and incomplete addresses.

**Consequence:** certain inputs may require users to reformulate their requests.

**Potential improvement:** expand the rule set and investigate additional text interpretation techniques while keeping geographic validation independent.

#### 2.3 No Guarantee of Identifying the Exact Property Facade

The application generates external links to Google Maps and Google Street View.

However, panoramic imagery availability depends on the coverage and conditions of these external services.

Even when an address satisfies the application's confirmation criteria, the imagery displayed by Street View may have been captured from a nearby position.

The system does not perform visual facade recognition or verify that the displayed image corresponds exactly to the requested property.

**Consequence:** users may access panoramic imagery without obtaining an accurate view of the property's facade.

**Potential improvement:** investigate mechanisms for assessing panoramic imagery availability and proximity while respecting the technical requirements and terms of use of the relevant services.

#### 2.4 Dependence on External Services

Integrated functionality depends on Nominatim availability and, for visualization, Google Maps and Street View services.

Connection failures, temporary outages, API changes, or operational restrictions may affect the user experience.

The application does not control the infrastructure of these services.

**Consequence:** features may fail even when the local application code is operating correctly.

**Potential improvement:** implement additional observability, error-handling, and recovery mechanisms.

#### 2.5 Scalability Limitations

The current version was structured as a prototype running on Python and Gradio.

There is no documented evidence of load testing or an architecture designed for large numbers of concurrent users.

Furthermore, use of the public Nominatim service is subject to usage policies and operational limits.

**Consequence:** behavior observed during individual tests cannot reliably predict performance under intensive usage.

**Potential improvement:** evaluate dedicated infrastructure, caching mechanisms, request controls, and geocoding alternatives suitable for the expected query volume.

#### 2.6 Limited Testing Coverage

Version v5.9.3 includes local checks implemented in `run_offline_tests()` and ten manually executed functional scenarios reported as passed.

These checks provide initial evidence of the prototype's behavior but do not constitute a comprehensive assessment of geographic accuracy.

No statistical performance metrics, complete code coverage measurements, or systematic security and accessibility tests have been documented.

**Consequence:** observed results cannot be generalized to all Brazilian addresses.

**Potential improvement:** expand the test dataset, automate verification, and produce reproducible records.

#### 2.7 Deployment and Maintenance Limitations

Google Colab was used as the primary development and execution environment.

This environment is suitable for experimentation and demonstration but does not, by itself, represent permanent production infrastructure.

The current documentation also does not describe a complete process for continuous deployment, operational monitoring, or production version management.

**Consequence:** continuous public availability would require additional infrastructure and maintenance decisions.

**Potential improvement:** separate application components, structure dependency management, and evaluate hosting alternatives.

### 3. Product Scope Limitations

In addition to technical constraints, LOC VIEW AI has defined functional boundaries.

Version v5.9.3 was not developed to:

- Guarantee identification of every Brazilian address.
- Confirm property ownership, title, or legal status.
- Verify the current physical existence of a building.
- Visually identify facades through image recognition.
- Guarantee Google Street View imagery availability.
- Independently query the official Brazilian postal service database.
- Provide real-time geographic navigation.
- Replace official land registration or real estate documentation services.
- Generate results using generative artificial intelligence models.

These boundaries are important for preventing misunderstandings about the application's capabilities.

### 4. Technical and Product Risks

Future development planning considers risks that may affect reliability and user experience.

| Risk | Potential Impact | Proposed Strategy |
|---|---|---|
| Incomplete geographic data | Real addresses remain unconfirmed | Evaluate coverage and alternative sources |
| Incorrect matches | Inaccurate location identification | Expand testing and validation criteria |
| API unavailability | Temporary query failures | Error handling and monitoring |
| Usage limits | Restricted query volume | Request controls and appropriate infrastructure |
| Ambiguous inputs | Incorrect address interpretation | Improved normalization and user guidance |
| Limited Street View coverage | No useful facade imagery | Explicit communication of limitations |
| Interface issues | Usability difficulties | Usability and accessibility testing |
| Growth without adequate infrastructure | Performance degradation | Deployment and scalability planning |

The strategies presented are proposed mitigation measures, not controls proven to be implemented in the current version.

### 5. Principles for Product Evolution

Future development should preserve the principles that guided version v5.9.3.

#### 5.1 Reliability Before Coverage

Increasing the number of recognized addresses should not require accepting matches without sufficient supporting evidence.

#### 5.2 Transparency About Uncertainty

The interface should continue distinguishing confirmed, incomplete, and unavailable results.

#### 5.3 Validation Independent of Interpretation

Even if new text processing techniques are introduced, geographic confirmation should remain subject to verifiable criteria.

#### 5.4 Evidence-Driven Evolution

New features should be evaluated through reproducible tests and appropriate metrics.

#### 5.5 Technical Sustainability

Service, infrastructure, and integration choices should consider cost, maintenance, availability, and usage conditions.

### 6. Development Roadmap

The roadmap is organized into four proposed phases, considering technical dependencies and potential product value.

No delivery dates have been established for these phases.

#### Phase 1 — Reliability and Quality

**Priority: High**

**Objective:** strengthen functional validation and result traceability.

Proposed initiatives:

1. Build a reference dataset containing real addresses with verified expected results.
2. Expand automated testing for address interpretation and normalization.
3. Simulate API responses to test incomplete results and communication failures.
4. Record inputs, expected results, and observed responses.
5. Evaluate false positives, false negatives, and correct rejections.

**Suggested completion criterion:** availability of a reproducible test suite and initial validation performance metrics.

#### Phase 2 — User Experience

**Priority: High**

**Objective:** make the application clearer, more accessible, and more consistent.

Proposed initiatives:

1. Improve error messages and guidance for reformulating addresses.
2. Implement clearer visual indicators for different matching states.
3. Evaluate the experience on mobile devices.
4. Conduct accessibility checks, including keyboard navigation and readability.
5. Perform usability testing with external participants.

**Suggested completion criterion:** reduction of difficulties identified during usability testing and documentation of implemented improvements.

#### Phase 3 — Geographic Coverage and Integrations

**Priority: Medium**

**Objective:** investigate alternatives for improving the usefulness of geographic results.

Proposed initiatives:

1. Compare geocoding services using a controlled sample of Brazilian addresses.
2. Investigate complementary postal data sources.
3. Evaluate query strategies for non-standard address formats.
4. Investigate features for communicating panoramic imagery coverage limitations.
5. Assess the technical and contractual feasibility of additional integrations.

**Suggested completion criterion:** a documented comparative analysis covering quality, costs, restrictions, and benefits of each alternative.

#### Phase 4 — Architecture and Deployment

**Priority: Conditional on Product Evolution**

**Objective:** prepare the application for potential continuous availability.

Proposed initiatives:

1. Separate processing logic from the presentation layer.
2. Organize dependencies and environment configuration.
3. Implement operational logging and failure monitoring.
4. Evaluate hosting and infrastructure compatible with expected usage.
5. Investigate caching mechanisms and request controls.
6. Structure deployment, maintenance, and update processes.

**Suggested completion criterion:** a documented execution environment with operational checks and capacity aligned with defined requirements.

### 7. Prioritization Matrix

The proposed prioritization considers potential impact, relative effort, and technical dependencies.

The classifications below represent an initial qualitative assessment, not quantitative measurements.

| Initiative | Estimated Impact | Estimated Effort | Priority |
|---|---|---|---|
| Expand automated testing | High | Medium | High |
| Build a reference address dataset | High | Medium | High |
| Improve error messages | High | Low | High |
| Evaluate accessibility | High | Medium | High |
| Compare geocoding services | High | High | Medium |
| Investigate complementary postal data | Medium | Medium | Medium |
| Assess panoramic imagery coverage | Medium | High | Medium |
| Structure production infrastructure | High, if deployed | High | Conditional |

Each initiative's priority may be revised as new evidence, available resources, and usage objectives emerge.

### 8. Proposed Monitoring Indicators

Data-driven development requires criteria for evaluating whether improvements actually produce results.

The following indicators are proposed for future phases.

#### 8.1 Correct Match Rate

The proportion of correctly confirmed addresses within the reference dataset.

#### 8.2 False Positive Rate

The proportion of matches classified as exact that do not correspond to the expected address.

#### 8.3 Correct Rejection Rate

The proportion of invalid or unconfirmable inputs that receive the expected treatment.

#### 8.4 Response Time

The time required to process a request and display its result.

#### 8.5 Integration Failure Rate

The proportion of queries affected by communication errors or geographic service unavailability.

#### 8.6 Task Completion Rate

The proportion of participants who can use the interface and understand the result without additional assistance during usability testing.

#### 8.7 Automated Test Coverage

The proportion of relevant code exercised by the test suite, accompanied by an assessment of test case quality.

These indicators do not yet have formally established baseline values or quantitative targets.

Their use will depend on defining an appropriate measurement methodology.

### 9. Potential Future Applications

LOC VIEW AI was developed as a geographic interpretation and validation prototype.

However, its components may provide a foundation for investigations in different contexts.

#### 9.1 Real Estate Operations

Address validation may support preliminary property location and information verification activities.

It does not replace documentary, cadastral, or legal assessments.

#### 9.2 Cadastral Data Quality

Normalization and verification rules may inform processes for identifying inconsistencies in address databases.

#### 9.3 Logistics Operations

Address interpretation may help identify incomplete records before operational processes that depend on location information.

A real-world logistics application would require additional requirements and validation.

#### 9.4 Geographic Analysis

Geocoding components may support studies involving territorial information, provided that data quality and accuracy are evaluated according to the analysis objectives.

These examples represent potential opportunities, not implemented or validated commercial applications.

### 10. Criteria for a Potential Future Version

A new LOC VIEW AI version should be considered a meaningful improvement when it demonstrates verifiable progress compared with v5.9.3.

Potential criteria include:

- Documented expansion of test coverage.
- Demonstrable reduction in incorrect classifications.
- Observable improvements in input interpretation.
- Clearer communication of uncertain results.
- Verified usability and accessibility improvements.
- Greater stability in external service integration.
- Documentation updated and aligned with the source code.

Implementing new features alone should not be considered sufficient evidence of improved product quality.

### 11. Final Considerations

Version v5.9.3 represents a consolidated stage in the development of LOC VIEW AI, but it does not exhaust the project's potential for evolution.

The main identified limitations relate to dependence on external geographic data, constraints of deterministic interpretation, the absence of guaranteed exact facade imagery, and the need to strengthen validation evidence.

The proposed roadmap initially prioritizes reliability and user experience before advancing toward more complex integrations and larger-scale infrastructure.

This sequence seeks to balance functional value, implementation effort, and technical risks.

Future development should preserve the principle that a retrieved location does not necessarily represent a confirmed address.

The next and final chapter presents a development retrospective, bringing together lessons learned, challenges, applied competencies, and reflections on the process of building LOC VIEW AI.
