# Trade-offs & Boundaries

SQLTerrain deliberately refuses to turn uncertainty into fake certainty. That choice shapes its architecture, its interface, and the claims this showcase makes.

## 1. Static reasoning versus production facts

SQL text can reveal structure:

- referenced sources and scopes;
- join operators and predicates;
- filters and aggregations;
- window definitions;
- output expressions;
- set-operation branches;
- possible grain transitions.

It cannot prove facts that exist only in schemas, data, or an execution environment:

- whether a column is unique in production;
- whether a relationship is one-to-one, one-to-many, or many-to-many for actual rows;
- how many nulls or duplicates exist;
- whether a filter implements the intended business rule;
- how a database optimizer will execute the query;
- how much the query will cost.

Atlas therefore separates SQL-proven behavior from schema-dependent assumptions and data-dependent unknowns. Survey exists to measure some of the facts that static reasoning cannot establish. Even Survey can only speak for the data and sampling scope it was given.

The alternative would be a friendlier-looking interface that invents keys or cardinality from names such as `customer_id`. That would be easier to demonstrate and less safe to trust.

## 2. Local-first versus a centralized backend

Running the core engines in the browser provides meaningful benefits:

- SQL and imported data do not need to be posted to an analysis API;
- no production database credentials are required for the core tools;
- the product has a smaller backend attack and operations surface;
- deterministic engine versions can be tested as part of the client;
- users can move between reasoning, conversion, profiling, and composition without creating an account.

The costs are real:

- WebAssembly and Pyodide have startup overhead;
- browser memory and CPU are finite;
- cold runtime initialization may require network access;
- browser compatibility and CDN policy can affect readiness;
- very large files can exceed practical local limits;
- there is no server-side production catalog or execution history by default.

SQLTerrain does not describe these costs as implementation details the user should ignore. They are consequences of the privacy and deployment model.

## 3. Conversion versus execution verification

Dialect conversion is not guaranteed lossless.

SQL dialects differ in syntax, type systems, null ordering, date arithmetic, identifier rules, JSON behavior, procedural extensions, and feature availability. A target query can parse and still behave differently.

Passage therefore distinguishes several outcomes:

- **checked:** the conversion and current audit found no material issue in the evidence available;
- **review required:** the output exists, but a construct or rewrite needs human verification;
- **blocked:** a faithful target cannot be produced under the current contract;
- **known gap:** the limitation is understood and documented rather than silently rewritten.

The product provides conversion evidence and provenance, not a promise that source and target databases will return identical rows for every input dataset. Important migrations still require target-engine execution, representative data, and domain review.

## 4. Deep browser analysis versus fast startup

SQLGlot provides the dialect-aware syntax tree needed for scope-aware analysis and conversion. Running it through Pyodide keeps the processing local but makes startup heavier than a small JavaScript parser.

SQLTerrain prefers the deeper path and can use a limited structural fallback when the runtime is unavailable. This improves failure tolerance but introduces a product obligation: the fallback must be labelled truthfully, preserve input, and never masquerade as the deeper result.

The alternative choices both have drawbacks:

- waiting forever for the deep runtime makes the product brittle;
- silently returning a weaker result makes the product misleading.

The current design chooses availability with explicit capability and provenance boundaries.

## 5. Rich semantic views versus model consistency

Atlas presents maps, lineage, joins, review findings, execution stages, and exports. Multiple views make complex SQL easier to inspect, but each additional surface can drift if it derives facts independently.

The architectural preference is one accepted semantic snapshot feeding every view. This sometimes means withholding a convenient explanation when the shared model lacks evidence. A visually complete graph built from guessed edges would be worse than an explicitly unresolved path.

Layout quality and semantic correctness are separate. Moving React Flow nodes cannot repair an invalid dependency model. Visual polish is applied after the nodes, edges, scopes, and source mappings are correct.

## 6. Browser-local profiling versus warehouse-scale execution

DuckDB-WASM gives Survey a capable analytical engine without a server. It is well suited to local files, extracts, and bounded exploratory profiling.

It is not a replacement for a warehouse or lakehouse execution environment. Practical limits include:

- available browser memory;
- file size and format support;
- time needed to parse or materialize inputs;
- remote CORS restrictions;
- the absence of production catalog statistics and permissions;
- browser-tab lifecycle.

Survey reports the scope of its calculations and sampling. For large or regulated datasets, a warehouse-side profiling workflow may remain the appropriate choice.

## 7. Guided building versus complete SQL representation

Draft prioritizes safe, understandable structure. Semantic blocks teach how a query is assembled and produce visible SQL.

SQL is too broad to promise that every statement, extension, procedural construct, or dialect-specific expression can be represented as a puzzle workspace. Draft therefore supports a bounded structured subset and preserves unsupported SQL as text.

This produces a less magical product, but it avoids destructive synchronization. A system that forces unfamiliar SQL into partial blocks can silently drop meaning while appearing successful.

## 8. Deterministic core versus an LLM assistant

No LLM is part of SQLTerrain's core analysis, conversion, lineage, warning, profiling, or builder path.

Benefits:

- results are repeatable for a pinned engine and input;
- outputs can be graded against exact oracles;
- provenance and failure modes are bounded;
- a model cannot invent schema facts or rewrite user SQL unpredictably;
- SQL is not sent to a language-model service.

The trade-off is less open-ended assistance. SQLTerrain does not accept a vague natural-language request and improvise an architecture or business rule. It focuses on explaining and transforming artifacts the user can inspect.

This is a product decision, not a claim that language models are never useful in data work.

## 9. Experimental dialects

PostgreSQL, MySQL, SQLite, and SawitDB have supported product paths under their respective contracts. BigQuery and Snowflake are exposed as experimental because their parse and conversion paths are smoke-tested but not corpus-deep to the same degree as the primary mainstream dialects.

SawitDB is not included in the 20-route Passage translation scorecard. It uses a separate normalization and dual-syntax contract, so importing the mainstream-dialect percentages would be misleading.

Support labels are intended to follow tested capability metadata. Marketing copy should not upgrade a dialect because a single example happened to work.

## 10. Public showcase versus public source

This repository explains the product, architecture, validation discipline, privacy boundary, and decisions behind SQLTerrain. It does not distribute the application source.

That limits what an external reviewer can reproduce directly from this repository. The compensating choice is to publish concrete architecture boundaries, scoped evidence, known gaps, screenshots, and a live product rather than pretending a documentation repository is an open-source package.

No install instructions, contribution guide, source license, or public developer API are included because they would imply a distribution model that does not exist.

## 11. What should be verified outside SQLTerrain

Before a material production decision, use evidence that matches the risk:

- run the target query on the actual database;
- inspect the database execution plan;
- test conversion against representative source and target datasets;
- confirm keys and constraints from authoritative schema metadata;
- measure nulls, duplicates, and relationship cardinality on governed data;
- review business semantics with the owning domain team;
- apply the organization's security and change-management process.

SQLTerrain is useful when it makes those next steps more precise. It is not useful if its diagrams are treated as a substitute for them.
