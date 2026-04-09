---
name: skill-learning
description: >
  Use this skill whenever the user wants AI Agent to learn, master, study, or become an expert in
  any topic, technology, language, framework, domain, or concept — and save that knowledge as a
  reusable skill file. Triggers include phrases like "learn X", "master X", "study X", "become
  an expert in X", "teach yourself X", "create a skill for X", "save everything about X",
  "learn X from this course", "take this course on X", "follow this Udemy/Coursera/YouTube
  course", or "extract knowledge from this online course".
  After learning, AI Agent automatically creates a dedicated `skill_name/` folder and saves a
  comprehensive `skill_name.md` file inside it. The agent can reload the file later to instantly
  recall the skill. Always use this skill when the user wants persistent, reusable expertise
  captured in a file — even if they phrase it casually like "can you get good at Python for me?",
  "I want you to know React deeply", or "go through this course and learn everything".
---

# Skill Learning — Learn Anything & Save It

This skill enables AI Agent to **deeply learn any topic** — from web research, documentation,
**or online courses** — and save the distilled knowledge as a structured `skill_name.md` file
inside a dedicated `skill_name/` folder. The saved file acts as a reloadable "brain" — next time
the agent reads it, it instantly operates as an expert in that skill.

---

## Phase 1 — Clarify the Skill to Learn

Before researching, gather these details (ask only what isn't obvious from context):

| Question                                                                                        | Why it matters                 |
| ----------------------------------------------------------------------------------------------- | ------------------------------ |
| What is the skill/topic?                                                                        | Defines the scope              |
| What level of depth? (beginner / intermediate / expert / all)                                   | Controls breadth vs. depth     |
| Any specific sub-areas, frameworks, or versions to focus on?                                    | Avoids wasted research         |
| What will the agent _do_ with this skill? (write code, answer questions, tutor, build projects) | Shapes the knowledge structure |
| Should the output file be self-contained or reference external docs?                            | Determines bundling strategy   |
| Is there a specific online course to learn from? (URL, platform, title)                         | Enables course-based learning  |

If the user's request is clear (e.g., "learn Python"), proceed directly — don't over-ask.

---

## Phase 2 — Research & Learn

Use ALL available tools to build comprehensive knowledge:

### 2a. Web Research (use `web_search` + `web_fetch`)

Search for:

- Official documentation / spec
- Best practices and idioms
- Common pitfalls and how to avoid them
- Cheat sheets and reference cards
- Advanced patterns (design patterns, architecture, optimization)
- Real-world usage examples
- Version-specific differences if relevant

Run **multiple targeted searches**, for example for "Python":

```
web_search: "Python core concepts cheat sheet"
web_search: "Python best practices 2025"
web_search: "Python common mistakes beginners"
web_search: "Python advanced patterns generators decorators"
web_search: "Python standard library most useful modules"
```

Fetch full pages (not just snippets) for the most authoritative sources.

### 2b. Online Course Research (use `web_search` + `web_fetch` + `read_url_content`)

When the user provides a course URL or asks to learn from an online course:

1. **Extract the course structure** — Fetch the course landing page and syllabus/curriculum to
   identify all modules, sections, and lessons.
2. **Map the learning path** — Note the order of topics as the course creator intended.
3. **Fetch accessible content** — For each module/section:
   - Read available lesson descriptions, summaries, and outlines
   - Extract code examples, project files, and exercise descriptions
   - Capture key takeaways and learning objectives listed per section
4. **Supplement with free resources** — For each core topic in the syllabus, search for:
   - Related official documentation
   - Free tutorials that cover the same concept
   - Community blog posts and discussions about the topic
   - Open-source code examples that match the course exercises
5. **Capture course metadata** —
   - Course title, platform, instructor
   - Total duration, number of modules
   - Prerequisites listed
   - Target audience and skill level

Example searches when a user says "learn React from this Udemy course":

```
web_fetch: <course_url> (to get syllabus and module list)
web_search: "React component lifecycle explained"
web_search: "React hooks tutorial with examples"
web_search: "React state management patterns 2025"
web_search: "React project structure best practices"
```

> **Tip**: Even when learning from a single course, always cross-reference with official docs and
> community resources to fill gaps and ensure accuracy.

### 2c. Synthesize Into Structured Knowledge

Organize everything learned into these canonical sections (adapt as needed for the skill):

1. **Overview** — What is it, why it matters, when to use it
2. **Core Concepts** — Fundamental ideas, mental models, terminology
3. **Syntax / API Reference** — Key syntax, commands, functions, methods (with examples)
4. **Patterns & Best Practices** — Idiomatic usage, recommended approaches
5. **Common Pitfalls** — Mistakes to avoid, gotchas, edge cases
6. **Advanced Topics** — Power features, performance, architecture
7. **Tooling & Ecosystem** — Libraries, frameworks, tools, package managers
8. **Quick Reference / Cheat Sheet** — Fast lookup card
9. **Examples** — Real, working code/usage examples
10. **Resources** — Top links for going deeper

---

## Phase 3 — Create the Skill Folder & Write the `skill_name.md` File

### Folder & File Naming Convention

Convert the skill name to lowercase with hyphens. **Create a folder** with that name, then save
the `.md` file inside it:

- "Python" → `python/python.md`
- "React Hooks" → `react-hooks/react-hooks.md`
- "Docker Compose" → `docker-compose/docker-compose.md`
- "Machine Learning" → `machine-learning/machine-learning.md`

### Output Location

1. **Create the folder**: `Skills/<skill_name>/`
2. **Save the file inside it**: `Skills/<skill_name>/<skill_name>.md`

Each skill lives in its own dedicated folder so related assets, sub-skills, or examples can be
added alongside it later.

### File Structure Template

Use this exact structure for every generated skill file:

````markdown
---
skill: <skill_name>
version_covered: <version or "general" if not version-specific>
depth: <beginner | intermediate | expert | comprehensive>
last_updated: <YYYY-MM-DD>
source: <"web-research" | "online-course" | "hybrid">
course_info: > # Include only if learned from a course
  Platform: <platform_name>
  Course: <course_title>
  Instructor: <instructor_name>
  URL: <course_url>
use_when: >
  [One paragraph: when an agent should load and apply this skill file]
---

# <Skill Name> — Expert Reference

> **Quick Summary**: [2–3 sentence TL;DR of the skill]

---

## 1. Overview

[What it is, why it exists, when to use it vs. alternatives]

---

## 2. Core Concepts

[Fundamental mental models, key terminology, foundational ideas]

### Key Terms

| Term | Definition |
| ---- | ---------- |
| ...  | ...        |

---

## 3. Syntax / API / Command Reference

[The most important syntax, commands, methods — with brief examples inline]

```<language>
# Example here
```
````

---

## 4. Patterns & Best Practices

[Idiomatic ways to do things, recommended approaches, do's and don'ts]

### ✅ Do

- ...

### ❌ Don't

- ...

---

## 5. Common Pitfalls & Gotchas

[Mistakes beginners and intermediates make, surprising behaviors, edge cases]

---

## 6. Advanced Topics

[Power features, optimization, architecture decisions, advanced patterns]

---

## 7. Ecosystem & Tooling

[Key libraries, frameworks, tools, package managers, integrations]

| Tool/Library | Purpose | When to use |
| ------------ | ------- | ----------- |
| ...          | ...     | ...         |

---

## 8. Quick Reference Cheat Sheet

[Dense, fast-lookup format — commands, syntax, flags, shortcuts]

---

## 9. Worked Examples

[2–5 complete, realistic, well-commented examples]

### Example 1: [Name]

```<language>
# Full working example with comments
```

### Example 2: [Name]

```<language>
# Full working example with comments
```

---

## 10. Course Roadmap (if sourced from a course)

[Module-by-module breakdown of the course with key takeaways per section.
Include only when the skill was learned from an online course.]

| Module | Topic | Key Takeaway |
| ------ | ----- | ------------ |
| ...    | ...   | ...          |

---

## 11. Go Deeper — Resources

[Top 5–10 curated links: official docs, tutorials, books, tools, and the source course]

- [Official Docs](url)
- [Source Course](course_url) _(if applicable)_
- ...

---

_Generated by skill-learning. Reload this file to restore expert-level knowledge of <skill_name>._
_Source: <web-research | course_name on platform_name | hybrid>_

```

---

## Phase 4 — Quality Checklist

Before saving, verify the generated file meets these standards:

- [ ] **Self-contained**: Agent can load this file cold and operate as an expert immediately
- [ ] **Accurate**: All code examples are correct and runnable
- [ ] **Comprehensive**: Covers beginner → advanced without gaps
- [ ] **Scannable**: Headers, tables, and code blocks make it fast to navigate
- [ ] **Concise**: No padding — every line earns its place
- [ ] **Versioned**: Includes version info where relevant (e.g., Python 3.12, React 18)
- [ ] **Practical**: Emphasizes real-world usage, not just theory
- [ ] **Course-aligned** _(if from a course)_: Follows the course's learning path and covers all modules

---

## Phase 5 — Confirm & Present

After saving:
1. Use `present_files` to give the user the download link
2. Summarize what was captured: sections written, depth level, any gaps noted
3. Offer to deepen any section or add sub-skills

---

## Behavior Rules

- **Always create the folder and save the file** — the whole point is persistence. Never skip Phase 3.
- **Research first, write second** — don't rely solely on training data; use web search to get current, accurate information.
- **Embrace online courses** — when a course URL or name is provided, extract the full syllabus and map every module into the skill file. Supplement with external resources to fill gaps.
- **Create a dedicated folder per skill** — always `Skills/skill_name/skill_name.md` in lowercase-hyphen format.
- **Be opinionated** — include genuine best practices, not wishy-washy "it depends" answers (while noting where it genuinely does depend).
- **Include real code** — every skill file must have working examples, not pseudocode.
- **Cover gotchas** — the pitfalls section is often the most valuable part.

---

## Example Invocations

| User says | Folder created | Skill file created |
|---|---|---|
| "Learn Python for me" | `python/` | `python/python.md` |
| "Master React Hooks" | `react-hooks/` | `react-hooks/react-hooks.md` |
| "Study Docker" | `docker/` | `docker/docker.md` |
| "Become an expert in SQL" | `sql/` | `sql/sql.md` |
| "Learn machine learning basics" | `machine-learning/` | `machine-learning/machine-learning.md` |
| "Get good at Terraform" | `terraform/` | `terraform/terraform.md` |
| "Learn TypeScript deeply" | `typescript/` | `typescript/typescript.md` |
| "Learn React from this Udemy course: [url]" | `react/` | `react/react.md` |
| "Take this Coursera ML course and save it" | `machine-learning/` | `machine-learning/machine-learning.md` |
| "Follow this YouTube Node.js tutorial series" | `nodejs/` | `nodejs/nodejs.md` |
| "Go through this freeCodeCamp course on APIs" | `apis/` | `apis/apis.md` |
```
