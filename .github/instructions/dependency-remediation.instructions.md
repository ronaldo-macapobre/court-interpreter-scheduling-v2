## Dependency Remediation Strategy

Before starting, fetch the latest changes from origin/main.

If the fetch fails, stop immediately and report the error.

Always prefer updating declared dependencies over pinning transitive dependencies.

For each vulnerability:

1. Determine whether the vulnerable package is declared in the project manifest.

2. If the vulnerable package is a direct dependency:
   - Update it directly in the manifest to the lowest non-breaking fixed version.

3. If the vulnerable package is a transitive dependency:
   - Do not add it as a new direct dependency.
   - Identify the nearest declared top-level dependency that introduces it.
   - Upgrade that declared dependency to the lowest non-breaking version that resolves the vulnerability.
   - Use `overrides` / `resolutions` only as a documented fallback.

4. Use `overrides` / `resolutions` only when:
   - no safe non-breaking parent upgrade exists,
   - the parent package is abandoned,
   - or a parent upgrade would require breaking changes.

## Lockfile

- After applying all planned dependency updates, regenerate the lockfile once.
- Ensure the lockfile is consistent with the updated manifest.
- Do not regenerate the lockfile after each individual dependency update.

## Validation

- Do not run lint, tests, build, or other CI validations locally unless explicitly requested.
- Assume the pull request CI pipeline is the source of truth for validation.
- Only perform local dependency installation if required to regenerate the lockfile or verify dependency resolution.

## Guardrails

- Never add a vulnerable transitive package as a new direct dependency solely to remediate it.
- Never edit application source files unless explicitly required.
- Keep the final diff limited to dependency manifests and lockfiles.
- If a remediation requires source code changes or a breaking dependency upgrade, classify it as requiring manual review instead of implementing it.

## Remediation Summary

At the end of the remediation, provide a summary containing:

- Vulnerabilities targeted.
- Direct dependencies updated.
- Parent dependencies updated to remediate transitive vulnerabilities.
- `overrides` / `resolutions` added or modified, with justification.
- Packages that could not be remediated automatically and the reason.
- Files changed.
- Any assumptions or manual follow-up required.
