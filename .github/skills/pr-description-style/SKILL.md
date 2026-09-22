---
name: pr-description-style
description: Draft or revise pull request descriptions in Bestra's voice, always applying the ASD-STE100 and unslop skills before returning prose. Use when asked to write, rewrite, improve, or review a PR description, PR body, or gh pr create body.
---

# PR Description Style

Write for an engineer who understands programming but does not know this feature, its internal terminology, or the investigation that led to this PR.

Always apply the `asd-ste100-skill` skill to PR descriptions. Use its reference and examples for clarity, not compression. Keep sentences direct and preserve all technical constraints. Then apply the `unslop` skill to remove filler, not to add personality or flourish. Finish with the required reader check below. These audience and wording rules take precedence over sentence length targets and instructions to "add soul."

Keep the repo's PR template intact, but remove AI tells, puffery, unnecessary lists, needless bold labels, em dashes, and filler. Return the PR description, not a report of these editing passes.

## Workflow

1. Read the target repo's PR template and local instructions first if they exist.
2. Identify the real reason the PR exists: bug, issue, incident, rollout need, followup, or workflow problem.
3. Put the most important links near the top when they help orient the reviewer: issue, predecessor PR, sibling PR, Sentry, demo, or docs. When Sentry is relevant, link directly to the issue instead of mentioning only its slug.
4. Map the writeup onto the repo's actual section headings instead of replacing them with your own preferred structure.
5. Make the description self-contained so a reviewer reading only the PR body can understand what changed, why now, what is out of scope, and any important caveats.
6. Run the result through `asd-ste100-skill` to remove ambiguity without compressing explanations into jargon. Preserve domain terms, facts, conditions, scope qualifiers, and required template text.
7. Run the result through `unslop` to remove filler without adding flourish or deleting required template sections or HTML comments.
8. Apply the reader and wording check below after both skills. Rewrite unexplained internal labels and compressed phrases before returning the description.

## Reader and wording

- Explain what the user does, what goes wrong, and what changes before describing implementation details. For changes without a user interaction, explain the system's behavior instead.
- Name concrete actions and data. Do not invent labels for combinations of conditions or behavior.
- Do not compress explanations into hyphenated phrases. Rewrite the underlying idea; merely removing the hyphens does not fix the problem.
- Introduce internal terms only when they help explain the change. Explain their meaning before using them as shorthand.
- Preserve exact identifiers, model names, quotations, and template text. Keep established technical terms when they are precise, but explain any meaning specific to this feature.
- Prefer a longer clear explanation over a shorter phrase that requires feature knowledge. Preserve all material conditions and uncertainty.

| Avoid | Prefer |
|---|---|
| "no-send return path" | "switching back without sending a message" |
| "latest committed assistant model" | "the model that produced the last completed response" |
| "external-context guard" | "the check that prevents switching models when the conversation includes tool results" |

Required final check: inspect every hyphenated phrase and internal label after all other editing passes. Rewrite anything a reviewer would need the author to explain. Check that the description explains the behavior before naming its implementation. Do not shorten the result if that would restore the ambiguity or remove a condition.

## Core style

- Lead with intent and context, not a generic summary.
- Explain the current behavior or problem in concrete terms.
- Say why this PR exists now.
- Use actual code paths, handlers, flags, components, or symptoms when that helps reviewers orient quickly.
- Prefer permalinks to exact code locations when pointing reviewers at specific code, instead of naming a method, variable, or file without a link.
- Keep scope boundaries explicit, especially when obvious adjacent work is intentionally not included.
- Use bullets only when they genuinely clarify the change. Do not default to a bland summary list.
- Mention testing, rollout, or feature flags only when they add reviewer value. Routine lint, typecheck, and test commands are almost always noise.
- Omit low-signal validation sections that only restate commands, passing test counts, or files a reviewer can already see in the diff.
- Be direct, matter-of-fact, and comfortable naming uncertainty or correction plainly.
- Keep filler low. Avoid vague AI-ish words like "streamline", "enhance", "robust", or "seamless" unless they point to something specific.
- Leave reviewer-owned template checkboxes unchecked.

## Cardinal rules

- **Describe the final state, not the dev journey.** The reviewer is reading the final diff. Do not narrate how the branch got there: no "originally tried X, then switched to Y", no "fixup after review", no "addressed feedback in commit abc123", and no changelog of iterations.
- **Do not restate the diff.** File-by-file or function-by-function recaps are noise. Describe the change at the level of what it does and why, one level of abstraction above the code.
- **Do include non-obvious implementation nuance.** Anything a careful reader of the diff would still wonder about belongs in the body: surprising constraints, performance considerations, ordering or locking concerns, compatibility or security trade-offs, deliberate non-goals, known follow-ups, and edge cases the code handles silently.
- **Do not include process status as prose.** Skip standalone `make lint` / `make test` / `npm run tsc` / `npm run lint` paragraphs, CI status, force-push notes, "switched approach from X to Y", and self-review findings already fixed. The reviewer can see CI in GitHub and reads the final diff.
- **Use the PR template when one exists.** Fill every section or ask for the missing information.
- **Re-ground in the diff before rewriting an existing PR.** Title and body drift is common after a few iterations. Do not update from memory alone.

## What belongs in the body

| Include | Exclude |
|---|---|
| What the PR does, in one or two sentences at the top | A blow-by-blow of every changed file |
| Why this change is needed, with the motivating issue, bug, incident, or constraint linked when useful | Narration of the development process |
| Non-obvious design decisions and the rationale for the chosen approach | Restating what the diff already shows clearly |
| Trade-offs accepted, alternatives considered and rejected, and why | "Originally I tried X, then switched to Y" |
| Surprising constraints the code responds to, such as performance, ordering, compatibility, or security | Self-review findings already fixed |
| Deliberate non-goals, limitations, and follow-ups | CI status, routine lint output, or force-push notes |
| Edge cases handled silently in the code | Apologies, hedges, filler, or a summary of the commit log |

## Validation and verification sections

When a PR template asks "How did/will you validate this change?", answer with reviewer-useful signal, not a command log.

- Prefer naming the validation strategy over listing individual test files or exact commands. "Added regression coverage for the quota fallback path" is enough when the diff and CI show the files and commands.
- Do not say tests, lint, typecheck, or CI "pass". Passing is expected, and GitHub shows it.
- Do not include routine lint, typecheck, formatting, or package test commands. `npm run tsc`, `npm run lint`, `make lint`, `bin/rake test`, and similar commands are usually slop in the PR body.
- Do not write "reran the tests", "ran the surrounding tests", or "132 runs, 0 failures". That is process status, not context.
- Do mention manual or unusual validation when it explains risk: a codespace-only repro, a local e2e stack, a production-like demo path, a feature-flag rollout check, a migration dry run, a browser/device matrix, or another verification step the reviewer would not infer from the diff.
- Mention added or changed tests only at the behavior level. Say what risk they cover, not the command used to run them.
- Use the template's "Other" verification section only when normal verification was unavailable, insufficient, or novel enough to matter. Running lints or typechecks does not count as "Other".
- If the only validation is ordinary tests and CI, keep the section terse or say nothing beyond what the template requires.

Bad:

```md
Tests - Ran `npm test -- packages/web/components/__tests__/ItemPicker.test.tsx -t "selected items appear at the top when search is cleared"`; ran `npm run lint -- packages/web/components/__tests__/ItemPicker.test.tsx`; ran `npm run typecheck`.
```

Still mostly noise:

```md
Tests - Unit tests in seat_management_test.rb updated to assert new ordering; integration tests in seat_management_controller_test.rb pass (132 runs, 0 failures).
```

Better:

```md
Tests - Added coverage for the seat ordering behavior.
```

Good use of "Other":

```md
Other - Verified the first prompt against a local stack with four services because this path depends on the scheduler, API service, worker service, and web application running together.
```

## Default shape

Use this when the repo does not strongly force another structure:

```md
Fixes / Part of / Followup to <important link>

Short paragraph explaining the current behavior and why it needs to change.

Short paragraph or short list explaining what this PR changes and any important scope limits.

Optional:
- demo link
- screenshot
- exact failing error
- rollout or feature-flag notes
- validation notes that help reviewers
```

## Important habits

- If you know the issue a PR belongs to or fixes, include that link near the top unless there is a concrete reason not to.
- If you mention a Sentry issue, prefer linked text like `[EXAMPLE-123](https://sentry.io/issues/1234567890)` over a bare slug or quoted identifier.
- If you point at a code path that matters to the review, prefer a permalink to the exact file and lines.
- Preserve the repo's real section names instead of swapping in your own.
- If a template section is not relevant, answer tersely instead of padding it with boilerplate.
- If the PR intentionally leaves out an obvious adjacent behavior or still has a notable limitation, say that plainly.
- If the description could plausibly fit almost any PR, rewrite it to be more concrete.

## When editing an existing PR body

- Re-read the actual diff against the PR base before drafting; do not rewrite from memory or from the existing body alone.
- Keep useful links, repo-specific prompts, and operational notes.
- Tighten generic filler before adding more content.
- Preserve truthful scope statements and known caveats.

## Common mistakes

- **Contrasting with an external spec, design doc, or earlier proposal.** Phrases like "the original spec proposed X, but we did Y instead", "the design doc framed this as Z, which is stale", or "first pass tried W, the final code does V" force the reader to chase a counterfactual to understand the actual code. Describe the current code's behavior and rationale as if the alternative never existed. If a spec doc is now wrong, update the doc or surface it separately instead of litigating it in the PR body.
- **Rewriting from memory.** When updating an existing PR, always re-ground in the actual diff against the base ref first. Memory drifts after a few iterations.
- **Padding the body with process status.** CI results, force-push notes, approach changes, and fixed self-review findings do not belong in the body.
- **Recapping the diff in prose.** A bullet list of "changed `foo.rs` to add X, changed `bar.rs` to update Y" is just the diff in worse format. State what the PR does and why, not what files moved.
- **Omitting non-obvious nuance to stay short.** Concise does not mean empty. If a careful reader of the diff would still wonder "why this way?" or "what about case Z?", that is exactly the content the body should carry. Brevity applies to filler, not substance.

## gh CLI note

If `gh pr create` quoting gets awkward, write the body to a temp file and use `GH_PROMPT_DISABLED=1 gh pr create --body-file <file> --base <base> --head <head>`, then clean up the temp file.
