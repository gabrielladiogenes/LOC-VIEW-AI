# LOC VIEW AI
## 03 — Development Journey

**Project:** LOC VIEW AI  
**Final Documented Version:** v5.9.3  
**Category:** Geolocation, Data Validation, and Geographic Intelligence  
**Technologies:** Python · Gradio · OpenStreetMap Nominatim · REST API  
**Development Environment:** Google Colab

---

### 1. Introduction

LOC VIEW AI was developed through an iterative process of implementation, functional evaluation, and refinement.

The project began with a relatively straightforward objective: enabling users to enter a Brazilian address and retrieve geographic information useful for locating the requested property.

However, development revealed an important technical challenge: retrieving coordinates from a geocoding service does not necessarily mean that the requested address has been accurately identified.

This observation guided the product's evolution, particularly in handling street names, building numbers, postal codes, and incomplete geographic matches.

Across the documented versions, the focus shifted from simply retrieving geographic results toward building a more rigorous validation workflow, supported by an interface capable of communicating different levels of confirmation.

This process culminated in version v5.9.3, consolidated after successive functional and visual improvements.

### 2. Development Strategy

LOC VIEW AI was developed in Python, using Google Colab as its primary execution environment and Gradio to provide a browser-accessible interface.

The project evolved through development cycles involving four main activities:

1. **Problem identification:** recognizing a functional inconsistency, an interpretation challenge, or an interface issue.
2. **Change implementation:** updating processing functions, validation rules, or visual components.
3. **Behavior verification:** executing representative scenarios to observe the results of each change.
4. **Refinement:** introducing further adjustments when the observed behavior did not meet expectations.

This approach allowed improvements to be incorporated progressively rather than requiring the entire solution to be implemented in a single development phase.

The history presented in this chapter combines changes recorded during development with characteristics identifiable in the final source code.

It does not constitute an exhaustive record of every commit, intermediate modification, or experiment conducted.

### 3. Functional Evolution — v5.7 Series

The v5.7 series focused on improving address interpretation and establishing stricter criteria for accepting geographic results.

#### 3.1 Problem: Street Names Containing Numbers

Brazilian addresses frequently contain numbers as part of an official street name.

Examples include:

- Rua 25 de Março.
- Rua Vinte e Cinco de Março.
- Avenida 9 de Julho.
- Avenida Nove de Julho.

These representations may refer to the same street but contain textual differences that complicate direct comparisons.

Additionally, an address interpretation system must distinguish numbers that belong to street names from those identifying individual properties.

#### 3.2 Implemented Solution

The development process introduced text normalization rules and conversion of certain numeric expressions written in words.

The final version includes the functions `norm()`, `normalize_street()`, and `street_equal()`, which standardize and compare street names.

Address interpretation also uses rules to separate building numbers from other address components.

These mechanisms were developed to reduce representational differences without automatically treating every similar result as a valid match.

#### 3.3 Evolution of Validation Criteria

Another concern during this stage was preventing approximate geographic results from being presented as exact confirmations.

Validation began considering structured attributes, including country, municipality, state, street name, and building number.

Postal codes were also incorporated into consistency checks when available.

The final implementation distinguishes exact address matches from street-level results and preserves that distinction in the information presented to users.

**Stage outcome:** improved text interpretation and the establishment of explicit criteria for geographic matching.

### 4. Functional Evolution — v5.8 Series

The v5.8 series expanded postal code handling and improved the presentation of navigation resources.

#### 4.1 Problem: Inconsistent Postal Information

An address may contain a postal code within its text while the user enters another postal code in a separate field.

These values may be individually valid but mutually inconsistent.

Inputs may also contain an incorrect number of digits or unexpected characters.

Without preliminary validation, such inconsistencies could produce unsuitable geographic queries.

#### 4.2 Solution: Optional Postal Code and Conflict Detection

Development introduced an optional postal code field, accompanied by specific interpretation and validation rules.

The `postal_input()` function checks the format of the postal code entered separately.

Processing also considers any postal code extracted from the full address.

When the supplied postal information conflicts, the application can reject the request before executing the geographic search.

In the final version, comparison against the postal code returned by Nominatim occurs when that information is available in the geographic record.

This validation is a consistency check, not an independent lookup against the official Brazilian postal service database.

#### 4.3 Evolution of Navigation Features

Another documented improvement was the separation of external links for Google Maps and Google Street View.

These resources serve different purposes:

**Google Maps:** enables users to search for and view a location using the interpreted address.

**Google Street View:** requests panoramic imagery based on coordinates returned by the geocoding service.

The final implementation uses the `links()` function to construct these URLs.

Providing a Street View link does not guarantee that imagery of the exact property facade is available.

#### 4.4 User Communication

The evolution also included adjustments to interface language, aiming to present results and messages more clearly.

Detailed technical explanations were concentrated in the documentation, while the interface prioritized guidance directly related to the user's task.

**Stage outcome:** improved input consistency and clearer presentation of navigation options.

### 5. Interface Evolution — v5.9 Series

The v5.9 series focused on visual presentation, component organization, and user experience.

#### 5.1 Initial Visual Implementation

The initial release in the v5.9 series introduced an interface with a more defined visual identity.

During review, certain elements were identified as inconsistent with the intended product presentation.

These included a visual demonstration or portfolio label and a gray outer container surrounding the input fields.

These elements were considered unnecessary for the intended user experience.

#### 5.2 Refinements in v5.9.1 and v5.9.2

The intermediate versions introduced CSS adjustments to improve component appearance.

However, the visual modifications did not completely remove the gray container.

This behavior demonstrated that the issue was not determined solely by the CSS properties applied to the fields.

The structure generated by Gradio's components also needed to be considered.

#### 5.3 Consolidated Solution in v5.9.3

Version v5.9.3 introduced the use of `container=False` in relevant Gradio components, allowing greater control over input field presentation.

The interface also adopted separate HTML labels and customized styles to achieve a more consistent visual composition.

The final version includes:

- Input fields with a clean appearance and simplified visual organization.
- A distinct LOC VIEW AI visual identity.
- Labels and messages organized around the user's task.
- Removal of the demonstration label.
- Layout adaptations for different screen sizes.
- Distinct response panels for confirmed and unconfirmed results.

**Stage outcome:** consolidation of a more visually consistent interface aligned with the product's functionality.

### 6. Consolidation of Version v5.9.3

Version v5.9.3 brought together the main improvements developed in previous stages.

Its operation integrates five core capabilities.

#### 6.1 Brazilian Address Interpretation

The system processes structured and semi-structured addresses, identifies relevant components, and applies specific normalization rules.

#### 6.2 Geographic Data Integration

Queries are performed through the Nominatim API using OpenStreetMap data.

The application evaluates returned records rather than assuming that the first result is necessarily correct.

#### 6.3 Match Validation

The implementation compares geographic attributes and distinguishes results compatible with a complete address from those identifying only a street.

#### 6.4 Handling Uncertain Situations

When a building number cannot be confirmed, the application avoids presenting the location as an exact match.

Input errors and API communication failures also have dedicated messages.

#### 6.5 Presentation and Navigation

The interface presents location information and provides external links when exact-match criteria are satisfied.

These capabilities constitute the documented functional scope of the final version.

### 7. Functional Validation of the Final Version

Following the refinements, version v5.9.3 was evaluated through ten manually executed functional scenarios reported during development.

The selected scenarios were:

| ID | Evaluated Scenario | Reported Result |
|---|---|---|
| T01 | Complete address in Fortaleza | Passed |
| T02 | Complete address in São Paulo | Passed |
| T03 | Building number with an alphabetic suffix | Passed |
| T04 | Address with a correct postal code | Passed |
| T05 | Invalid postal code | Passed |
| T06 | Conflicting postal codes | Passed |
| T07 | Street without a building number | Passed |
| T08 | Avenue with a number in its name | Passed |
| T09 | Unconfirmed building number | Passed |
| T10 | Incorrect municipality and state | Passed |

**Reported result: 10 out of 10 selected functional scenarios passed.**

These results reflect the manual evaluation of the specified scenarios and should not be interpreted as a guarantee of universal geographic accuracy.

The final version also contains local automated tests implemented in the `run_offline_tests()` function, targeting specific address interpretation and validation behaviors.

The methodology, results, and limitations of these checks will be detailed in Chapter 05 — Testing and Validation.

### 8. Key Lessons from the Development Process

The evolution of LOC VIEW AI highlighted several aspects of developing data-driven applications.

**Result quality depends on validation.**

A response provided by an API may be technically valid while remaining insufficient to confirm the information requested by the user.

**Data interpretation requires contextual awareness.**

Numbers appearing in addresses can represent different elements, such as part of a street name, a building number, or a postal code.

**Interface design influences result interpretation.**

Presenting coordinates or links without adequately distinguishing confirmation levels may imply a degree of accuracy that the underlying data does not support.

**Visual refinement also involves technical decisions.**

The issue identified with Gradio containers demonstrated the importance of understanding component behavior rather than relying exclusively on CSS modifications.

**Representative tests guide improvements.**

Using scenarios with valid, invalid, and ambiguous inputs helped evaluate different application behaviors.

### 9. Scope and Limitations of the Documented History

This chapter describes the known evolution of the project and the functionality observable in version v5.9.3.

Changes attributed to the v5.7, v5.8, and v5.9 series were organized based on records from the development process.

The final source code confirms the presence of certain functions and behaviors but does not independently establish the exact version in which each modification was introduced.

For this reason, the history does not provide release dates, previous performance metrics, or quantitative comparisons between versions without supporting evidence.

This distinction preserves the traceability and reliability of the technical documentation.

### 10. Final Considerations

The development of LOC VIEW AI demonstrates an evolution process guided by problem identification, solution implementation, and result evaluation.

Across the documented versions, the project advanced in three main dimensions:

- **Functional reliability:** more rigorous address interpretation and validation.
- **Data consistency:** improved handling of postal information and incomplete geographic matches.
- **User experience:** better interface organization and clearer communication of results.

Version v5.9.3 represents the consolidation of these improvements into a functional prototype.

The next chapter examines the main engineering decisions supporting the solution, including their rationale, alternatives, and technical consequences.
