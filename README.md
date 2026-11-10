# Quiz CLI

## 1. High-Level Description

Quiz CLI is intended to be an interactive terminal quiz for learners testing their programming knowledge. The package describes it as a game for learning JavaScript. The entry point lets a player choose a category and quiz length, answer questions, view results, and choose whether to play again.

**Repository status:** The committed application is incomplete and cannot currently start. `index.js` imports `src/input.js`, `src/quiz.js`, and `src/colors.js`, but none of these files are present. It also expects `data/questions.json`, which is absent. The workflow described below is established by the entry point; input validation, scoring, question rendering, and actual question content cannot be verified without those files.

### Technology stack

| Technology | Role |
| --- | --- |
| Node.js 18 or newer | Runtime required by `package.json` |
| JavaScript ES modules | Enabled by `"type": "module"`; used for imports and module-relative paths |
| `node:fs/promises` | Asynchronous reading of the question bank |
| `node:url` | Converts `import.meta.url` into a filesystem path |
| `node:path` | Resolves the question bank relative to the entry point |
| Node.js test runner | Invoked by the declared `npm test` script |

No third-party dependencies or development dependencies are declared. Package metadata identifies the application as `quiz-cli`, version `1.0.0`, with an MIT license declaration; a license file is not included in the repository.

## 2. Features

The following behavior is implemented or orchestrated in `index.js`, but depends on the missing modules and data to run:

- **Category selection:** Builds a menu from the names in `data.categories` and uses the selected index to identify the category.
- **Quiz-length selection:** Always offers all questions, offers three questions when the category has at least three, and five when it has at least five.
- **Ordered question selection:** Passes the first selected number of questions to the quiz using `slice(0, questionCount)`. The entry point does not shuffle them; any further ordering inside the missing `Quiz` class is unknown.
- **Interactive quiz loop:** Creates a `Quiz` instance, awaits each question, and continues until `quiz.isComplete` is true.
- **Pause prompts:** Waits for Enter before the quiz and between questions, except after the last question.
- **Result display and replay:** Calls `quiz.showResults()` and asks whether to start another quiz. The question bank is loaded once before the replay loop.
- **Terminal presentation:** Clears the console for each new quiz and uses color helpers for the banner, instructions, success messages, and errors. Exact styling depends on the missing color module.
- **Asynchronous orchestration:** Uses promises and `async`/`await` for file loading and user interactions.
- **Runtime error reporting:** The main loop catches runtime errors, prints their messages and stack traces, and exits with status 1. Static import failures occur before this handler can run.

Scoring rules, supported answer formats beyond the numeric-answer instruction, input validation, and category contents are not available for inspection.

## 3. Project Structure

The existing files on `main` at the time of inspection are:

```text
test-app/
├── .DS_Store
├── index.js
└── package.json
```

This generated `README.md` is added on the documentation branch and is not part of that original inventory.

| File | Responsibility |
| --- | --- |
| `index.js` | Executable entry point with a Node.js shebang; loads data, constructs menus, runs the quiz loop, displays results, and handles replay and runtime errors |
| `package.json` | Defines package metadata, ES module mode, the Node.js engine requirement, and start/test scripts |
| `.DS_Store` | Binary macOS directory metadata, not application source; its contents could not be decoded by the text reader |

### Referenced files that are missing

These paths are required by the entry point but are **not present** in the inspected repository:

| Referenced path | Interface expected by `index.js` |
| --- | --- |
| `src/input.js` | Named exports `createInterface`, `select`, `confirm`, and `pressEnter`. The interface returned by `createInterface()` must expose `close()`. Menu selections are consumed through `index` or `value`; replay uses the return value of `confirm()`. |
| `src/quiz.js` | Named export `Quiz`, constructed with a questions array and category name; instances provide `isComplete`, asynchronous `askQuestion(rl)`, and `showResults()`. |
| `src/colors.js` | Formatting exports used as functions: `cyan`, `highlight`, `dim`, `info`, `success`, and `error`. |
| `data/questions.json` | JSON with a top-level `categories` object. Each category is accessed through its `name` and `questions` array. Individual question fields are unknown. |

The architecture separates orchestration, input handling, quiz logic, terminal formatting, and question data at the import level. Only orchestration is available for analysis. No lockfile, test files, environment template, CI configuration, or other documentation was found.
