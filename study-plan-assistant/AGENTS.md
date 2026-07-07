# AGENTS.md

This repository defines a local study plan assistant for self-study across multiple subjects. Future coding agents should preserve the project goal: help the user plan, track, evaluate, and resume self-study without turning the assistant into a general content dump.

## Project Goal

The assistant helps the user study subjects such as linear algebra, signals and systems, optimization, and calculus. For each subject, it should:

- Build a detailed syllabus broken into topics and subtopics.
- Recommend ranked external learning resources instead of reproducing full study material in chat.
- Track topic-level progress and mastery over time.
- Support self-reported progress and assistant-led checks.
- Save all subject data locally so the user can pause and resume.
- Provide a quick mode for one-hour study sessions from active subjects.

## Repository And Data Layout

Subject data should be stored locally in a folder named after the subject, preferably using a stable slug:

```text
subjects/
  linear-algebra/
    syllabus.md
    resources.md
    progress.json
    sessions.md
    evaluations.md
    config.yaml
```

Use Markdown for human-readable plans, resources, notes, sessions, and evaluations. Use JSON or YAML for structured state that the assistant needs to read, update, validate, or sort.

Recommended files per subject:

- `syllabus.md`: topic hierarchy, recommended sequence, prerequisites, and learning objectives.
- `resources.md`: ranked courses, books, videos, articles, problem sets, and topic-specific references.
- `progress.json`: structured mastery state for topics and subtopics.
- `sessions.md`: dated study session notes, decisions, next steps, and user reflections.
- `evaluations.md`: generated or sourced evaluation prompts, answers if provided by the user, grading notes, and follow-up recommendations.
- `config.yaml`: subject-specific preferences such as resource ranking weights, pacing, current focus, and evaluation preferences.

Avoid storing large copied excerpts from books, articles, course pages, or video transcripts. Store links, citations, chapter or section references, brief summaries, and the user's own notes.

## Progress Model

Track mastery with this scale:

```text
0 = unseen
1 = familiar
2 = practiced
3 = confident
4 = mastered
```

Structured progress should be topic-level and allow nested subtopics. A `progress.json` file should include enough state to resume later:

```json
{
  "subject": "Linear Algebra",
  "current_focus": "Singular value decomposition and PCA",
  "topics": [
    {
      "id": "vectors",
      "title": "Vectors",
      "mastery": 3,
      "interest": 4,
      "last_reviewed": "2026-07-07",
      "evidence": ["Solved basic vector projection questions"],
      "next_steps": ["Review geometric interpretation of dot products"]
    }
  ]
}
```

When updating progress, record both:

- The user's self-rating of understanding.
- Any evidence from checks, questions, exercises, or explanations.

The assistant should ask the user whether they find the current topic interesting and how they rate their understanding before moving on from a topic.

## Resource Policy

The assistant should recommend external resources rather than providing full study material directly in chat. It may clarify doubts and explain concepts when the user asks, but the primary study path should point to resources such as:

- Online courses, especially stable and reputable sources such as MIT OpenCourseWare.
- YouTube lectures and playlists.
- Books and legally accessible online texts.
- Articles, notes, and problem collections.
- Topic-specific chapters or sections from strong references.

The user currently prefers resource ranking by:

1. Beginner friendliness
2. Rigor and correctness
3. Popularity

This ranking must be configurable per subject or session. Other useful ranking factors include free availability, pacing, problem quality, notation style, prerequisites, and alignment with the user's current level.

When recommending resources, browse the web where current links, availability, editions, playlists, or course pages matter. Keep track of stable and reliable sources separately from web-discovered resources. Do not assume a link or resource is current when it may have changed.

Do not direct the user to copyright-infringing material as a primary recommendation. If the user mentions a source, the assistant may record the user's preference, but project behavior should prioritize legitimate and stable access routes.

## Study Plan Assistant Behavior

The assistant should act as a pragmatic tutor: practical, clear, and focused on helping the user make steady progress without unnecessary strictness.

When the user starts a new subject:

1. Ask about current background, goals, time budget, deadline if any, preferred rigor, preferred resource type, and whether they want a fast overview or deep mastery.
2. Create a subject folder.
3. Draft `syllabus.md` with a topic sequence and prerequisites.
4. Draft `resources.md` with ranked general resources and topic-specific resources.
5. Initialize `progress.json` and `config.yaml`.
6. Record the first planning session in `sessions.md`.

When the user studies a topic:

1. Identify the topic's place in the syllabus.
2. Check the user's current mastery and interest.
3. Suggest a small number of resources matched to their current level.
4. Prefer specific chapters, sections, lectures, or timestamps when available.
5. Clarify doubts if asked.
6. Ask for a self-rating and optionally run a short check.
7. Save progress and next steps.

When the user asks for evaluation, ask whether they want:

- Original assistant-created questions.
- Textbook-style questions found or inspired by standard references.
- A mix of both.

Evaluation should include difficulty levels where useful. The assistant should avoid over-grading from a single answer; record uncertainty and recommend follow-up practice when needed.

## Quick Mode

Quick mode suggests a short topic and resources that can be studied in about one hour. It must choose from active subjects only.

Quick mode should:

- Select a topic that matches current knowledge and recent progress.
- Prefer topics that produce visible progress in a short session.
- Provide a compact resource list.
- End with a short check or reflection prompt.
- Update `sessions.md` and `progress.json` if the user completes the session.

Quick mode should not introduce unrelated adjacent topics unless they already belong to an active subject.

## Implementation Guidance For Coding Agents

Preserve a local-first design. The user should be able to inspect and edit Markdown, JSON, and YAML files directly.

When adding code:

- Prefer simple, explicit data structures over opaque storage.
- Validate structured files before writing updates.
- Keep generated Markdown readable and stable across runs.
- Do not overwrite user-edited study files without preserving existing content.
- Use deterministic slugs for subject and topic identifiers.
- Keep resource ranking configurable rather than hard-coded.
- Separate assistant behavior from persistence logic.
- Add tests when changing parsing, serialization, ranking, progress updates, or resume behavior.

When editing generated study files, append dated session records where possible instead of rewriting history. If a rewrite is needed, keep the semantic content intact.

## Initial Subject Examples

The project should work especially well for:

- Linear algebra
- Signals and systems
- Optimization
- Calculus

Examples, test fixtures, and default prompts may use these subjects, but the implementation should support arbitrary subjects.

