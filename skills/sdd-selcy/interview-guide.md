# Interview guide

Read this in full before you ask the first question. It holds the stages, the panel mechanics, and the completeness gate. Do not open the interview, generate a question, or write a file until you have read it.

## Panel mechanics

Every question is a decision panel, never a neutral menu:

- 2 to 4 concrete options, real to this project, each with a one line consequence.
- Exactly one marked `(recommended)`, with a one line why. You are making the call and letting the engineer override, not offloading it.
- Never add your own `Other` option. If the agent has a picker it appends `Other` automatically; if not, say in plain text that a free text answer is welcome.
- Batch related questions up to 4 per panel. Run as many panels as a stage needs. Fold prior answers forward so the interview reads as one continuous conversation.
- Infer and skip anything already answered, already in the repo, or already in state. Re asking is a bug.

State is saved after every panel, so a lost chat resumes cleanly.

## Stage 0: Framing (3 to 5 panels)

The product and the working terms. Cheap to ask, and everything downstream reads them.

- **Product name and one line description.** What it is and who it is for, in one sentence each.
- **Doc language.** Indonesian or English for the generated context files and feature specs. Code, file paths, commands, and identifiers stay English either way. (Recommended: match how the engineer talks in this chat.)
- **MVP boundary.** The smallest version that delivers the core value. The single most important question in the interview; every later cut is judged against it. Offer 2 to 3 concrete shapes of the MVP, not an open field.
- **Primary audience.** Only if the one line description did not already settle it.
- **Monetization.** Free / subscription / one time / usage based / ads / none yet. Shapes whether billing is a foundation or a deferred feature.
- **Success metric.** Signups, activation, revenue, retention. Informs whether analytics is a foundation.
- **Hard constraints.** Deadline, budget, team size, compliance scope (PCI, GDPR, HIPAA, region specific). Any of these can reorder the whole plan.

## Stage 1: Capabilities (1 to 2 panels, multi select)

The cross cutting capabilities this product plausibly needs. Present as a multi select, never name the implementing tool here (that is Stage 3):

authentication, multi tenant orgs, payments or billing, email or notifications, file or media upload, search, realtime collaboration, admin panel, public API, AI features, offline support, internationalization.

For each selected, confirm: **in this first build, or deferred?** Each in scope selection becomes one or more features. Each deferred one is recorded in `project-overview.md` under Out Of Scope, so the plan stays honest about what was considered and cut.

## Stage 2: Cross cutting and go to market (1 to 2 panels)

Routinely forgotten, and expensive to retrofit. Ask even for internal tools, where the answer is often "not applicable":

- **SEO.** Public or marketing pages, metadata, sitemap, structured data, social cards, SSR or SSG needs. Skip for auth walled internal apps, and say why you skipped.
- **Performance.** Core Web Vitals targets, caching expectations, expected load and concurrency.
- **Analytics and error monitoring.** Product analytics, error tracking, conversion events.
- **Accessibility.** WCAG target level, keyboard navigation, screen reader support.
- **Legal and compliance.** Cookie consent, privacy policy and terms, GDPR or CCPA scope, age gating.

Each `yes` becomes its own feature or folds into a relevant feature's acceptance criteria.

## Stage 3: Stack (2 to 4 panels)

This is where `/architect` ARCHITECTURE mode lands. One panel per dependency cluster, each option carrying a recommendation and a one line why. Recommend on top of what the repo and Stage 0 already show; never recommend a tool that fights an existing constraint.

Cluster order (each batched panel covers one):

1. **Language and runtime, framework, rendering strategy.** Monolith versus split front and back end; server components versus SPA; SSR or SSG needs from Stage 2.
2. **Database, ORM or query layer, and local dev database.** Relational versus document; migration strategy; how the engineer runs it locally (Docker, installer, managed, sqlite for dev).
3. **Auth and session.** Self hosted library versus managed provider; where session and user data live; MFA and social needs.
4. **Storage, hosting, background jobs, observability, testing.** Blob or file storage; hosting target; long running work (queue, durable tasks, or none yet); error tracking; unit and e2e test runners.

For each tool, also settle the **money and precision rule** if the product touches money, and the **timezone rule** if it touches reporting, shifts, or daily buckets. These are the two decisions most often silently invented during a build, and they are cheap to settle here.

Every Stage 3 answer is written to state and becomes the `architecture-context.md` stack table. Nothing here is deferred to feature specs.

## Stage 3b: Architecture shape (1 panel)

The system shape, decided once so no feature spec reinvents it.

- **Architecture style.** Modular monolith, monorepo, or microservices. Present each with its real cost, recommend exactly one. (Recommended: modular monolith for a small team. One deploy, one codebase, module boundaries enforced by convention and lint, not by network. Microservices buy independent deploy at the cost of distributed system failure modes a small team cannot absorb.)
- **Module boundaries.** The modules the product will have, and what each owns. A feature touches one module.
- **Split triggers.** The conditions that would justify extracting a module or service. Written down so the decision is not relitigated per feature. Default: split only on measured evidence (a team boundary, a scaling bottleneck, an independent deploy need), never on anticipated scale.
- **Dependency direction.** What may depend on what. No cycles, no feature importing another feature's internals.

## Stage 3c: Security (1 panel)

Non negotiable rules, written once so every feature inherits them instead of deriving its own.

- **Auth and session.** How identity is established and where session lives.
- **Authorization model.** Who may read and mutate what. The rule every mutation boundary enforces.
- **Input validation.** Where and how external input is validated (at the boundary, before any logic runs).
- **Secrets.** How secrets are stored and read, and what must never be logged.
- **Compliance scope.** GDPR, CCPA, UU PDP, PCI, HIPAA, or none. Named explicitly, because it changes what a feature must do.
- **Data protection.** What is PII in this product, how it is handled, and what must not be stored.

If the engineer has no opinion, recommend the strictest default that does not slow the first build, and say it is a default.

## Stage 3d: Error handling and observability (1 panel)

The contract for failure. Decided once, so a builder never invents an error shape mid feature.

- **Error response shape.** One shape for every API response, success and failure.
- **Error categories.** Validation, not found, unauthorized, conflict, rate limited, internal. Which are retryable.
- **Logging.** What is logged, at what level, and what must never appear in a log line (secrets, tokens, PII).
- **Observability.** Error tracking, metrics, tracing. Which from day one, which deferred.
- **User facing errors.** What the user sees. Plain language, no stack traces, no internal names.

## Stage 3e: Testing (1 panel)

What must be tested, where, and from which slice onward.

- **Runners.** Unit, integration, e2e. Which tools.
- **Test pyramid.** What each level covers, and from which feature onward it is required.
- **What is tested first.** Money, auth, permissions, and any invariant in `architecture-context.md`. These ship with tests in the same feature, never deferred.
- **Definition of done.** A feature is not done because it works. It is done because it is verified against its spec.

## Stage 4: UI direction (1 to 2 panels)

Only if the product has a UI. Needed because `ui-context.md` is a foundation file every feature spec references.

- **Theme mode.** Dark only, light only, or both. (Recommended: pick one only for the first build; theming both is a feature of its own.)
- **Color direction.** A named direction plus the accent color or colors. If the engineer has brand colors, take the hex values now.
- **Typography.** Font family or families (UI and mono), and how they load.
- **Component foundation.** A component library (shadcn/ui, or other) versus hand rolled. Radius scale and spacing scale.
- **Design references.** Any app, screenshot, or repo the engineer wants the UI to feel like. Optional but high value; it removes a whole class of later questions.

If the engineer has no opinion, recommend a complete, coherent default and say it is a default they can replace. A decided default beats an unresolved one.

## Stage 5: Code standards (1 panel)

What `/audit` would ask, compressed. These become `code-standards.md`:

- **Type strictness.** Strict mode, `any` policy, validation at boundaries.
- **File organization.** The folder contract (where lib, components, API or routes, data layers live).
- **Testing strategy.** Which runners, from which slice onward, what must be tested first (anything doing money, auth, or permissions).
- **Git workflow.** Branch per feature or trunk, commit cadence, whether the AI may commit.

## Stage 5b: Repository (1 panel)

- **GitHub repository URL.** The engineer supplies it (for example `https://github.com/maulanaadib/ghost-ai.git`). Never invent an owner or repo name. If the repo does not exist yet, tell the engineer to create an empty one on GitHub first, without a README or a license, then continue.
- **Branch strategy.** Commit straight to `main`, or work on a branch (`develop`, or `feature/<name>`) and keep `main` deployable. (Recommended: `main` for a solo build, a branch when the engineer wants review.) This sets the default the `## Commit and Push` section of every feature spec asks about; the engineer can still pick differently per feature.
- **Commit cadence.** One commit per feature spec (recommended), or per milestone inside it.

Recorded in state and echoed into `ai-workflow-rules.md`, because that is the file later builders read.

## Stage 5c: Deployment and CI (1 panel, conditional)

How the project ships, and the one infra decision that cannot be deferred past feature 00. Conditional on the Stage 5b repository answer.

**The local only exception.** If Stage 5b recorded no repository URL, the project is local only. Say so plainly, then ask only the env var convention question below. Mark first deploy, trigger, and CI checks not applicable with the reason "no remote repository". Never push anything, and never ask about a deploy target for a project with no repo to deploy from.

If there is a repository URL, ask all four:

- **First deploy.** Does `00-project-setup` also perform the first deploy (link the project on the host, set every env var, trigger the build, verify the URL responds)? Or is deploy handled later, when there is something to show? (Recommended: do it in `00-project-setup`. At that point the app is a placeholder, the env var list is three or four entries, and a failed deploy breaks no business logic. The cost of deferring rises every feature: by feature 05 the app has a data model, auth, and a dozen env vars the engineer must recall from memory, and the deploy gets debugged inside a feature that never planned for it.)
- **Deploy trigger.** Auto deploy on push to `main`, manual deploy from the host only, or a preview deployment per pull request with `main` deploying on merge. (Recommended: auto on `main` for a solo trunk based build, which is the branch strategy Stage 5b already recommended; preview per PR only when the engineer works on branches and wants a review step before merge. The trigger must agree with the Stage 5b branch strategy, it never overrides it.)
- **CI checks.** What must pass before a change lands: lint and typecheck only, or lint and typecheck plus the test suite. (Recommended: match the Stage 6 workflow tier. Prototype and Alpha, lint and typecheck, because the loop is still fast and wrong is cheap. Beta and GA, add the test suite, because at that tier a regression reaching `main` costs more than the CI minutes. Note the interaction with the trigger: only the preview per PR trigger actually gates a merge. Auto deploy on `main` runs the checks after the code is already live, so at Beta and GA the two choices pull toward each other, and the real gate lives in each feature spec's `## Commit and Push` section instead.)
- **Env var convention.** The dev env file name (`.env.local` for Next.js, `.env` for most others), the full list of env vars the Stage 3 stack requires, and where production values live. (Recommended: `.env.local` for dev, gitignored, production values set on the hosting dashboard and never in the repo.) This list is written to `architecture-context.md` as the env var table, so every later feature inherits the same variable names instead of inventing its own and shipping an `undefined` to production.

Recorded in state. Echoed into `architecture-context.md` as the env var table, and into `00-project-setup.md` as the first deploy steps.

## Stage 6: Build approach and workflow tier (1 panel)

- **Build approach.** Present the four by their guiding principle, recommend exactly one:
  - **Tracer Bullet**: vertical slices, each feature end to end through every layer, working. (Recommended for production builds.)
  - **Skateboard**: thinnest usable whole first, then grow. For fast validation of one core loop.
  - **Facade**: clickable UI shell on placeholder data, then wire the back. Prototype grade, say so plainly.
  - **Journey**: one complete user path per phase. When the experience is the product.
- **Workflow tier.** Prototype, Alpha, Beta, or GA. How much process each feature warrants, and what closes `done` at that tier. (Recommended: Beta for a real product, Alpha for a throwaway validation.)

Both are recorded on the state header and echoed into `ai-workflow-rules.md`, because that is the file later builders read.

## Stage 7: Feature decomposition (1 to 2 panels, then one confirmation panel)

You reason here, you do not ask. From every answer so far, produce the ordered feature list:

- **`00-project-setup` is always first.** Scaffold the project from the Stage 3 stack decision and clean the framework boilerplate. No product behavior lives in it. It exists because feature 01 otherwise starts by fighting a template.
- **Then foundations.** Tooling and standards, data model, design system, walking skeleton. Never buried as sub tasks of a feature.
- **Then slices**, ordered by the Stage 6 approach. Keep features small: one page or one cohesive unit each. A listing, a detail page, and a cart are three features, not one storefront.
- **Then explicitly deferred** nice to haves, each with a one line why it is out.
- Per feature: a 1 to 2 line intent, and a `Done when` line of observable outcomes (acceptance criteria seeds, the WHAT, never the HOW).

Then the **confirmation panel**: show the whole plan back (stack summary, approach, tier, the numbered feature list with intents, the deferred list) and ask one question, approve or change. Until this is approved, nothing is generated and any earlier answer can still be revisited.

## Completeness gate (before generating, do not skip)

Do not write a single file until every dimension above is accounted for. Each is:

- **answered** by the engineer, or
- **inferred**, with the inference stated back in the confirmation panel so the engineer can object, or
- **not applicable**, with the reason recorded in state.

Any dimension still open → keep asking. An unasked dimension becomes a silent decision during a build, which is exactly the failure this skill exists to prevent. Depth is the goal: when in doubt, ask the extra question. Never fold two distinct dimensions into one option, never end with a dimension silently skipped.

A short interview is a red flag. A real project spans every stage above.
