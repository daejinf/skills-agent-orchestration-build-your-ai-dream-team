# Project Pulse implementation plan

## Summary

Build Mona's Project Pulse as a small, dependency-free static dashboard for
contributors. The first view should make active projects easy to scan by
showing each project's name, owner, status, recent activity, priority or risk,
and a short contributor-friendly summary. The page must be semantic and
accessible, the cards must be driven by `app/project-data.json`, and the
preview must open the dashboard itself rather than a server directory listing.

The Orchestrator coordinates the work, the Planner maintains this plan, the
Designer defines the information hierarchy and visual/accessibility decisions,
and the Coder implements the connected static files and deterministic VS Code
launch configuration. No external framework or package is required.

## File assignments

| File | Owner | Assignment and completion requirements |
| --- | --- | --- |
| `docs/project-pulse-plan.md` | Planner, reviewed by Orchestrator | Record the goal, phases, ownership, dependencies, parallel/sequential decisions, risks, validation, and handoff criteria. Keep the plan aligned with the repository brief and custom agent definitions. |
| `app/index.html` | Coder, using Designer's decisions | Create the semantic dashboard shell with the exact page title `Project Pulse`; link `styles.css`; load or reference `project-data.json`; render multiple visible project cards using the `project-card` class; expose each project's `name`, `owner`, `status`, `recentActivity`, and `priority` in readable, accessible markup. Use meaningful landmarks, headings, labels, and status text rather than relying on color alone. |
| `app/styles.css` | Designer, implemented by Coder | Define the polished responsive visual system and include deterministic `.dashboard` and `.project-card` selectors. Provide readable typography and spacing, clear status/priority treatment, rounded cards (`border-radius`), depth (`box-shadow`), adequate contrast, keyboard-visible focus states, and layouts that remain usable on narrow screens. |
| `app/project-data.json` | Coder, informed by Designer's content needs | Provide valid JSON with a top-level `projects` array. Every project object must contain non-empty `name`, `owner`, `status`, `recentActivity`, and `priority` fields, with enough varied records to demonstrate multiple cards, statuses, and priority levels. Keep values contributor-friendly and safe to display as text. |
| `.vscode/launch.json` | Coder | Create strict JSON with no comments. Add a configuration named `Run Project Pulse Dashboard`, run `python3 -m http.server 5500`, set `cwd` to `${workspaceFolder}/app`, and configure `serverReadyAction` to open `http://localhost:%s/index.html`. The launch target must be `index.html`, not the directory root. |

## Responsibilities

### Planner

- Research the Project Pulse brief and the existing agent definitions before
  implementation.
- Maintain the ordered phases, ownership boundaries, dependencies, risks, and
  measurable validation expectations in this document.
- Keep the plan practical for a static app and avoid introducing unnecessary
  tooling or dependencies.

### Designer

- Define a clear information hierarchy: dashboard title and context first,
  followed by a scannable responsive grid of project cards.
- Specify accessible semantic structure, readable labels, status and priority
  affordances, contrast, focus treatment, and behavior at narrow widths.
- Ensure status badges and priority indicators communicate meaning with text,
  not color alone, and that spacing and typography support quick scanning.
- Provide the visual direction for `.dashboard`, `.project-card`, card
  surfaces, status/priority variants, and responsive breakpoints without
  changing Coder-owned markup or data files.

### Coder

- Implement the assigned HTML, CSS, JSON, and launch files while preserving
  the file boundaries above.
- Connect `index.html` to both `styles.css` and `project-data.json`; use a
  deterministic rendering approach that surfaces load or parse errors rather
  than silently producing a misleading empty dashboard.
- Keep the data schema consistent across JSON and rendered fields, escape or
  safely insert data as text, and provide a useful empty-state or error-state
  treatment if the data cannot be loaded.
- Produce strict, parseable launch JSON with the required working directory,
  server command, named configuration, and direct `index.html` browser URL.
- Run the static and runtime checks below and report any limitation to the
  Orchestrator.

## Dependencies and execution phases

1. **Research and plan — sequential first.** The Planner reads the brief,
   `docs/agent-team.md`, and all relevant definitions in `.github/agents/`,
   then finalizes this plan. The Orchestrator reviews the assignments before
   implementation starts.
2. **Design and content direction — can run in parallel.** Designer can define
   the visual system and accessibility requirements while Coder drafts the
   project records in `app/project-data.json`, because they own separate
   files. The Designer should agree on the fields and status/priority vocabulary
   before Coder finalizes the rendering details.
3. **Data and markup integration — sequential after the direction.** Coder
   creates or confirms the JSON schema before wiring `index.html`, so the
   renderer's required field names and empty/error behavior match the data.
   Designer reviews the resulting structure for hierarchy and accessibility
   before styling is considered complete.
4. **Styling and launch configuration — partly parallel.** Coder can implement
   `app/styles.css` from Designer's direction while independently creating
   `.vscode/launch.json`; neither file changes the JSON schema. Final runtime
   validation must wait until all four app/launch files exist.
5. **Integration validation and handoff — sequential last.** The Orchestrator
   reviews the complete file set, checks the launch behavior, confirms that
   the plan was followed, and records any remaining risk before handoff.

Work must not be parallelized when it edits the same file, when markup depends
on an undecided data schema, or when runtime validation depends on the final
launch configuration. Designer may advise Coder throughout, but Coder remains
the implementation owner and should not overwrite design-only decisions
without recording the change.

## Dependencies

- **Runtime:** Python 3, for `python3 -m http.server 5500`; a browser capable of
  loading local static assets and JSON.
- **Editor:** VS Code Run and Debug support for `.vscode/launch.json`.
- **Application:** No npm packages, build step, framework, API, or network
  service. `index.html`, `styles.css`, and `project-data.json` must work when
  served from the `app` directory.
- **Repository context:** `.github/project-pulse-brief.md`,
  `.github/agents/planner.agent.md`, `.github/agents/designer.agent.md`,
  `.github/agents/coder.agent.md`, and
  `.github/agents/orchestrator.agent.md`.

## Validation expectations

### Static validation

- Confirm all assigned files exist and no unrelated files were changed.
- Parse `app/project-data.json` with a JSON parser. Assert that `projects` is
  an array and every project has `name`, `owner`, `status`, `recentActivity`,
  and `priority`.
- Parse `.vscode/launch.json` with a JSON parser; reject comments or trailing
  syntax that makes it non-strict JSON. Confirm the configuration name is
  exactly `Run Project Pulse Dashboard`, the command is
  `python3 -m http.server 5500`, `cwd` is exactly
  `${workspaceFolder}/app`, and `serverReadyAction` opens
  `http://localhost:%s/index.html`.
- Inspect `app/index.html` for the exact `Project Pulse` title, a link to
  `styles.css`, a reference to `project-data.json`, semantic landmarks and
  headings, the `project-card` hook, and visible rendering of all required
  fields.
- Inspect `app/styles.css` for `.dashboard`, `.project-card`,
  `border-radius`, `box-shadow`, responsive rules, readable contrast, and
  focus styling. Check that card layout does not require horizontal scrolling
  at a narrow viewport.
- Check that the HTML, CSS, and JSON field names agree and that the page has
  no hard-coded project list that bypasses the JSON source.

### Runtime validation

1. Launch **Run Project Pulse Dashboard** from VS Code, or run the equivalent
   server from `app/` with `python3 -m http.server 5500`.
2. Request `http://localhost:5500/index.html` and confirm the response is the
   dashboard document, not a directory listing; confirm the stylesheet and
   JSON return successfully.
3. Open the page in a browser and confirm the Project Pulse heading and several
   populated project cards appear from the JSON data. Verify owner, status,
   recent activity, and priority are visibly associated with each card.
4. Exercise a narrow viewport and a wider viewport. Confirm the cards reflow,
   text remains readable, controls/focus indicators remain visible, and no
   essential content is clipped.
5. Temporarily test a missing or malformed data response where practical and
   confirm the UI presents an explicit, understandable error or empty state
   instead of silently claiming there are no projects. Stop the preview
   server after the check.

## Edge cases and risks

- **JSON fetch restrictions:** Opening `index.html` directly from `file://`
  can block `fetch`; use the launch server and document that the dashboard is
  served over HTTP.
- **Malformed or incomplete records:** Missing fields can create blank labels
  or broken cards. Validate the schema and render an explicit fallback/error
  state rather than silently dropping records.
- **Untrusted text values:** Insert project data as text, not executable HTML,
  so a future project name or activity string cannot inject markup.
- **Empty projects array:** Provide a clear empty-state message while keeping
  the dashboard shell usable.
- **Long content:** Long names and recent activity must wrap without causing
  horizontal overflow; priority and status labels must remain legible.
- **Visual-only status:** Color-only status or risk cues are inaccessible.
  Retain visible text labels and sufficient contrast for every state.
- **Launch mismatch:** A wrong `cwd`, omitted `index.html`, or root URL opens a
  directory listing. Validate the exact path and URL, not merely that a
  server starts.
- **Concurrent edits:** Designer and Coder must not edit the same file in
  parallel. The Orchestrator resolves any ownership conflict before merging
  changes.

## Handoff criteria

The Orchestrator may hand off Project Pulse only when:

- The five assigned paths exist with only the intended plan change in this
  planning phase and the implementation files in the build phase.
- `app/index.html` is semantic, accessible, data-connected, and visibly
  renders multiple `.project-card` elements with all required project fields.
- `app/styles.css` contains the required `.dashboard` and `.project-card`
  hooks plus polished, responsive card styling, rounded corners, shadows,
  contrast, and focus treatment.
- `app/project-data.json` is valid and every project record has the complete
  required schema.
- `.vscode/launch.json` is strict JSON and its named configuration serves from
  `${workspaceFolder}/app` using port 5500 and opens
  `http://localhost:%s/index.html`.
- Static parsing and structural checks pass, and a live preview demonstrates
  the dashboard rather than a directory listing at desktop and narrow widths.
- Designer's accessibility and responsive decisions, Coder's implementation
  details, and any known limitations are reported clearly by the Orchestrator.
- No changes are staged, committed, or pushed as part of this plan; git
  operations remain under the learner's control.


