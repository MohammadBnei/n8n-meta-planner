# Functional Design Document

## n8n‑Based Project‑Planning Orchestrator ("Meta‑Planner")

**Version:** 1.0 (MVP)  
**Date:** 2026‑02‑11  
**Author:** Alex, Senior Functional‑Expert Consultant

---

## 1. Overview

**Purpose:**  
Automate the creation of comprehensive, detailed, code‑free project‑plan documents via a human‑guided, multi‑expert workflow in n8n. The orchestrator coordinates virtual specialists, maintains synergy through an architect, and outputs a structured GitHub repository containing the complete project blueprint—including an infographic summary.

**Key Flow:**

1. User provides an app idea via chat.
2. Orchestrator clarifies and confirms the idea.
3. Architect proposes a document template (validated by user).
4. Each expert (Backend, Frontend, Data, UI/UX, DevOps) collaborates with the architect to produce a domain‑specific proposal.
5. Architect synthesizes proposals, highlights trade‑offs, and presents concise recommendations for user validation.
6. Steps 4‑5 iterate until the entire plan is approved.
7. Writer agent formats the final plan and pushes it to a GitHub repo.

## 2. Scope

| In‑Scope | Out‑of‑Scope |
|----------|--------------|
| n8n workflow with dedicated nodes/agents for Orchestrator, Architect, Backend, Frontend, Data, UI/UX, DevOps, Writer. | Code generation, scaffolding, or implementation of the planned application. |
| Pure planning artifacts: markdown documents, architecture diagrams, tech‑stack recommendations, infographic summary. | Automated deployment of the planned application. |
| Human‑in‑the‑loop validation at each major gate (template, expert proposals, final plan). | Real‑time collaboration between multiple human users. |
| GitHub repository creation and structured documentation output. | Integration with project‑management tools (Jira, Trello) - possible future phase. |
| Integration with an infographic‑creation tool via natural‑language description. | Machine‑learning optimization of planning decisions. |

## 3. Stakeholders

| Role | Responsibility | Interaction Point |
|------|---------------|-------------------|
| **User/Triggerer** | Provides the initial idea, validates all outputs, final escalation path. | Chat interface, validation prompts. |
| **Orchestrator Agent** | Initiates workflow, rephrases idea for clarity, confirms with user, routes data between agents. | Start of flow, after each user input. |
| **Architect Agent** | Designs document template, coordinates experts, mediates disagreements, synthesizes proposals, generates infographic. | After Orchestrator, before/after each expert, before final user validation. |
| **Expert Agents** (Backend, Frontend, Data, UI/UX, DevOps) | Provide domain‑specific recommendations, collaborate with architect, revise based on feedback. | After template validation, during iteration loops. |
| **Writer Agent** | Formats the final validated plan into markdown, creates repo structure, commits to GitHub. | After final user validation. |
| **Infographic Tool** | Receives natural‑language description from architect, returns an image file (PNG/SVG). | After architect synthesis step. |

## 4. Functional Requirements

| ID | Description | Priority |
|----|-------------|----------|
| **FR‑1** | The system shall accept a raw application idea via a chat‑based trigger (e.g., n8n manual trigger with form, Telegram, or webhook). | High |
| **FR‑2** | The Orchestrator shall rephrase the input idea and ask the user for confirmation before proceeding. | High |
| **FR‑3** | The Architect shall propose a multi‑file document template (directory structure + section outlines) based on the confirmed idea. | High |
| **FR‑4** | The user must explicitly validate or edit the proposed template before experts are engaged. | High |
| **FR‑5** | For each expert role, the Architect shall initiate a sub‑workflow that includes: a) sharing context, b) asking clarifying questions, c) challenging assumptions, d) reconciling dependencies with other experts. | High |
| **FR‑6** | Each expert shall produce a domain‑specific proposal (markdown snippet) that addresses: tech‑stack recommendations, key components, open decisions, and cross‑role dependencies. | High |
| **FR‑7** | The Architect shall synthesize all expert proposals into a consolidated view, highlight trade‑offs, and generate a natural‑language description for the infographic tool. | High |
| **FR‑8** | The system shall present the synthesized plan (including infographic) to the user for validation; if rejected, route specific feedback back to the concerned expert(s) and iterate. | High |
| **FR‑9** | After final user approval, the Writer shall create a GitHub repository with a `/docs` folder, commit all planning documents, and attach the infographic. | High |
| **FR‑10** | All validation steps shall include a clear "Approve/Edit/Reject" interface for the user. | Medium |

## 5. Non‑functional Requirements

- **Performance:** The automated portion of the workflow (excluding human validation time) shall complete within 1 hour for a medium‑complexity idea.
- **Usability:** Validation prompts shall be concise, jargon‑free, and include concrete examples where helpful.
- **Maintainability:** The n8n workflow shall be modular, allowing easy addition, removal, or substitution of expert nodes.
- **Reliability:** The workflow shall preserve state between iterations; no data loss if the workflow is paused or restarted.
- **Integration:** The infographic tool shall be invoked via API call; fallback to a textual summary if the service is unavailable.

## 6. Use‑Cases / User Stories

- **UC‑1 - Dream‑Analysis App Plan**  
  *As a User, I want to input "dream analysis app with LLM and saving features," so that I receive a complete project‑plan repository after 2‑3 validation rounds, including an infographic of the architecture.*

- **UC‑2 - Proposal Revision**  
  *As a User, I reject the backend proposal because it recommends a costly stack, so that the Architect re‑engages the Backend Expert with my cost concern and returns a revised option.*

- **UC‑3 - Template Customization**  
  *As a User, I edit the proposed template to add a "Security Considerations" section, so that all subsequent expert proposals include that dimension.*

## 7. Data Model (Key Entities)

- **ProjectBrief:** `{ id, rawIdea, clarifiedIdea, userValidationFlag }`
- **TemplateProposal:** `{ id, projectId, directoryStructure, sectionOutlines, userApprovedFlag }`
- **ExpertProposal:** `{ id, projectId, role, contentMarkdown, dependencies[], openIssues[], version }`
- **SynthesizedPlan:** `{ id, projectId, consolidatedMarkdown, tradeoffsSummary, infographicDescription, userApprovedFlag }`
- **GitHubRepo:** `{ repoUrl, commitHash, docPaths[] }`

## 8. UI/UX Sketches (Text Description)

- **Trigger Interface:** n8n manual‑trigger form with a single long‑text field "Describe your application idea" and a "Start Planning" button.
- **Validation Prompts:** Each prompt appears as a clear message showing the artifact (template, proposal, infographic) with three buttons: **Approve**, **Edit** (opens a text box for corrections), **Reject** (with optional reason).
- **Output:** GitHub repository with the following minimal structure:

  ```
  ├── README.md               # Project overview, infographic, quick‑start
  ├── docs/
  │   ├── 01‑architecture.md
  │   ├── 02‑backend‑plan.md
  │   ├── 03‑frontend‑plan.md
  │   ├── 04‑data‑plan.md
  │   ├── 05‑ui‑ux‑plan.md
  │   └── 06‑devops‑plan.md
  └── assets/
      └── project‑overview.png   # Infographic
  ```

## 9. Integration & APIs

| Integration | Purpose | Notes |
|-------------|---------|-------|
| **GitHub API** | Create repository, commit files, manage branches. | Use PAT (Personal Access Token) for authentication. |
| **Infographic‑Tool API** | Generate PNG/SVG from natural‑language description. | Assume a POST endpoint that returns an image URL. |
| **n8n Internal** | JSON‑based data passing between nodes. | Use n8n's "Set" nodes to manage state. |
| **Chat‑Platform Webhook** (Optional) | Deliver validation prompts via Telegram/Slack. | Could use n8n's native chat nodes. |

## 10. Acceptance Criteria (MVP)

1. Given a user provides the "dream‑analysis app" idea, the workflow produces a GitHub repo with all six planning documents and an infographic within 3 validation cycles.
2. Each expert's proposal explicitly references at least one dependency on another expert's domain (e.g., "Data layer requires backend API endpoints for LLM calls").
3. When the user edits the template, the updated structure is propagated to all subsequent expert requests.
4. If the infographic tool fails, the workflow falls back to a bullet‑point summary and continues without blocking.
5. The final repo is commit‑ready and can be cloned immediately for team onboarding.

## 11. Open Issues & Assumptions

**Assumptions:**

- User has a GitHub account and is willing to provide a PAT for repo creation.
- Infographic‑tool API is available, accepts a natural‑language description, and returns an image within 30 seconds.
- Experts are implemented as separate n8n workflows or AI‑prompt nodes with clear domain boundaries.

**Open Issues:**

1. How should the Architect handle a deadlock between experts? *(Resolution: Architect escalates to the user with a concise summary of the conflict and recommended options.)*
2. Should past validated plans be stored for reuse or template learning? *(TBD - out of scope for MVP.)*
3. What is the maximum number of iteration loops before a timeout? *(Proposal: 5 cycles, then alert the user.)*
