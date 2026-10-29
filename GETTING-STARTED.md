# Getting started — setup status

This folder started from a generic spec template (first adapted to another product) and was re-targeted to RMS on 2026-10-28. This file tracks what is done and what is still open.

## Done

- Names, folder paths and the RMS stack are filled in across the folder.
- Platform notes use the fixed RMS sub-sections: RMS API, Data — Tenant DB, Data — Central DB, Integrations, Admin dashboard (see [`conventions.md`](./conventions.md#platform-notes)).
- Test cases use **Company (tenant) isolation** and the **SuperAdmin boundary** as access boundaries. The real response shapes of the API (401 bodies, tenant-middleware errors, `Status=false`) are recorded in [`project-overview.md`](./project-overview.md#api-conventions).
- Feature folders have a `ui-ux.md` for the admin-dashboard design, following `RMS_API/specs/UI_UX_STANDARDS.md`.
- `RMS_API/CLAUDE.md` and `RMS_NextJS/CLAUDE.md` point here for specs and test cases. Plans, tasks and test results stay in `RMS_API/specs/<feature>/`.
- First worked example: [`features/setting-location/`](./features/setting-location/) (feature code `LOC`), migrated from `RMS_API/specs/setting-location/spec.md` (kept there as `spec.legacy.md`).

## Still open

1. **Fill in `project-overview.md`** through a `spec-intake` interview: product description, user roles and what each may do, supported languages, the rest of the glossary, out of scope.
2. **Decide what `api/openapi.yaml` is** — hand-maintained, exported from the API's Swagger (`/swagger`), or dropped.
3. **Git.** This folder is not a git repository. The "change with the spec" rules work without git, but history and review are easier with one.
4. **Migrate other modules** when they are next touched. `RMS_API/specs/global-page-header/` is a cross-cutting UI component spec and stays in `RMS_API/specs/` for now.

Find the remaining TODO callouts with:

```sh
grep -rn "TODO" . --include="*.md" --include="*.html" --exclude-dir=template
```

## Writing a feature

```sh
cp -R features/template features/<feature-name>
```

Then in the new folder:

- Rename `01-use-case.md` to `01-<actual-slug>.md`, and the test-case files to match
- Replace `{{FEATURE_NAME}}` and `{{FEATURE_CODE}}` wherever they appear
- Fill in the TODO callouts
- Read `conventions.md` once before drafting

The worked example is [`features/setting-location/`](./features/setting-location/).

## Skills

Run Claude Code inside this folder so the skills under `.claude/skills/` auto-discover. Start with `spec-intake`, then `feature-spec-from-figma`. Use `grill-me` to pressure-test a plan.
