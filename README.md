# Local Reconnaissance and Attack Surface Map

**Assignment:** Edvyro Ethical Hacking\
**Target:** OWASP Juice Shop running locally\
**Target URL:** `http://localhost:3000`\
**Assessment date:** 13 October 2026\
**Scope:** Local lab environment only

## Overview

This report documents a beginner-level reconnaissance exercise against a
local OWASP Juice Shop instance. The goal was to identify exposed web
routes and assets, record observable HTTP behavior and response headers,
and turn those observations into risk hypotheses for follow-up testing.

Only the local lab target was examined. No destructive actions,
credential attacks, or confirmed exploit attempts were performed.

## Lab Setup

-   Application: OWASP Juice Shop
-   Runtime: Docker container
-   Local port mapping: `127.0.0.1:3000` to container port `3000`
-   Base URL: `http://localhost:3000`

## Key Observations

-   The homepage responded with **HTTP 200 OK** and HTML content.
-   The application exposed JavaScript assets including `main.js` and
    `scripts.js`.
-   `/robots.txt` returned HTTP 200 and included `Disallow: /ftp`. A
    robots.txt directive is not an access-control mechanism.
-   `/sitemap.xml` returned HTML rather than a confirmed XML sitemap.
-   `/api-docs/` served a Swagger UI interface.
-   The visible API specification documented a `POST /orders` operation
    named `createCustomerOrder`.
-   An `OPTIONS /` request returned HTTP 204 and advertised several HTTP
    methods. This does not prove that every method is supported or
    usable on every route.
-   A request to a made-up path returned the HTML application shell,
    suggesting frontend fallback behavior. This should be verified by
    comparing response bodies and testing known routes.

## Selected Response Headers

  -------------------------------------------------------------------------------
  Header                          Observed value          Note
  ------------------------------- ----------------------- -----------------------
  `Access-Control-Allow-Origin`   `*`                     Broad CORS response;
                                                          assess actual
                                                          cross-origin behavior
                                                          before concluding risk.

  `X-Content-Type-Options`        `nosniff`               Present.

  `X-Frame-Options`               `SAMEORIGIN`            Present.

  `Cache-Control`                 `public, max-age=0`     Observed on the
                                                          homepage response.

  `X-Recruiting`                  `/#/jobs`               Exposes a jobs-related
                                                          route hint.

  `Feature-Policy`                `payment 'self'`        Legacy-style policy
                                                          header observed.
  -------------------------------------------------------------------------------

## Initial Risk Hypotheses

These are leads for further testing, not confirmed vulnerabilities:

1.  **API documentation exposure:** Determine whether the documentation
    is intended to be available in this environment and review the
    documented API's authentication and authorization behavior.
2.  **Broad CORS header:** Check the actual CORS policy and whether
    sensitive responses are accessible cross-origin; the wildcard header
    alone does not establish an exploitable issue.
3.  **Route discovery:** Review the application shell, JavaScript
    assets, and documented routes to build a fuller endpoint inventory.
4.  **Frontend fallback behavior:** Compare status codes and response
    bodies for valid and invalid paths so that missing routes are not
    mistaken for real endpoints.
5.  **HTTP methods:** Test allowed methods only against explicitly
    in-scope routes and record actual behavior rather than relying only
    on the `OPTIONS` response.

## Reproducible Commands

Run these commands only against the local lab instance:

``` powershell
curl.exe -I http://localhost:3000
curl.exe -i http://localhost:3000/robots.txt
curl.exe -i http://localhost:3000/sitemap.xml
curl.exe -i -X OPTIONS http://localhost:3000/
curl.exe -I http://localhost:3000/main.js
curl.exe -I http://localhost:3000/scripts.js
curl.exe -i http://localhost:3000/api-docs/
curl.exe -i http://localhost:3000/orders
```

## Limitations

-   Findings are based on responses observed during this reconnaissance
    pass.
-   No claim is made that a vulnerability has been exploited or
    confirmed.
-   The visible OpenAPI specification and observed responses should be
    rechecked if the lab version changes.
-   This report concerns a local training target and is not
    authorization to test any public system.

## Files

The full report is available in the repository alongside this README:

-   `Local_Reconnaissance_Attack_Surface_Map.pdf`
-   `Local_Reconnaissance_Attack_Surface_Map.docx`
-   `Local_Reconnaissance_Attack_Surface_Map.md`

Update these filenames if you choose different names when uploading your
report.

## Conclusion

The reconnaissance pass identified the local application entry point,
key frontend assets, basic response headers, a robots.txt route hint,
and an API documentation interface. The next step is controlled
validation of the listed hypotheses, documenting evidence and impact
without treating unverified observations as confirmed vulnerabilities.
