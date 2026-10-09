# Project structure

Speedrun organizes coding agent skills by the development stage they support.
This is a proposed taxonomy for this collection, rather than a universal standard
for AI native software development.

AI native development makes context gathering, explicit specifications, agent
execution, and evaluation part of the workflow. People define intent, resolve
uncertainty, and approve actions that require judgment or authorization. Agents
work within those boundaries and provide evidence for their results.

## Lifecycle categories

| Category | Purpose | Example skills | Expected output |
| --- | --- | --- | --- |
| `01-discovery` | Understand the problem, users, and existing system. | Requirements interviews, repository exploration, source research, feasibility probes. | Problem statement, constraints, evidence, open questions. |
| `02-specification` | Turn intent into a clear, reviewable definition of success. | Product requirements, user stories, acceptance criteria, domain modeling. | Specification with acceptance criteria and explicit exclusions. |
| `03-design` | Decide how the solution should work and fit the system. | Architecture, API contracts, data modeling, UX design, threat modeling. | Design, contracts, tradeoffs, and decision records. |
| `04-planning` | Break the approved scope into executable work. | Task decomposition, dependency mapping, implementation planning, agent delegation. | Bounded tasks with inputs, dependencies, and verification steps. |
| `05-implementation` | Build or change the software within the agreed scope. | Feature development, refactoring, migration implementation, test driven development. | Code changes and supporting tests or fixtures. |
| `06-validation` | Gather evidence that the change meets requirements. | Test execution, code review, security review, accessibility review, agent evaluations. | Results, findings, and unresolved verification gaps. |
| `07-release` | Deliver a verified change to its intended environment. | Pull request preparation, release notes, deployment, migration rollout, rollback planning. | Release artifacts and deployment evidence. |
| `08-operations` | Learn from production behavior and maintain the system. | Observability, incident response, debugging, performance analysis, maintenance. | Diagnosis, recovery evidence, and feedback for the next iteration. |

These stages form a feedback loop. Validation can send work back to design or
implementation; production findings can start another discovery cycle. Testing,
security, and documentation also happen throughout the lifecycle.

## QA, security, and DevOps responsibilities

Lifecycle stages describe when work happens. Disciplines describe the expertise
involved. QA, security, and DevOps contribute across stages; each skill still
has one primary lifecycle category so its instructions are not duplicated.

| Discipline | Responsibilities across the lifecycle | Example skill placement |
| --- | --- | --- |
| Quality assurance (QA) | Review testability and acceptance criteria, plan test coverage, prepare test data and automation, perform exploratory and regression testing, assess release readiness, investigate escaped defects. | `02-specification/acceptance-criteria`, `04-planning/test-strategy`, `05-implementation/test-automation`, `06-validation/exploratory-testing`, `06-validation/regression-testing`. |
| Security | Identify sensitive data and security requirements, model threats, review trust boundaries, guide secure implementation, assess vulnerabilities, review release controls, respond to security incidents. | `02-specification/security-requirements`, `03-design/threat-modeling`, `05-implementation/secure-implementation`, `06-validation/security-review`, `08-operations/security-incident-response`. |
| DevOps and reliability | Define infrastructure and reliability requirements, design environments, automate builds and delivery, manage configuration and secrets, validate deployment changes, deploy and roll back releases, monitor services and recover from failures. | `03-design/infrastructure-design`, `05-implementation/pipeline-implementation`, `06-validation/infrastructure-validation`, `07-release/deployment`, `07-release/rollback`, `08-operations/observability`, `08-operations/incident-response`. |

Include a **Disciplines** line in each skill document, such as
`Disciplines: QA, Security, DevOps`, listing only the relevant disciplines.
This allows browsing by expertise while keeping lifecycle stages as the main
folder structure. A skill may serve several disciplines.

Skills support the people responsible for these decisions. Each skill should
state which actions an agent may perform, what evidence it must provide, and
when a responsible person must review or authorize the next step. Completion
of an agent workflow does not by itself establish release or security approval.

## Proposed directory layout

The layout below illustrates placement; most listed skills are proposals.
See the README for available skills.

```text
speedrun/
├── README.md
├── PROJECT_STRUCTURE.md
└── skills/
    ├── 00-agent-foundations/
    │   ├── context-management/
    │   │   └── SKILL.md
    │   ├── tool-use/
    │   │   └── SKILL.md
    │   └── skill-authoring/
    │       └── SKILL.md
    ├── 01-discovery/
    │   └── repository-exploration/
    │       └── SKILL.md
    ├── 02-specification/
    │   ├── acceptance-criteria/
    │   │   └── SKILL.md
    │   └── security-requirements/
    │       └── SKILL.md
    ├── 03-design/
    │   ├── architecture-design/
    │   │   └── SKILL.md
    │   ├── threat-modeling/
    │   │   └── SKILL.md
    │   └── infrastructure-design/
    │       └── SKILL.md
    ├── 04-planning/
    │   ├── implementation-planning/
    │   │   └── SKILL.md
    │   └── test-strategy/
    │       └── SKILL.md
    ├── 05-implementation/
    │   ├── feature-development/
    │   │   └── SKILL.md
    │   ├── test-automation/
    │   │   └── SKILL.md
    │   ├── secure-implementation/
    │   │   └── SKILL.md
    │   └── pipeline-implementation/
    │       └── SKILL.md
    ├── 06-validation/
    │   ├── code-review/
    │   │   └── SKILL.md
    │   ├── exploratory-testing/
    │   │   └── SKILL.md
    │   ├── regression-testing/
    │   │   └── SKILL.md
    │   ├── security-review/
    │   │   └── SKILL.md
    │   └── infrastructure-validation/
    │       └── SKILL.md
    ├── 07-release/
    │   ├── create-changelog/
    │   │   ├── SKILL.md
    │   │   └── agents/
    │   │       └── openai.yaml
    │   ├── release-preparation/
    │   │   └── SKILL.md
    │   ├── deployment/
    │   │   └── SKILL.md
    │   └── rollback/
    │       └── SKILL.md
    └── 08-operations/
        ├── incident-diagnosis/
        │   └── SKILL.md
        ├── observability/
        │   └── SKILL.md
        ├── incident-response/
        │   └── SKILL.md
        └── security-incident-response/
            └── SKILL.md
```

`00-agent-foundations` contains capabilities used across stages: managing
context, using tools, handling permissions, and authoring skills. It is a shared
category, not a prerequisite phase for every task.

## Placement rules

- Give each skill one home, based on its primary outcome. Link to related skills
  instead of duplicating their instructions.
- Use lowercase names with hyphens and keep skill names unique across categories.
- Keep the reusable workflow in `SKILL.md`. Document agent specific requirements
  there when needed.
- Add `scripts/`, `references/`, or `assets/` inside a skill directory only when
  the skill uses them. Do not create empty supporting folders.
- Keep category names focused on outcomes. A framework or programming language
  belongs in a skill's name or description when it affects the workflow.

For example, debugging belongs in operations when it diagnoses a production
incident, and validation when it diagnoses a failing check before release.
Choose the primary use case and describe the other supported uses in the skill.

## Skill document contents

Each `SKILL.md` should explain:

1. **Purpose and trigger:** what it accomplishes and when to use it.
   Include the relevant disciplines so people can find skills for their role.
2. **Inputs:** required context, files, tools, and permissions.
3. **Workflow:** concrete steps, including when to ask for missing information.
4. **Verification:** evidence needed to assess the result.
5. **Output and handoff:** what the next person or agent receives.
6. **Limits:** unsupported cases and any agent specific setup.

Repository categories organize browsing. Agent installation and discovery rules
must be documented and checked separately; this layout alone does not establish
compatibility with an agent's skill loader.
