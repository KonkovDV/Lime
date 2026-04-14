# Lime

Compatibility-first standalone AI chat backend blueprint and public repository seed.

> Status: published docs-first GitHub seed. This repository contains the architecture blueprint, MicroPhoenix extraction matrix, and community-health surfaces for Lime. It does not yet claim a running backend implementation.

## What This Folder Is

This directory is the self-contained public seed for the standalone GitHub repository named `Lime`.

It exists so Lime can evolve independently of the full `c:\plans` working tree.

The folder deliberately contains only:

1. repository identity and community-health files;
2. the canonical architecture blueprint for Lime;
3. the canonical MicroPhoenix extraction boundary for Lime;
4. GitHub issue and PR surfaces.

It deliberately does **not** pretend that the implementation already exists.

## Key Documents

1. [Lime Standalone AI Chat Backend Design](docs/LIME_STANDALONE_AI_CHAT_BACKEND_DESIGN.md)
2. [Lime MicroPhoenix Extraction Matrix](docs/LIME_MICROPHOENIX_EXTRACTION_MATRIX.md)

## Architecture Horizons

The blueprint now carries a verified H0-H5 horizon model grounded in the live MicroPhoenix codebase.

1. `H0` — OpenAI-compatible chat backend MVP
2. `H1` — multi-provider + PostgreSQL + Redis + JWT
3. `H2` — event maturity + embeddings + retrieval
4. `H3` — agents + tools + MCP + firewall activation
5. `H4` — memory planes + context engine + graph-backed retrieval
6. `H5` — near-full MicroPhoenix platform convergence

The verified live-code census used for that model is:

1. `67` domain subdirectories
2. `77` infrastructure subdirectories
3. `35` application subdirectories
4. `29` core subdirectories
5. `~291K` source LOC

The full horizon map and seam-budget rules live in the design document.

## Claim Boundary

Implemented in this folder:

1. repository-ready documentation package;
2. public-facing project identity;
3. extraction boundary and MVP rules;
4. GitHub contribution, CODEOWNERS, and issue surfaces.

Not implemented in this folder:

1. runtime code;
2. HTTP API;
3. persistence adapters;
4. Docker packaging;
5. CI, CodeQL, or Dependabot automation.

That omission is intentional. Until the code scaffold exists, adding fake CI or dependency automation would create false trust signals.

## GitHub Status

This seed is already published at:

1. `https://github.com/KonkovDV/Lime`

Current public-repo state:

1. community-health files are present;
2. issue and PR surfaces are present;
3. `CODEOWNERS` is present;
4. the definitive H0-H5 architecture map is documented;
5. runtime code is intentionally absent.

Before raising the repo to Standard-tier OSS automation, add the real runtime scaffold first, then wire truthful CI, dependency automation, and CodeQL around actual code.

## Recommended Next Step

The next honest engineering step is to scaffold the real repository structure described in the blueprint:

1. `src/`
2. `tests/`
3. `package.json`
4. `tsconfig.json`
5. Docker and workflow surfaces

## License

MIT. See [LICENSE](LICENSE).
