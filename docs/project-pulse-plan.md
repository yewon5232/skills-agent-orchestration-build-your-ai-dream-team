# Project Pulse implementation plan

## Summary

Build a small, polished static Project Pulse dashboard for contributors. The first view should make active projects, owners, current status, recent activity, priority/risk, and a short contributor-friendly summary easy to scan. The implementation is intentionally framework-free and should use the existing repository layout:

- `app/index.html` — semantic dashboard structure and the project content.
- `app/styles.css` — responsive visual system and interaction states.
- `app/project-data.json` — deterministic project data with a top-level `projects` array.
- `.vscode/launch.json` — a reproducible Run Project Pulse Dashboard configuration that serves `app/` and opens `index.html`.

The Orchestrator coordinates the work and integration but does not implement application code. No agent stages, commits, or pushes changes; the learner controls git operations through Copilot CLI.

## Ordered implementation steps and assignments

### 1. Establish the contract and implementation boundaries

**Owner:** Orchestrator, informed by Planner  
**Files:** No file changes required.

Confirm the brief and preserve the existing static-app scope. The dashboard must support the following information:

- Which projects are active.
- Project owner.
- Current project status.
- Recent activity.
- Priority or risk level.
- A concise contributor-facing summary.

Keep the implementation deterministic and lightweight. Avoid adding a framework, package manager, build step, or dependency when plain HTML, CSS, JSON, and the existing VS Code workflow are sufficient.

### 2. Define the visual and interaction direction

**Owner:** Designer  
**Assigned file:** `app/styles.css`

Design the page as a clear dashboard rather than a bare list:

- Establish a strong page header identifying Project Pulse and its purpose.
- Use a responsive project-card grid for the primary content.
- Give each card clear hierarchy: project name, summary, owner, status, recent activity, and priority/risk.
- Use recognizable status and priority treatments with text labels, not color alone.
- Include the required deterministic hooks `.dashboard` and `.project-card`.
- Use readable typography, sufficient whitespace, rounded corners, restrained shadows, and clear contrast.
- Define styles for focus-visible controls, links, badges, and any empty or unavailable state used by the markup.
- Make the layout usable on narrow screens without horizontal scrolling; allow cards to collapse to a single column and preserve readable line lengths.
- Respect accessibility preferences such as reduced motion where transitions are introduced.

The Designer should keep all styling in `app/styles.css`, explain notable design tradeoffs in the handoff, and avoid changing markup or data files.

### 3. Create the project data contract and content

**Owner:** Coder  
**Assigned file:** `app/project-data.json`

Create valid JSON with a top-level `projects` array. Each project object must include:

- `name`
- `owner`
- `status`
- `recentActivity`
- `priority`

Use several realistic, contributor-friendly sample projects so the dashboard demonstrates active work and meaningful variation in status and priority/risk. Keep values concise enough for cards, use consistent status and priority vocabulary, and ensure every object has all required fields. If the UI includes a summary field, either provide it consistently in the data or make the summary a clearly defined part of the content contract rather than mixing shapes.

The data must be deterministic, human-readable, and free of secrets, external API requirements, or generated timestamps. The Coder should validate that it parses as JSON before handing it off.

### 4. Implement the dashboard document

**Owner:** Coder  
**Assigned file:** `app/index.html`

Build a complete static page that:

- Declares the document language, character encoding, viewport, and a useful title.
- Loads `styles.css` with a relative URL.
- Presents a semantic header and main dashboard region.
- Uses headings in a logical order and provides an accessible name for the project collection.
- Renders visible project cards with the required content: project name, owner, status, recent activity, priority/risk, and a short summary.
- Uses text labels and meaningful HTML elements instead of relying only on visual color or iconography.
- Applies `.dashboard` to the main project collection and `.project-card` to each card so the Designer’s styles have stable hooks.
- Provides useful `alt` text for any non-decorative image, or avoids unnecessary images/icons.
- Avoids inline styles and unnecessary JavaScript or external runtime dependencies.

The simplest implementation may render the deterministic sample data directly into the page. If the document loads `project-data.json` dynamically, the implementation must include an explicit, user-visible error state for a failed load and must still be testable through the configured local server. The chosen approach must not make the dashboard depend on a network service.

### 5. Add the runnable VS Code configuration

**Owner:** Coder  
**Assigned file:** `.vscode/launch.json`

Create strict JSON for a launch configuration named **Run Project Pulse Dashboard**. It must:

- Serve the `app/` directory as the server working directory.
- Open `index.html` when launched, rather than showing a directory listing.
- Use a deterministic local command, port, and URL consistent with tools available in the repository/environment.
- Set `cwd` to `${workspaceFolder}/app` as required by the Coder agent contract.
- Avoid comments, trailing commas, secret values, and machine-specific absolute paths.

The launch setup should make the dashboard straightforward to run and preview for a learner using VS Code.

### 6. Integrate and review the complete surface

**Owner:** Orchestrator with Designer and Coder handoff  
**Files:** All four assigned files, reviewed together.

Review the implementation as one browser surface rather than validating files in isolation. Confirm that:

- The HTML references the stylesheet and, if applicable, the JSON file with correct relative paths.
- Classes used by the HTML match the CSS hooks.
- Data fields and displayed labels agree.
- The launch configuration serves the same `app/index.html` that was implemented.
- The page’s first viewport clearly communicates Project Pulse and exposes useful project information.
- No agent has modified files outside its assignment.

If integration reveals an overlap, the Orchestrator should return the issue to the owning specialist instead of making an unassigned cross-file edit. Any sequential correction must preserve the file ownership boundaries.

## Dependencies and handoffs

| Work item | Depends on | Handoff |
| --- | --- | --- |
| Scope and contract confirmation | Repository brief and existing agent conventions | Orchestrator gives each specialist an explicit file scope. |
| Designer styling direction | Scope confirmation; stable markup hooks are agreed | Designer supplies the CSS contract, including `.dashboard`, `.project-card`, state classes, responsive behavior, and accessibility states. |
| Project data | Scope confirmation | Coder supplies valid, consistently shaped sample data for the page. |
| HTML implementation | Scope confirmation; data field names and CSS hooks | Coder aligns semantic markup with the data contract and Designer’s hooks. |
| Launch configuration | App location and entry point are known | Coder configures the server from `app/` and opens `index.html`. |
| Integrated review | HTML, CSS, data, and launch files exist | Orchestrator checks cross-file paths, names, behavior, and ownership. |

The main technical dependency is the shared contract between the HTML and the data. The Designer’s CSS can be developed independently against the agreed semantic class names, but final styling review depends on the actual HTML. The launch configuration depends on the final app entry point and should be validated after the app files are present.

## Parallel and sequential work decisions

### Work that can run in parallel

After the Orchestrator confirms the shared contract, these tasks can proceed in parallel because their file scopes do not overlap:

- Designer creates `app/styles.css`.
- Coder creates `app/project-data.json`.
- Coder drafts `app/index.html`, provided the agreed field names and CSS hooks are used.

The data and stylesheet tasks are independent of one another. The Coder may also prepare `.vscode/launch.json` in parallel with styling because it is configuration-only, as long as the entry path and required `cwd` are already agreed.

### Work that must run sequentially

The following steps require ordering:

1. Confirm the shared contract before specialist implementation.
2. Agree on HTML class hooks and data field names before parallel work begins.
3. Complete the HTML/data integration before final visual review; any runtime data-loading choice must be known before launch validation.
4. Validate `.vscode/launch.json` only after the app entry point and serving assumptions are fixed.
5. Run the integrated browser/HTTP checks after all four files are present.
6. Apply fixes through the owning agent, then repeat the relevant integration checks.

Do not have Designer and Coder edit the same file concurrently. If design feedback requires markup changes, pause styling review, assign the markup change to Coder, and then have Designer re-check the resulting surface.

## Responsibilities by role

### Orchestrator

- Translate the request into explicit phases and file assignments.
- Pass the Planner’s contract and dependencies to Designer and Coder.
- Keep agents within their assigned files.
- Coordinate parallel work only where there are no overlapping writes or unresolved data dependencies.
- Resolve integration issues by delegating them to the correct owner.
- Perform the final cross-file review and report remaining risks.

### Planner

- Research the repository brief, existing conventions, agent capabilities, and runnable-app requirements.
- Identify the file ownership model, data contract, dependencies, parallel work, sequential work, edge cases, and validation expectations.
- Do not write implementation files.

### Designer

- Own the information hierarchy, visual language, responsive layout, accessibility states, and CSS implementation in `app/styles.css`.
- Ensure project cards, status badges, priority treatment, spacing, typography, contrast, and focus behavior are polished and readable.
- Use the agreed stable selectors and avoid changing Coder-owned files.

### Coder

- Own the semantic page in `app/index.html`, deterministic content in `app/project-data.json`, and runnable configuration in `.vscode/launch.json`.
- Keep the implementation explicit, framework-free, and aligned with the Designer’s CSS contract.
- Use valid JSON and strict launch-configuration JSON.
- Surface errors clearly if runtime data loading is used.
- Verify paths, serving behavior, and the configured entry point.

## Edge cases and risks

- **Missing or malformed data:** Every project object must contain all required fields; malformed JSON or mismatched field names must be caught before integration.
- **Status/priority ambiguity:** Status and priority/risk must be conveyed by text as well as color so the meaning survives grayscale viewing and supports color-vision differences.
- **Long content:** Long project names, owner names, activity text, and summaries must wrap without breaking the card grid or causing horizontal scrolling.
- **Small screens:** The layout must remain usable on narrow viewports, with cards stacking and controls retaining adequate tap/focus space.
- **Keyboard and assistive technology:** Use semantic headings and landmarks, visible focus indicators, logical reading order, and no interaction that is mouse-only.
- **Reduced motion:** Do not make essential information depend on animation; suppress nonessential motion for users who request reduced motion.
- **Asset and path portability:** Use relative app paths and avoid assumptions about a particular machine, repository clone location, or network availability.
- **Directory-listing launch failure:** The launch configuration must explicitly open `index.html`; serving only the directory is not sufficient.
- **Runtime loading failure:** If JSON is fetched at runtime, local serving is required and the page must expose a meaningful failure message rather than silently rendering an empty dashboard.
- **Scope drift:** Do not add unrelated tooling, dependencies, generated files, or edits outside `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` for the implementation.

## Validation expectations

### Static validation

- Parse `app/project-data.json` with a JSON parser.
- Parse `.vscode/launch.json` with a JSON parser; confirm it is strict JSON with no comments or trailing commas.
- Confirm the launch configuration is named **Run Project Pulse Dashboard**, uses `cwd: "${workspaceFolder}/app"`, serves the app directory, and opens `index.html`.
- Check that `index.html` references existing local assets and that every required data field is represented in the visible UI.
- Check that all CSS selectors used as integration hooks, especially `.dashboard` and `.project-card`, exist and are applied.

### Browser and responsive validation

- Start the configured local server and open the configured URL; confirm the response is the dashboard document, not a directory listing.
- Confirm the first view has a clear Project Pulse heading and visible project cards.
- Confirm cards show project name, owner, status, recent activity, priority/risk, and summary.
- Resize or inspect the page at narrow and wide viewport sizes; confirm cards reflow, text remains readable, and no horizontal scrolling is introduced.
- Navigate with the keyboard and inspect focus visibility and reading order.
- Check sufficient contrast for body text, badges, card surfaces, and focus indicators.
- If data is loaded dynamically, test both a successful load and a failed load so the failure state is explicit and not silent.

### Integration and handoff validation

- Review the changed-file list to ensure only the four assigned implementation files are involved in the app build, with this plan document being the only planning artifact.
- Confirm no git staging, commit, or push is performed by any agent.
- Re-run the smallest relevant checks after any correction and report unresolved environment-specific limitations plainly.

## Open questions and assumptions

- The repository does not prescribe a framework, package manager, design system, or test runner, so the plan assumes a dependency-free static implementation.
- The brief requires the project fields listed above but does not mandate a separate `summary` field. The implementation should either include a consistent summary field for better contributor context or keep summaries as clearly defined static content; it must not use inconsistent object shapes.
- The exact local serving command may depend on the available VS Code server/debug extension. The Coder should use the repository/environment’s supported deterministic option while preserving the required `cwd` and `index.html` entry behavior.
- No backend, persistence, authentication, external API, or live activity feed is in scope. Sample data should communicate the intended UI without introducing network or secret-management requirements.
