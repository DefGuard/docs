# Product Development Procedure

This document describes the official product development process used in Defguard.

As an open organization and open-source project, we aim to keep our planning, prioritization, and execution transparent and understandable to contributors, customers, and the community.

The procedure defines how ideas become features, how work is planned, and how development progress is tracked using our public GitHub Projects setup.

### Scope and goals

This procedure applies to:

* Product features and improvements
* Bug fixes and stability work
* Security, compliance, and enterprise-readiness tasks
* Infrastructure and release engineering work
* Technical debt and refactoring

The goals are to:

* Maintain predictable and transparent development
* Align short-term execution with long-term product strategy
* Enable community participation and feedback
* Ensure high quality and security standards

### Methodology

Defguard’s product development process is heavily based on **Scrum**.

We use short, time-boxed iterations (sprints), regular planning and refinement meetings, and a continuously prioritized backlog to ensure fast feedback cycles and predictable delivery. The process is adapted to fit an open-source and open-organization model, where planning artifacts (roadmap, backlog, and sprint progress) are publicly visible and community input is actively incorporated into prioritization.

This lightweight, iterative approach allows us to balance long-term product strategy with short-term execution while remaining flexible to security, compliance, and customer-driven requirements.

### Tools used

Defguard uses the following public tools for product development management:

* **GitHub Issues** – feature requests, bugs, tasks
* **GitHub Projects (**[**Backlog & Roadmap**](https://github.com/orgs/DefGuard/projects/5)**)** – planning and execution tracking
* **GitHub Milestones** – release versions

The [GitHub Project](https://github.com/orgs/DefGuard/projects/5) provides three primary views:

* [Roadmap view](https://github.com/orgs/DefGuard/projects/5/views/16) (long-term planning)
* [Sprints view](https://github.com/orgs/DefGuard/projects/5/views/8) (short-term planning)
* [Board view](https://github.com/orgs/DefGuard/projects/5/views/2) (current sprint execution)

### Issue workflow

All work items (features, bugs, improvements, and technical tasks) follow a standardized workflow in GitHub Projects. The workflow is designed to make the current state of each item clear to both the team and the community.

The following statuses are used:

* **New** – The issue has been created and awaits initial triage.
* **Refinement ready** – The item is ready to be discussed and detailed during the refinement session (scope, acceptance criteria, and technical approach may be clarified).
* **Development ready** – The item is fully specified and can be planned into a sprint.
* **In progress** – Active development is ongoing.
* **Review** – Implementation is finished and waiting for code review and/or product review.
* **Testing** – The item is being tested (manually and/or automatically).
* **Ready to release** – Work is completed and the item is ready to be included in the next release.

### Roadmap planning

#### Structure

* The roadmap is managed in the [**Roadmap view**](https://github.com/orgs/DefGuard/projects/5/views/16) of the GitHub Project.
* Each roadmap item is assigned to a **GitHub Milestone** representing a release version (e.g., `v1.6`, `v1.7`).
* Items without a milestone are considered unplanned or under evaluation.

#### Sources of ideas

New ideas and tasks included in the roadmap may originate from:

* Community feature requests and GitHub issues
* Customer and enterprise feedback
* Internal product planning
* Security and compliance requirements
* Technical maintenance needs

#### Refinement process

* Roadmap items are reviewed during a **weekly refinement session** with the team.
* During refinement, the team may:
  * Clarify scope
  * Split or merge tasks
  * Re-prioritize items
  * Move features between milestones (release versions)

#### Community participation

* Community members are encouraged to vote using 👍 reactions on issues.
* Comments and use-case descriptions are reviewed during prioritisation.
* Voting is advisory but strongly influences product direction.

#### Roadmap stability

* The roadmap represents intent, not a guarantee.
* Milestones and priorities may change due to:
  * New information
  * Urgent bugs or security issues
  * Customer or compliance requirements

### Sprint planning

#### Structure

* Development is organised into **1-week sprints**.
* Sprint planning is performed once per week.
* The [**Sprints view**](https://github.com/orgs/DefGuard/projects/5/views/8) shows:
  * The current sprint
  * The next 2–3 planned sprints
* Future sprint content is considered **tentative** and may change before the sprint starts.

#### Planning session

During weekly planning, the team:

* Selects issues from the roadmap/backlog
* Confirms scope and acceptance criteria
* Verifies feasibility within one sprint
* Assigns tasks to the upcoming sprint

Only well-defined and actionable issues are included in a sprint.

### Sprint execution

#### Structure

* The [**Board view**](https://github.com/orgs/DefGuard/projects/5/views/2) represents the execution state of the current sprint.

#### Daily workflow

* Developers update issue status as work progresses.
* The board is used to:
  * Track progress
  * Identify blockers
  * Coordinate parallel work

#### Demo session

* The sprint ends with a demo session, where developers present the results of their work.
* Unfinished issues move to the following sprint or are postponed during a planning session.

### Release management

* A release is prepared when all milestone items reach the **Ready to release** status or are explicitly postponed.
* Releases are versioned according to the milestone name.
* Release notes are generated from completed issues.

