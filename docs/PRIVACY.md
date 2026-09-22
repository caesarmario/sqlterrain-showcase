# Privacy

## Local-first by architecture

Core SQL reasoning runs in the browser. SQL text is not sent to a first-party or third-party analysis API. Survey executes analytical queries and profiling locally through DuckDB-WASM, with SQLite files decoded locally through `sql.js` before entering the same analytical path.

This boundary reduces the amount of sensitive query text and imported data that must leave the user's machine. It does not mean the application makes no network requests.

## What stays in the browser

The following product work is designed to happen in the browser process:

- editing SQL;
- parsing and scope-aware analysis;
- graph, lineage, grain, join, and review model construction;
- SQL dialect conversion;
- Draft document operations and SQL generation;
- Survey file ingestion, querying, and profiling;
- generation of local downloads such as diagrams and reports.

SQLTerrain does not require production database credentials for its core SQL tools. Survey works with files or database extracts the user intentionally opens in the browser; it is not a hosted database connection service.

## Network behavior

Local-first and offline-only are different claims. SQLTerrain can make network requests for:

- application HTML, JavaScript, fonts, icons, illustrations, and sample assets;
- Pyodide and runtime packages needed to run SQLGlot;
- hosting, CDN, and operational delivery services;
- explicitly opened remote resources, subject to browser and CORS policy;
- optional product analytics when the deployment has configured an endpoint.

These requests deliver code or product assets. They are not an API to which SQL is submitted for analysis. A cold runtime may need the network before the local engine becomes ready, while a warm browser cache may reduce that dependency.

Hosting and CDN providers may maintain ordinary request logs under their own operational policies. Those logs are different from SQLTerrain's application-level analysis path.

## Analytics boundary

The application analytics module is disabled unless the deployment explicitly provides an analytics endpoint. When enabled, it is designed to:

- honor the browser's Do Not Track preference;
- send short event names and a small number of bounded, enum-like properties;
- reject property keys associated with SQL, queries, credentials, contact details, users, addresses, and other sensitive fields;
- reject empty or oversized string values;
- fail without interrupting the product workflow.

SQL and imported data are outside the analytics contract. The presence of hosting analytics or an operational CDN beacon should not be described as “no network,” but it also should not be misrepresented as uploading SQL to an analysis service.

## Local persistence

SQLTerrain may retain limited browser-local state so the product can behave predictably between pages or sessions. Categories can include:

- selected theme and locale;
- editor or cross-tool handoff state;
- workspace layout preferences;
- dismissed help or onboarding state;
- recoverable product state needed for a user-requested workflow.

This public documentation intentionally does not expose internal storage-key names. Local persistence is not cloud synchronization, an account system, or a remotely stored project history.

Browser storage can be cleared through the browser's site-data controls. Clearing it may remove preferences and recoverable local work.

## Survey data handling

Files opened in Survey are read by browser-local code. DuckDB-WASM performs analytical execution in the browser. SQLite content is decoded locally and imported into the analytical runtime without first being uploaded to an SQLTerrain profiling service.

The user still controls what they open. A browser tab has finite memory, and imported data may be visible to browser extensions, operating-system tooling, or anyone with access to the device. Local processing reduces server exposure; it does not replace endpoint security.

Remote URL imports, when available, are ordinary network fetches initiated for a resource the user selected. They remain subject to the remote host, browser CORS rules, and network intermediaries.

## Downloads and clipboard

SQLTerrain can create downloadable SQL, Markdown, text, or image artifacts and can read from or write to the clipboard after a user action. Those operations are browser-mediated. Once an artifact is downloaded or copied, its handling is controlled by the user, operating system, destination application, and any later sharing choice.

Reports can include the SQL being analyzed because that is their purpose. Users should inspect generated files before publishing them if the query contains sensitive identifiers, comments, or business logic.

## What SQLTerrain does not provide

SQLTerrain is not a secure vault, access-control layer, data-loss-prevention system, or compliance certification. It does not encrypt arbitrary local files for storage, manage database credentials, or govern where users later paste or upload generated artifacts.

The privacy model is narrower and testable:

> Core SQL reasoning and Survey data processing run locally in the browser; SQL and imported rows are not sent to an SQLTerrain analysis backend.

That statement deliberately leaves room for application delivery, runtime packages, hosting infrastructure, user-requested remote resources, and bounded analytics metadata.
