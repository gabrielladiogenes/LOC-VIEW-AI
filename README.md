# LOC VIEW AI

### Address Intelligence & Geographic Validation

**Python · Geocoding · Data Validation · OpenStreetMap · Gradio**

*A deterministic address-processing prototype designed to interpret Brazilian addresses, evaluate geographic matches, and provide access to location information.*

**Version:** 5.9.3  
**Project Type:** Independent Technical Portfolio Project  
**Documentation:** English | Português (Brasil)

---

## Overview

**LOC VIEW AI** is a Python-based geolocation application developed to transform Brazilian address inputs into geographic information through structured interpretation, normalization, and validation.

The application integrates with **OpenStreetMap Nominatim** to retrieve geographic information and generates external links to **Google Maps** and **Google Street View**.

Its main engineering objective is to distinguish between a location returned by a geocoding service and an address sufficiently supported by the available geographic data.

Rather than automatically treating every API response as an exact match, LOC VIEW AI applies deterministic comparison and validation rules to assess the returned information.

The project explores a practical challenge in data engineering and product development:

**How can an application provide useful geographic information without presenting incomplete data as verified facts?**

## Key Features

- **Brazilian Address Interpretation:** processes selected Brazilian address formats and extracts relevant components.
- **Address Normalization:** supports comparison between selected variations in street names and formatting.
- **Geographic Lookup:** retrieves geographic information using the Nominatim API.
- **Address Matching:** evaluates geographic responses against the requested address components.
- **Postal Code Consistency Checks:** identifies selected invalid and conflicting postal information.
- **Uncertainty Handling:** distinguishes insufficient geographic evidence from confirmed matches.
- **Geographic Coordinates:** provides latitude and longitude when available through the validated workflow.
- **External Map Access:** generates links to Google Maps and Google Street View.
- **Interactive Interface:** provides an application interface developed with Gradio.

## Technology Stack

| Technology | Role |
|---|---|
| Python | Application logic and data processing |
| Gradio | Interactive user interface |
| OpenStreetMap | Geographic data source |
| Nominatim API | Address geocoding |
| REST API | Communication with external geographic services |
| Google Maps | External geographic visualization |
| Google Street View | External access to panoramic street imagery |
| Google Colab | Development and execution environment |
| GitHub | Source code and technical documentation |

## How It Works

The application's workflow is organized around five responsibilities.

**1. Input Interpretation**

The application receives an address and processes relevant components, including street information, building number, municipality, state, and postal code when provided.

**2. Normalization**

Selected textual variations are standardized to support comparisons without unnecessarily changing the meaning of the original address.

**3. Geographic Retrieval**

The application queries Nominatim to obtain available geographic information associated with the address.

**4. Match Validation**

Returned information is evaluated against the requested address components.

The existence of coordinates alone does not establish that a specific building number has been confirmed.

**5. Result Presentation**

The application communicates the outcome and, when applicable, provides geographic coordinates and external links to mapping services.

### Core Engineering Principle

> A successful geocoding response is not necessarily a confirmed address match.

This principle guides the project's validation logic and its treatment of uncertain results.

## Getting Started

### Option 1 — Google Colab

The project includes a Jupyter notebook for use in a notebook-based Python environment.

1. Open `loc_view_ai.ipynb` from this repository.
2. Open the notebook in Google Colab.
3. Review and execute the notebook's installation and configuration cells.
4. Run the application cells according to their documented sequence.
5. Access the Gradio interface when it becomes available.

Execution may require installing dependencies or adapting configuration to the current environment.

### Option 2 — Python Source Code

The application source code is available in:

`loc_view_ai_5_9_3.py`

Developers can inspect the implementation, including address interpretation, normalization, geographic querying, validation, and interface components.

Local execution requires a compatible Python environment and the dependencies used by the application.

A standardized standalone installation procedure has not yet been documented or independently verified.

## Testing and Validation

Version v5.9.3 includes the `run_offline_tests()` function for selected deterministic checks.

The development record also includes **10 manually evaluated functional scenarios, all reported as passed**.

The scenarios cover:

- Complete addresses in Fortaleza and São Paulo.
- Building numbers with alphabetic suffixes.
- Correct, invalid, and conflicting postal codes.
- Addresses without building numbers.
- Street names containing numbers.
- Unconfirmed building numbers.
- Incorrect municipality and state information.

**Important:** these results describe the selected functional scenarios. They do not establish a 100% accuracy rate, comprehensive automated coverage, or statistically representative geographic performance.

See [Testing and Validation](docs/en/05-testing-and-validation.md) for details.

## Known Limitations

LOC VIEW AI v5.9.3 is a functional prototype, not a production-grade address verification service.

Its principal limitations include:

- Dependence on OpenStreetMap geographic coverage and data quality.
- Limited handling of unexpected or highly ambiguous address formats.
- No independent verification against the official Brazilian postal database.
- No guarantee that Google Street View displays the exact requested property facade.
- Dependence on external service availability and usage policies.
- No documented production-scale load testing.
- Limited functional testing evidence.

The application does not verify property ownership, legal status, or the physical existence of a building.

See [Limitations and Roadmap](docs/en/06-limitations-and-roadmap.md) for proposed improvements.

## Technical Documentation

The project includes a seven-chapter technical case study available in English and Brazilian Portuguese.

| Chapter | English | Português (Brasil) |
|---|---|---|
| 01 — Project Overview | [Read](docs/en/01-project-overview.md) | [Ler](docs/pt-BR/01-visao-geral.md) |
| 02 — System Architecture | [Read](docs/en/02-system-architecture.md) | [Ler](docs/pt-BR/02-arquitetura-do-sistema.md) |
| 03 — Development Journey | [Read](docs/en/03-development-journey.md) | [Ler](docs/pt-BR/03-historico-de-desenvolvimento.md) |
| 04 — Engineering Decisions | [Read](docs/en/04-engineering-decisions.md) | [Ler](docs/pt-BR/04-decisoes-de-engenharia.md) |
| 05 — Testing and Validation | [Read](docs/en/05-testing-and-validation.md) | [Ler](docs/pt-BR/05-testes-e-validacao.md) |
| 06 — Limitations and Roadmap | [Read](docs/en/06-limitations-and-roadmap.md) | [Ler](docs/pt-BR/06-limitacoes-e-roadmap.md) |
| 07 — Project Retrospective | [Read](docs/en/07-project-retrospective.md) | [Ler](docs/pt-BR/07-retrospectiva-do-projeto.md) |

The documentation covers architecture, development iterations, engineering trade-offs, testing evidence, limitations, proposed improvements, and lessons learned.

## Future Development

Potential next steps include:

1. Expanding automated and reproducible tests.
2. Building a verified reference dataset of Brazilian addresses.
3. Measuring matching performance and incorrect classifications.
4. Improving usability and accessibility.
5. Evaluating complementary geographic data sources.
6. Strengthening error handling and operational monitoring.
7. Assessing deployment requirements for continuous availability.

These are proposed improvements and are not part of the implemented functionality of v5.9.3.

## A Note on Artificial Intelligence

Despite the name **LOC VIEW AI**, version v5.9.3 does not implement a generative AI model, machine learning pipeline, autonomous agent, or computer vision system.

Its documented functionality relies on deterministic Python processing and geographic data retrieved through an external API.

Potential AI-assisted features may be investigated in future versions, subject to implementation and independent validation.

## Project Takeaways

LOC VIEW AI demonstrates practical work involving:

**Software Development:** Python programming, modular problem-solving, debugging, and interactive application development.

**Data Quality:** normalization, consistency checks, matching criteria, and uncertainty management.

**API Integration:** external service communication, geographic data interpretation, and integration constraints.

**Product Thinking:** balancing functional value, technical feasibility, reliability, and user expectations.

**Technical Communication:** documenting engineering decisions, test evidence, limitations, and future development priorities.

The project's central lesson is that **retrieving data is not the same as validating it**.

---

## Author

**Ana Gabriella dos Santos Diógenes**

Business Intelligence Undergraduate · Independent Project Developer

GitHub: [@gabrielladiogenes](https://github.com/gabrielladiogenes)

---

**LOC VIEW AI v5.9.3** — Independent Technical Portfolio Project.
