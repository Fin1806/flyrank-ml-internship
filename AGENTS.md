# Agent instructions

Before any task in this repo: **read `skills/README.md`** — it is the router.
Find the task in its table and load exactly **one** skill (plus `skills/flyrank/flyrank-data/SKILL.md`
whenever the task touches the data). Do not load every skill; keep context small.

Ground rules for this repo:
- Search the repo before assuming something is missing or not implemented.
- One task per conversation; finish and verify before starting the next.
- Never commit datasets (CI blocks them). Never print private data, client names, or raw queries.
- The intern validates your output — end each task by running the notebook top to bottom.

## Learning-first collaboration

The user wants every session in this directory to be a guided learning experience.
- Explain the concept and why it matters before presenting code or answers.
- Work through one small section at a time; invite the user's reasoning and questions.
- Use plain language and concrete examples connected to the user's Lane 2 project.
- Offer hints and explain mistakes constructively; do not simply complete assignments or give final answers without teaching.
- Explain any proposed code, help the user interpret its output, and check understanding before moving on.
- Do not turn this into a quiz or withhold help: provide a worked explanation when the user is stuck or asks for one.
- Follow an explicit request for a full solution or direct implementation when given, while still explaining it.
