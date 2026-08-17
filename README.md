# iSign Attendance Platform - Architecture and API Documentation

Architecture and OpenAPI contracts for a multi-service attendance platform connecting an admin portal, Android attendance client, backend API, and Python verification service.

## Project status

This repository documents an actively developed integration project.

The current OpenAPI 3.0.3 specification defines:

* 44 API paths
* 58 HTTP operations
* 14 functional API categories
* JWT bearer authentication
* device registration and activation
* employee, roster, attendance, and location management
* backend-controlled attendance verification sessions
* internal backend-to-Python face-verification integration

The specification includes implemented, prototype, planned, and future-facing contracts. An endpoint appearing in this repository does not by itself prove that the corresponding production implementation is complete.

## Problem being solved

Attendance systems operating across multiple service locations need a consistent contract between:

* the administration portal
* shared Android attendance devices
* the main backend
* employee and roster data
* face-verification services
* attendance reporting and audit functions

This repository provides a central API specification to reduce ambiguity between those components and document how identity verification and attendance recording should flow through the system.

## Documented API areas

The specification is organized into these categories:

* Authentication
* User Management
* Role Management
* Service Locations
* Employee Management
* Attendance Management
* Roster Management
* Device Registration
* Attendance Verification
* Internal Verification
* Biometrics
* HR Integration
* Security and Audit
* Reports

## Architecture overview

```text
Admin portal
     |
     | REST API
     v
iSign backend API <------ Employee, roster, attendance, and location data
     |
     | Internal verification API
     v
Python verification service
     ^
     |
     | Verification-session workflow
     |
Android attendance client
```

### Component responsibilities

| Component                   | Responsibility                                                                                                                   |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| Admin portal                | Manage users, roles, locations, employees, rosters, attendance records, and biometric enrollment                                 |
| Android client              | Register and activate attendance devices, select employees, complete required verification steps, and submit verified attendance |
| Backend API                 | Enforce business rules, resolve trusted devices and employees, manage verification sessions, and persist attendance data         |
| Python verification service | Process face enrollment and selfie-based face-verification requests initiated by the backend                                     |
| OpenAPI specification       | Define shared endpoint paths, request and response models, authentication requirements, and integration boundaries               |

## Attendance verification flow

The mobile attendance flow is designed around backend-controlled verification sessions:

1. The Android device starts a verification session for a selected employee and attendance action.
2. The backend validates the device context and returns the required verification steps.
3. The client completes required steps such as employee secret-code verification and live-selfie upload.
4. The backend sends image-verification work to the internal Python verification service.
5. The client requests verification-session completion.
6. Verified check-in or check-out is submitted only after the backend reports a verified session.
7. The backend records the attendance result and returns the updated state.

The mobile client does not call the Python verification service directly.

## API specification

The source specification is:

```text
isign-i3cubes-api.yaml
```

Key verified specification details:

| Item                 |                                      Value |
| -------------------- | -----------------------------------------: |
| OpenAPI version      |                                      3.0.3 |
| API document version |                                      1.0.0 |
| API paths            |                                         44 |
| HTTP operations      |                                         58 |
| GET operations       |                                         20 |
| POST operations      |                                         24 |
| PUT operations       |                                          7 |
| DELETE operations    |                                          7 |
| Security scheme      | HTTP bearer authentication with JWT format |

## Interactive documentation

The repository includes a ReDoc page in:

```text
index.html
```

When GitHub Pages is enabled for the repository, the interactive documentation is expected at:

```text
https://amilawedikkara.github.io/isign-attendance-platform-docs/
```

The page loads the OpenAPI source through the relative path `./isign-i3cubes-api.yaml`, so the same page works through GitHub Pages and a local static server.

## My contribution

My work across the wider iSign project includes:

* whole-project architecture design
* API contract design and maintenance
* OpenAPI documentation
* Android application development
* mobile-backend integration
* Python FastAPI face-verification service development
* backend and API testing
* frontend testing and defect identification
* Git feature-branch, pull-request, merge, and delivery management

Within this repository, my contribution includes maintaining the OpenAPI contract as the integration reference for the Android client, backend, frontend, and Python verification service.

The wider iSign application repositories are owned and maintained by the i3Cubes team. This personal repository documents my architecture, API-design, integration, and testing work without republishing the team repositories as personal source code.

## Engineering decisions and trade-offs

### Contract-first integration

The OpenAPI document acts as a shared reference before or during implementation.

**Benefit:** Frontend, mobile, backend, and verification-service work can align on request fields, response structures, authentication, and endpoint ownership.

**Trade-off:** The specification must be continuously checked against real implementations. Documentation can become misleading when planned and implemented behaviour are not clearly distinguished.

### Backend-controlled verification steps

Verification sessions return required steps instead of forcing the Android client to assume one fixed process.

**Benefit:** Verification policy can change according to employee or backend configuration without requiring a new mobile flow for every case.

**Trade-off:** Mobile behaviour depends on stable backend enum values and correct session-state handling.

### Backend-mediated face verification

The Python face-verification endpoint is documented as an internal service boundary.

**Benefit:** The backend remains responsible for authorization, employee resolution, audit context, and attendance business rules.

**Trade-off:** The workflow depends on multiple networked services and requires clear timeout, retry, failure-classification, and observability policies.

### Transitional device identity

Some prototype contracts use `device_uuid` while the documented secure direction is a signed device token.

**Benefit:** It supports early integration and testing.

**Trade-off:** Device UUID alone is not sufficient as a long-term authentication mechanism.

## Security and privacy considerations

This repository documents authentication, employee data, attendance records, device identity, secret-code verification, and biometric workflows.

Important considerations include:

* production credentials must never be stored in the OpenAPI examples
* development infrastructure addresses should not be exposed publicly
* employee secret codes should be stored and compared using a secure hash design
* device authentication should move from transitional UUID-based identity to signed device credentials
* mobile-to-backend and backend-to-verification traffic should use HTTPS
* face images and biometric results require controlled retention, access, logging, and deletion policies
* public screenshots and examples must use synthetic employee and biometric data
* JWT examples in this repository are documentation placeholders, not working credentials

The public specification currently uses sanitized example credentials.

## Local use

No package installation is required to inspect the OpenAPI YAML.

Clone the repository:

```bash
git clone https://github.com/amilawedikkara/isign-attendance-platform-docs.git
cd isign-attendance-platform-docs
```

Open the specification directly:

```text
isign-i3cubes-api.yaml
```

To view the included ReDoc page locally, start a static file server from the repository root. One possible command is:

```bash
python -m http.server 8000
```

Then open:

```text
http://localhost:8000/
```

The OpenAPI specification is loaded through a local relative path. Internet access is still required because `index.html` loads the ReDoc JavaScript bundle from the ReDoc CDN.

## Deployment

This repository is intended for static documentation hosting through GitHub Pages.

It does not deploy:

* the Android application
* the admin portal
* the backend API
* the Python verification service
* a production database

The production server entry in the OpenAPI document is a contract reference. This repository does not verify production availability.

## Known limitations

* The specification contains implemented, prototype, planned, and future contracts without a machine-readable implementation-status field.
* There is no automated OpenAPI linting or validation workflow.
* There is no CI workflow checking broken references or invalid schema changes.
* The ReDoc page depends on a remotely hosted JavaScript bundle.
* No generated SDK or API client is maintained in this repository.
* No automated contract tests compare the specification with backend responses.
* No versioned release process is documented.
* No license file is currently included.
* Some security sections describe intended improvements rather than completed production controls.

## Future improvements

1. Add OpenAPI linting and schema validation in GitHub Actions.
2. Add implementation-status metadata for prototype, implemented, deprecated, and planned endpoints.
3. Add contract tests against a controlled backend environment.
4. Move reusable request and response structures into more shared component schemas.
5. Add architecture decision records for major integration choices.
6. Add sequence diagrams for registration, activation, Duty In, Duty Out, and biometric enrollment.
7. Establish semantic versioning and tagged documentation releases.
8. Add changelog automation for contract changes.
9. Add a license only after confirming ownership and permission with the i3Cubes project stakeholders.

## Related repositories

* Android application: maintained in the i3Cubes organization
* Frontend administration portal: maintained in the i3Cubes organization
* Main backend API: maintained in the i3Cubes organization
* Python verification service: `isign-attendance-verification-engine` in my personal GitHub account, currently private

## License

No license file is currently included.

The documentation and specification should not be assumed to permit unrestricted reuse, modification, or redistribution until ownership and licensing are confirmed with the i3Cubes project stakeholders.
