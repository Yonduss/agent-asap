# Agent ASAP project plan

**Date:** 2026-11-23  
**Status:** Planning  
**Audience:** Junior developers and solo builders starting a small project

## Purpose

Agent ASAP turns a short brief into a project the developer can run, read, and take over. It makes the early decisions that block a blank project, writes a concise plan, and prepares the code needed for one complete user flow. The developer decides what to add next.

Progress means reducing the time from an idea to a working project and making the first manual change easy. Measure simplicity by what a developer can understand and change. Record elapsed time and model usage separately so a cost or speed claim can be checked.

## First release

Start with a local command line tool written in Python. Accept a short brief or a choice of a supported starter, an output folder, and any explicit stack constraints. The supported starter for the first demo is a small web app.

| Deliverable | Contents |
| --- | --- |
| Project brief | One page with the goal, first user flow, stack, and features left for later. |
| Frontend | Plain HTML and CSS by default, with JavaScript only where the flow requires it. |
| Backend | One small Python app with the routes and validation needed for the first flow. Prefer a simple server rendered page when it keeps the flow easier to follow. |
| Data | Add persistence when required. Use SQLite for the first local example. Document the fields and relationships that the flow uses. Add an ERD when relationships need a diagram. |
| README | How to run the project, how to verify the flow, and which file to edit first. |

**First demo.** Generate a local task list where a developer can add a task and see it in the list after refreshing. Use one page, a form, and one SQLite table. Reject empty task names. Verify that a submitted task appears after reload, then point the developer to the form and the code that stores the task so they can add a completion field themselves.

## Design rules

- Agree on one complete user flow before generating files. Include the layers and dependencies that flow requires. A static site can stop at HTML and CSS.
- Prefer a clear file layout and direct code. Add a framework when it replaces meaningful complexity or the user requires it. Preserve the validation and error handling that the flow relies on.
- Use local templates for routine file generation. If a model helps interpret the brief, have it return a small scaffold specification and let Python render it. Start the integration with one provider and report model calls, tokens, and estimated cost when available.
- Write into a new or empty output folder. If a stack or requirement falls outside the supported starter, explain the limitation before generating files.
- Stop after the run instructions and the first flow have been checked. Leave one concrete next change in the README. More starters and provider choices can follow once the first workflow has been evaluated.

## Milestones

| Step | Work | Complete when |
| --- | --- | --- |
| 1. Define the output | Specify the task list flow, file layout, stack, and what the developer will change next. Record the checks before building the generator. | The expected files and the behavior of the first flow are clear. |
| 2. Build one local starter | Implement a small Python CLI and a template for the brief, HTML/CSS, Python backend, SQLite storage, and README. Generate the project from an explicit starter choice. | The CLI writes a runnable starter to an empty folder. A smoke check confirms a valid task persists and an empty task is rejected. |
| 3. Try model assisted planning | Translate a short natural language brief into supported starter settings. Compare one model call with asking the user to pick settings directly. | The same input can be scaffolded through either path. Record the time and model usage for each, including unsupported requests. |
| 4. Evaluate the handoff | Compare ASAP, a plain coding agent, and Ponytail on the same small-project briefs. Use the same base model where possible and record any differences. Ask a developer to run the output and make one small change without AI help. | A short results table shows whether the flow works, time to run, dependencies, model use, and whether the developer could explain and edit the code. |

The first two milestones can use the installed Python 3.14.6. The project virtual environment currently runs Python 3.9.6. Python 3.9 reached end of life on October 31, 2025, so use a fresh Python 3.14 environment before implementation. Choose compatibility requirements for generated projects separately when there is a reason to do so. [Python version status](https://devguide.python.org/versions/)

## References

- Ponytail shows how an agent can avoid unnecessary code and retain checks that matter. Its own benchmark provides a comparison point. ASAP applies this principle specifically to getting a new project to its first working state and handing it back to the developer. [Ponytail](https://github.com/DietrichGebert/ponytail)
- Karpathy describes his responsive personal site as two static HTML and CSS files. The practical lesson for ASAP is to start with what the requested behavior needs and make each additional layer earn its place. [Andrej Karpathy’s personal site](https://karpathy.ai/)
