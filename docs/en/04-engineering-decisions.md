# LOC VIEW AI
## 04 — Engineering Decisions

**Project:** LOC VIEW AI  
**Documented Version:** v5.9.3  
**Category:** Geolocation, Data Validation, and Geographic Intelligence  
**Technologies:** Python · Gradio · OpenStreetMap Nominatim · REST API  
**Development Environment:** Google Colab

---

### 1. Introduction

The development of LOC VIEW AI involved technical decisions related to address interpretation, integration with geographic services, result validation, and information presentation.

The primary engineering challenge was not simply converting an address into geographic coordinates, but determining when those coordinates could be presented as a reliable match.

This distinction influenced the application's architecture, validation criteria, and handling of uncertain situations.

This chapter examines the main decisions reflected in version v5.9.3, their rationale, technological alternatives, and consequences for the product's operation.

The alternatives discussed represent possible engineering approaches, not necessarily implementations experimentally evaluated during development.

### 2. Decision 01 — Use Deterministic Processing Instead of Generative AI

#### 2.1 Context

LOC VIEW AI receives textual information that may contain different formats, abbreviations, numbers, and geographic components.

One possible approach would be to use a language model to interpret addresses and determine whether the retrieved results match the user's request.

However, this approach would introduce a probabilistic component into a task requiring verifiable matching criteria.

#### 2.2 Adopted Solution

The application uses Python functions and explicit rules to interpret, normalize, and compare address components.

Functions identified in the implementation include:

- `parse_address()` — interprets input components.
- `norm()` — performs text normalization.
- `normalize_street()` — standardizes street names.
- `street_equal()` — compares street names.
- `canonical_state()` — standardizes state references.
- `check()` — evaluates geographic matches.

Integration with the Nominatim API provides geographic candidates, which are subsequently evaluated against the application's validation rules.

#### 2.3 Technical Rationale

The deterministic approach was appropriate for the prototype's scope because it enables:

- Explicit, inspectable validation rules.
- More predictable behavior for equivalent inputs.
- Local testing without querying external language models.
- Reduced dependency on additional services.
- More straightforward investigation of interpretation failures.

#### 2.4 Alternatives and Limitations

Language models could provide greater flexibility when interpreting informal inputs, but they would also require additional mechanisms to control inconsistent responses and validate generated information.

The adopted solution has limitations: explicit rules cannot anticipate every linguistic and structural variation found in Brazilian addresses.

**Final decision:** prioritize deterministic, verifiable validation without relying on generative artificial intelligence.

### 3. Decision 02 — Use OpenStreetMap Nominatim as the Geocoding Service

#### 3.1 Context

The application needed to convert Brazilian addresses into geographic records containing coordinates and structured attributes.

This required integration with a geocoding service.

#### 3.2 Adopted Solution

LOC VIEW AI uses the Nominatim API, based on OpenStreetMap data.

The `search_api()` function participates in communication with the service, while other functions process and verify the retrieved results.

#### 3.3 Technical Rationale

This choice enables the development of a prototype using open geographic data and an API that provides information useful for validation, such as municipality, state, street name, and, when available, building number and postal code.

The structure of the returned results makes it possible to evaluate individual attributes rather than relying exclusively on a formatted address string.

#### 3.4 Alternatives Considered in the Analysis

Other geocoding services could be used, including commercial solutions.

These alternatives may differ in geographic coverage, data quality, terms of use, availability, and cost.

No documented quantitative comparison between providers was conducted.

#### 3.5 Consequences and Limitations

Matching quality depends on the coverage and currency of OpenStreetMap data.

The absence of a building number in a returned record does not necessarily mean that the property does not exist.

Furthermore, the public Nominatim service has usage policies and operational limits that must be respected. A future product handling higher query volumes would require a dedicated infrastructure and capacity assessment.

**Final decision:** use Nominatim as a source of geographic candidates without treating it as an absolute authority for confirming every address.

### 4. Decision 03 — Separate Geocoding from Validation

#### 4.1 Context

A geocoding API may return an approximate result even when it cannot locate the complete requested address.

For example, a query containing a street name and building number may return only a representative point for the street.

If the application presented that result as an exact location, it could lead users to an incorrect interpretation.

#### 4.2 Adopted Solution

The architecture distinguishes between two operations:

**Geocoding:** retrieving geographic candidates and coordinates.

**Validation:** evaluating whether the returned attributes are compatible with the requested address.

The `check()` function participates in this evaluation stage.

The system considers geographic components including country, municipality, state, street name, building number, and postal information when available.

#### 4.3 Technical Rationale

This separation reduces reliance on the relevance ranking produced by the external service.

A result may be useful for identifying a street without providing sufficient evidence to confirm a specific property.

This distinction allows the application to communicate different levels of certainty.

#### 4.4 Consequences

Stricter validation may reject results that appear visually close to the intended address.

This behavior is a deliberate consequence of prioritizing the reliability of the information presented.

**Final decision:** a geographic result returned by an API must not automatically be considered an exact match.

### 5. Decision 04 — Normalize Data Before Comparison

#### 5.1 Context

Addresses may differ in accent marks, capitalization, abbreviations, and numerical representations.

Direct text comparisons can produce unsuitable results when equivalent inputs have different formats.

#### 5.2 Adopted Solution

LOC VIEW AI incorporates text normalization and comparison functions.

The implementation includes specific handling for certain numerical variations appearing in street names.

Conceptual example:

`Rua Vinte e Cinco de Março`

and

`Rua 25 de Março`

may require additional processing so that their representations can be considered equivalent during comparison.

#### 5.3 Technical Rationale

Normalization reduces superficial representational differences and contributes to more consistent validation.

It also allows comparison rules to be concentrated in dedicated functions, facilitating maintenance and verification.

#### 5.4 Limitations

Normalization does not constitute complete natural language understanding.

Unusual official street names, unsupported abbreviations, and regional ambiguities may still produce unconfirmed results.

**Final decision:** standardize data before comparing it while preserving explicit matching criteria.

### 6. Decision 05 — Validate Postal Codes as Supporting Information

#### 6.1 Context

A postal code can help narrow geographic interpretation but may also introduce inconsistencies.

A user may provide one postal code within the address and another in a separate field.

Additionally, a geographic record may not contain postal information.

#### 6.2 Adopted Solution

The application provides an optional postal code field and incorporates format and consistency checks.

The `postal_input()` function participates in postal information processing.

When a postal code appears in multiple parts of the input, the application can identify conflicts before executing a query.

During geographic result evaluation, the postal code returned by Nominatim is considered when available.

#### 6.3 Technical Rationale

This approach allows postal codes to serve as additional evidence without making them mandatory for every query.

It also reduces the likelihood of executing searches based on contradictory postal information.

#### 6.4 Limitations

The implementation does not independently validate postal codes against the official Brazilian postal service database.

The absence of postal code information in Nominatim records limits the checks that can be performed.

**Final decision:** treat postal codes as supporting consistency and validation information while respecting data availability.

### 7. Decision 06 — Do Not Present Incomplete Matches as Exact Addresses

#### 7.1 Context

One of the product's risks is communicating a greater degree of geographic precision than the retrieved result supports.

This risk is particularly relevant when the system finds the requested street but cannot confirm the building number.

#### 7.2 Adopted Solution

The application distinguishes confirmed situations from those in which the available match is insufficient.

When the building number cannot be adequately confirmed, the result is not presented as the property's exact location.

The interface uses response panels and messages to communicate the verification status.

#### 7.3 Technical Rationale

This decision reduces the risk of users interpreting approximate coordinates as the precise identification of a property facade.

It also preserves the distinction between retrieved data and conclusions actually supported by validation.

#### 7.4 Consequences

Some real addresses may remain unconfirmed because the available geographic data is insufficient.

Within the adopted scope, this limitation is preferable to presenting a confirmation without adequate supporting evidence.

**Final decision:** prioritize explicit communication of uncertainty rather than assuming precision.

### 8. Decision 07 — Use Google Maps and Street View as External Resources

#### 8.1 Context

The product aims to facilitate geographic visualization of the searched address.

One alternative would be to embed maps and panoramic imagery directly into the interface.

However, this approach could require additional integrations, credential management, and evaluation of service usage conditions.

#### 8.2 Adopted Solution

LOC VIEW AI generates external links to Google Maps and Google Street View.

The `links()` function constructs these URLs.

Visualization takes place through the external service rather than through images captured or stored by the application.

#### 8.3 Technical Rationale

This solution keeps the prototype simpler and separates responsibility for geographic validation from responsibility for displaying maps and panoramic imagery.

It also eliminates the need to develop a custom geographic viewer.

#### 8.4 Limitations

The existence of a Street View link does not guarantee panoramic coverage for the requested location.

The image provided by Google may represent a nearby position rather than the exact property facade.

The application also depends on the availability and behavior of external services.

**Final decision:** provide access to geographic visualization through external links without claiming that the displayed image necessarily corresponds to the validated property.

### 9. Decision 08 — Use Gradio for the Prototype Interface

#### 9.1 Context

The project needed an interactive workflow for entering addresses and displaying results.

It was desirable for the interface to run within the Python environment used during development.

#### 9.2 Adopted Solution

The application uses `gr.Blocks` to structure the interface.

The implementation includes input components, HTML elements, custom styles, and functions responsible for presenting responses.

Identified functions include:

- `search_location_ui()`
- `panel()`
- `success_panel()`

#### 9.3 Technical Rationale

Gradio enabled direct integration between the interface and Python logic, reducing the need to develop a separate frontend.

This characteristic was appropriate for building and testing a functional prototype.

#### 9.4 Implementation Challenge

During visual refinement, certain default Gradio containers remained visible despite CSS modifications.

Version v5.9.3 introduced `container=False` in relevant components and separate HTML labels to improve presentation.

#### 9.5 Limitations

Although suitable for prototyping, Gradio does not eliminate the need to assess infrastructure, accessibility, security, and maintenance if the application is deployed as a production service.

**Final decision:** use Gradio to accelerate interface development, with customizations appropriate to the prototype's scope.

### 10. Decision 09 — Separate Local Tests from External Queries

#### 10.1 Context

Not every interpretation or validation rule requires a live API request to be tested.

Relying exclusively on tests connected to an external service could make it more difficult to identify the source of certain failures.

#### 10.2 Adopted Solution

The final version includes the `run_offline_tests()` function, designed to verify specific local behaviors.

Development also included manually executed functional scenarios to observe the application's integrated behavior.

#### 10.3 Technical Rationale

Distinguishing local tests from integration tests allows specific processing rules and complete application behavior to be evaluated separately.

Local tests can help identify regressions in deterministic rules without depending on external responses.

#### 10.4 Limitations

The presence of local tests does not demonstrate complete code coverage.

Similarly, the reported success of ten functional scenarios does not guarantee correct operation for every Brazilian address.

**Final decision:** combine local checks with representative functional scenarios while acknowledging the limitations of each approach.

### 11. Summary of Engineering Decisions and Trade-offs

| Decision | Main Benefit | Trade-off or Limitation |
|---|---|---|
| Deterministic processing | Verifiable rules | Less flexibility with unforeseen inputs |
| Nominatim / OpenStreetMap | Access to open geographic data | Dependence on data coverage and service policies |
| Separate geocoding and validation | More rigorous confirmation | Potential rejection of useful approximate results |
| Text normalization | More consistent comparisons | Limited coverage of linguistic variations |
| Supporting postal code validation | Detection of inconsistencies | Dependence on available postal data |
| Explicit uncertainty handling | More reliable communication | Some properties remain unconfirmed |
| External navigation links | Reduced integration complexity | Dependence on external services and coverage |
| Gradio interface | Python-integrated prototyping | Customization and deployment limitations |
| Local and functional testing | Verification of specific behaviors | Non-exhaustive coverage |

### 12. Architectural Evolution Considerations

The decisions described were appropriate for building a functional prototype but do not necessarily represent the ideal architecture for large-scale operation.

Future development could include:

- More explicit separation between the interface, business logic, and external service integration.
- Broader automated testing, including simulated API responses.
- Monitoring of failures, latency, and availability.
- Evaluation of alternative geocoding services and geographic coverage quality.
- Caching strategies and query-volume controls that comply with service usage conditions.
- Deployment in a dedicated environment with appropriate configuration and observability.
- Systematic accessibility and usability assessments.

These possibilities represent directions for future development, not features already implemented.

### 13. Final Considerations

The engineering decisions behind LOC VIEW AI were guided by one central principle: **retrieving a geographic result must not be confused with confirming an exact address**.

The architecture of version v5.9.3 seeks to apply this principle through structured interpretation, normalization, integration with geographic data, match validation, and explicit communication of uncertainty.

The choice of deterministic rules, accessible tools, and external services made it possible to consolidate a functional prototype without introducing complexity beyond its scope.

At the same time, the project demonstrates that application reliability depends both on the information available and on the decisions made to interpret and present it.

The next chapter presents the testing and validation strategy, evaluated scenarios, reported results, and limitations of the available evidence.
