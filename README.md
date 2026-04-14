# Lime

Compatibility-first standalone AI chat backend blueprint and repository seed.

> Status: docs-first GitHub-ready seed. This folder contains the architecture blueprint, MicroPhoenix extraction matrix, and community-health surfaces for the future Lime repository. It does not yet claim a running backend implementation.

## What This Folder Is

This directory is a self-contained seed for a future standalone GitHub repository named `lime`.

It exists so Lime can be published independently of the full `c:\plans` working tree.

The folder deliberately contains only:

1. repository identity and community-health files;
2. the canonical architecture blueprint for Lime;
3. the canonical MicroPhoenix extraction boundary for Lime;
4. GitHub issue and PR surfaces.

It deliberately does **not** pretend that the implementation already exists.

## Key Documents

1. [Lime Standalone AI Chat Backend Design](docs/LIME_STANDALONE_AI_CHAT_BACKEND_DESIGN.md)
2. [Lime MicroPhoenix Extraction Matrix](docs/LIME_MICROPHOENIX_EXTRACTION_MATRIX.md)

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

## How To Publish This Folder As Its Own GitHub Repository

1. Create an empty GitHub repository named `lime`.
2. Copy the contents of this folder so that `README.md` from this folder becomes the repository root `README.md`.
3. Push the folder contents as the initial commit to `https://github.com/KonkovDV/Lime`.
4. After repository creation, enable:
   - dependency graph
   - secret scanning
   - push protection
   - private vulnerability reporting
   - branch protection or rulesets for `main`
5. Only after the runtime scaffold exists, add CI, Dependabot, CodeQL, and release automation.

This seed already includes `CODEOWNERS` and issue/PR surfaces so the future repository starts with basic governance instead of retrofitting it later.

## Recommended Next Step

The next honest engineering step is to scaffold the real repository structure described in the blueprint:

1. `src/`
2. `tests/`
3. `package.json`
4. `tsconfig.json`
5. Docker and workflow surfaces

## License

MIT. See [LICENSE](LICENSE).
