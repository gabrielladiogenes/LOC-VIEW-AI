# LOC VIEW AI
## 02 — System Architecture

**Project:** LOC VIEW AI  
**Analyzed Release:** v5.9.3  
**Architecture:** Python application with a web interface and external geocoding service integration  
**Core Technologies:** Python · Gradio · Requests · OpenStreetMap Nominatim  
**Development and Execution Environment:** Google Colab

---

### 1. Architecture Overview

LOC VIEW AI was developed as a Python-based geolocation application featuring a Gradio web interface and integration with OpenStreetMap's Nominatim API.

The architecture follows a centralized structure in which the application receives user input, interprets addresses, executes geographic queries, validates candidate results, and generates responses displayed through the interface.

The solution is organized into functional components with distinct responsibilities, although they are implemented within a single Python source file.

This approach enabled the development and testing of a functional prototype in a simplified execution environment, without requiring a separate application server or a proprietary database.

The system consists of six logical components:

1. **Presentation Layer:** User interface implemented with Gradio, HTML, and CSS.
2. **Address Interpretation Layer:** Extraction and normalization of address components.
3. **Integration Layer:** HTTP communication with the Nominatim API.
4. **Validation Layer:** Comparison between requested address attributes and returned geographic records.
5. **Response Layer:** Result classification, user feedback, coordinate presentation, and external link generation.
6. **Verification Layer:** Offline regression tests covering selected parsing and validation behaviors.

Separating these responsibilities into functions supports troubleshooting, maintainability, and future improvements.

### 2. Processing Workflow

The primary workflow begins when a user enters an address and initiates a search.

The `search_location_ui()` function coordinates the processing stages.

**Stage 1 — Input Collection**

The interface receives a textual street address and an optional postal code provided in a separate field.

**Stage 2 — Address Parsing**

The `parse_address()` function extracts relevant components, including street name, building number, municipality, state, neighborhood, and postal code when available.

**Stage 3 — Preliminary Validation**

The `postal_input()` function validates the format of the optional postal code.

The system also identifies conflicts between a postal code embedded in the address and one entered separately.

Incomplete or inconsistent inputs may be rejected before an external API request is performed.

**Stage 4 — Query Construction**

The application constructs multiple geographic search representations of the requested address.

**Stage 5 — Geographic Data Retrieval**

The `search_api()` function submits HTTP requests to Nominatim and retrieves candidate geographic records.

**Stage 6 — Candidate Validation**

The `check()` function evaluates returned records against the requested address, applying different criteria for exact-address and street-level matches.

**Stage 7 — Response Generation**

The application produces one of several possible outcomes: identified location, street identified without building-number confirmation, location not found, input validation error, or temporary search unavailability.

Only a validated exact-address candidate generates the success panel containing geographic coordinates and external navigation links.

### 3. Address Parsing and Normalization

Brazilian address interpretation is a central responsibility of the application.

Processing is performed through dedicated Python functions rather than a language model or an external text interpretation service.

#### 3.1 Text Normalization

The `norm()` function standardizes textual values through lowercase conversion, accent removal, replacement of special characters, and whitespace normalization.

This transformation reduces superficial differences between user-provided address information and records returned by the geocoding service.

#### 3.2 Street Name Normalization

The `normalize_street()` function expands selected street-type abbreviations and converts recognized written-out numerical expressions into their numeric equivalents.

For example, the application supports comparisons between:

- `Rua 25 de Março` and `Rua Vinte e Cinco de Março`.
- `Avenida 9 de Julho` and `Avenida Nove de Julho`.

The `street_equal()` function compares normalized street names.

In addition to exact textual equality, the implementation uses Python's `SequenceMatcher` for selected longer strings, requiring a similarity ratio of at least 0.94.

This mechanism provides limited tolerance for textual variations. It is not a semantic matching model and does not guarantee equivalence between arbitrary street names.

#### 3.3 Building Number Extraction

The `parse_address()` function uses regular expressions to identify and separate building numbers from street names.

The parser supports building numbers containing alphabetic suffixes, such as `1000A`.

A specific implementation concern is preventing numbers that belong to street names from being incorrectly interpreted as building numbers.

When a building number cannot be identified, the application returns a validation message requesting that information.

#### 3.4 Municipality and State Identification

The source code includes a mapping of Brazilian federative units and their abbreviations.

The `canonical_state()` function supports comparisons between state abbreviations and full names, such as `CE` and `Ceará`.

The parser identifies location components according to supported address structures.

This implementation targets selected Brazilian address formats rather than every possible representation of a physical address.

### 4. Nominatim API Integration

The application uses Nominatim, a geographic search service associated with OpenStreetMap data.

HTTP communication is implemented through Python's `requests` library.

The `search_api()` function submits requests to:

`https://nominatim.openstreetmap.org/search`

The implementation specifies parameters including:

- `format=jsonv2` — Requests structured JSON responses.
- `addressdetails=1` — Requests individual address attributes.
- `limit=20` — Limits the number of returned candidates per query.
- `countrycodes=br` — Restricts geographic searches to Brazil.

The application also defines a custom `User-Agent`, a 25-second request timeout, and a minimum interval of approximately 1.15 seconds between requests controlled by the application.

A `threading.Lock` coordinates request timing within the running Python process.

These measures support more controlled interaction with the external service.

Nevertheless, result availability, geographic coverage, and data quality remain dependent on Nominatim and the underlying OpenStreetMap dataset.

### 5. Geographic Validation Mechanism

Geographic validation is primarily implemented through the `check()` function.

The application distinguishes two matching levels.

#### 5.1 Exact-Address Matching

For a candidate to qualify as an exact-address match, the application checks:

- Country identified as Brazil.
- Municipality matching the requested municipality.
- State matching the requested state.
- Street name matching after normalization.
- Building number matching the requested number.

Postal codes are also compared when the user provides one and the returned geographic record includes that information.

Therefore, **postal-code validation does not constitute an independent verification against an official Brazilian postal registry**.

It is a consistency check based on the available information.

Similarly, a successful match indicates that the returned geographic record satisfied the application's validation criteria. It does not independently certify the physical existence of a building.

#### 5.2 Street-Level Matching

When the initial queries do not produce an exact-address match, the application performs additional street-level searches.

These searches are evaluated against the requested country, municipality, state, and street name without requiring an exact building-number match.

When a street-level match is found, the system communicates that the street was located but the requested building number could not be confirmed.

In this state, the application does not display the success panel containing coordinates and external navigation links.

This distinction reduces the risk of presenting geographic information with greater precision than the available records support.

### 6. User Interface, Responses, and Error Handling

The interface is implemented using `gr.Blocks`, input components, action buttons, and HTML output elements.

Custom CSS establishes the application's visual identity, field layout, result presentation, and responsive behavior for smaller screens.

Users can initiate a search through the primary action button or by submitting the address field.

The application generates different visual responses according to the processing outcome.

For exact-address matches, the `success_panel()` function displays location information and provides two external navigation links.

**Google Maps:** The URL is constructed from the address interpreted by the application.

**Google Street View:** The URL is constructed using coordinates returned by Nominatim and requests a panoramic view when available.

The application does not embed Street View photographs directly into its interface or automatically verify whether an available image represents the exact requested building facade.

For unconfirmed results, the `panel()` function generates explanatory feedback.

Input validation errors are handled through exceptions, while HTTP request failures are captured and communicated as temporary search unavailability.

The implementation uses `html.escape()` to escape selected textual values inserted into generated HTML and URL encoding for external navigation parameters.

### 7. Internal Testing and Verification

The source code includes a function named `run_offline_tests()`, which is executed before the Gradio interface is launched.

It uses assertions and exception checks to verify selected behaviors, including:

- Rejection of addresses without building numbers.
- Extraction of address components from a Brazilian address.
- Equivalence between street names containing numeric and written-out numerical expressions.
- Rejection of different street names.
- Postal-code normalization.
- Rejection of invalid postal-code formats.

These tests do not require Nominatim API requests.

Additionally, development included ten manually evaluated functional scenarios reported during validation of release v5.9.3.

Offline regression tests and manual functional scenarios serve complementary purposes and will be documented separately in the dedicated testing and validation chapter.

### 8. Architectural Decisions and Limitations

The architecture of release v5.9.3 reflects decisions appropriate for a functional prototype.

Using Python and Gradio allowed processing logic and the user interface to be implemented within an accessible development environment.

Nominatim integration eliminated the need to develop a proprietary geographic database, while introducing dependencies on external services and datasets.

Deterministic validation made the matching criteria explicit and inspectable, although interpretation remains limited to supported input patterns.

Conditional result presentation was adopted to distinguish exact-address matches from partially identified locations.

Architectural limitations of the analyzed release include the absence of proprietary persistent storage, dependence on network connectivity and external services, lack of an independent property-registration verification service, and execution through a Gradio session without documented dedicated production infrastructure.

The current architecture provides a foundation for future improvements in modularization, test coverage, observability, dependency management, and deployment.

### 9. Final Considerations

LOC VIEW AI v5.9.3 demonstrates the integration of text processing, API consumption, structured validation, and geographic information presentation within a functional application.

Its defining architectural characteristic is the logical separation between **finding a geographic record and accepting that record as a match for the requested address**.

This distinction guides the processing workflow, validation rules, and communication of results.

The next chapter examines the development journey, version iterations, technical challenges, and improvements that shaped the evolution of the product.
