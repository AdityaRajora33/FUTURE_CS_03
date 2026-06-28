# FUTURE_CS_03: API Security Risk Analysis

API Security Risk Analysis report for **Future Interns Cyber Security Task 3 (2026)**, covering two public test APIs: **JSONPlaceholder** (unauthenticated mock REST API) and **ReqRes** (key-based authenticated API). All testing was performed using Postman, restricted to safe, read-only requests.

## Objective

Analyze two public test APIs to identify common API security risks, assess their authentication and access control mechanisms, explain the risks in plain business language, and recommend clear remediation steps, consistent with how a real AppSec consultancy would scope a client API audit.

## APIs Tested

| API | Base URL | Auth Model | Role in this Assessment |
|---|---|---|---|
| JSONPlaceholder | jsonplaceholder.typicode.com | None (open) | Represents an unauthenticated/legacy-style API, used to surface access control and exposure risks |
| ReqRes | reqres.in | API key (header-based) | Represents a key-protected API, used as a control to confirm auth enforcement works as intended |

## Tools Used

- **Postman**: primary tool for constructing, sending, and inspecting API requests/responses
- **Browser DevTools**: used for quick header and response spot-checks
- **Microsoft Word**: report generation
- **GitHub**: public documentation of findings and evidence

## Scope

**In scope:**
- Testing public/demo APIs only (JSONPlaceholder, ReqRes)
- Read-only requests: GET, and safe POST where explicitly allowed by the API
- Header, token, and response inspection
- Documentation-based analysis

**Out of scope:**
- No exploitation or authentication bypass attempts
- No flooding or denial-of-service testing. Rate-limit checks were limited to a small burst of requests (20) purely to observe whether throttling exists, not to disrupt the service
- No private or production systems were tested

## Methodology

1. Reviewed available API documentation for both APIs
2. Sent baseline requests in Postman to observe default behavior (no auth headers)
3. Inspected authentication requirements, response headers, and response body content
4. Tested object-level access control by modifying resource IDs in the URL path (e.g. `/users/1` → `/users/2`)
5. Tested authentication enforcement on ReqRes using no key, a key sent in the wrong location (query string), and a valid key sent correctly
6. Tested input validation by submitting non-numeric and negative IDs, and tested rate limiting by sending 20 rapid sequential requests to a single JSONPlaceholder endpoint

## Summary of Findings

| # | Finding | API | Severity | Type |
|---|---|---|---|---|
| 1 | Unauthenticated endpoint access | JSONPlaceholder | Medium | Risk |
| 2 | IDOR on `/users` endpoint | JSONPlaceholder | High | Risk |
| 3 | Excessive data exposure (PII, geo-coordinates) | JSONPlaceholder | Medium | Risk |
| 4 | IDOR pattern repeats on `/posts` and `/comments` | JSONPlaceholder | High | Risk |
| 5 | Auth correctly blocks unauthenticated requests | ReqRes | N/A | Positive control |
| 6 | Auth correctly rejects key sent via query string | ReqRes | N/A | Positive control |
| 7 | Auth correctly allows valid API key | ReqRes | N/A | Positive control |
| 8 | No rate limiting on repeated requests | JSONPlaceholder | Medium | Risk |
| 9 | Graceful input validation on malformed IDs | JSONPlaceholder | N/A | Positive control |

The most significant issue identified is a **broken object-level authorization (IDOR)** vulnerability, present consistently across the `/users`, `/posts`, and `/comments` endpoints on JSONPlaceholder. While JSONPlaceholder is a mock API with no real user data, this finding is reported because it reflects a pattern seen in real-world production APIs.

ReqRes, by contrast, correctly enforced authentication across every test condition (no key, key in wrong location, valid key) and handled malformed input safely, demonstrating the assessment is balanced and not solely focused on identifying problems.

## Priority Order for Remediation

1. Fix broken object-level authorization (Findings 2 & 4): highest severity, systemic, highest real-world impact
2. Add authentication to all data-returning endpoints (Finding 1): foundational control that several other fixes depend on
3. Reduce excessive data exposure in responses (Finding 3): lower effort, immediate risk reduction
4. Implement rate limiting (Finding 8): protects against abuse once the above are in place

## Repo Contents

- `API_Security_Risk_Analysis_Report.docx`: full report (scope, methodology, 9 detailed findings with severity, business impact, and remediation, plus Postman evidence screenshots)

## Disclaimer

This assessment was performed strictly within the read-only, ethical scope defined in the Future Interns Task 3 brief. No exploitation, bypass, or denial-of-service technique was used at any point. All findings are reported in the spirit of a professional, responsible API security audit: identifying risk and recommending fixes, not demonstrating exploitation.

## Reference Material

- [OWASP API Security Top 10](https://github.com/OWASP/API-Security)
- [API Security Checklist](https://github.com/shieldfy/API-Security-Checklist)

---

**Author:** Aditya Rajora
**Program:** BTech AI, Delhi Skill and Entrepreneurship University (DSEU)
**Internship:** Future Interns, Cyber Security Task 3 (2026)
