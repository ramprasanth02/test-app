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

## 4. Getting Started

### Prerequisites

- **Node.js 18.0.0 or newer**, as declared in `package.json`.
- **npm**, to use the package scripts. The entry point can also be invoked directly with Node.js.
- **Git**, if cloning rather than downloading the repository.
- An interactive terminal for the intended menu and answer prompts.
- The missing input, quiz, and color modules and question bank listed above. These must be supplied before the application can run.

### Installation

1. Clone the repository and enter its directory:

   ```sh
   git clone https://github.com/ramprasanth02/test-app.git
   cd test-app
   ```

2. Verify your runtime:

   ```sh
   node --version
   npm --version
   ```

3. Review `package.json`. It declares no dependencies, so no third-party package installation is required for the available code. If you use the normal npm installation workflow, the command is:

   ```sh
   npm install
   ```

   This does not restore the missing local modules or question bank. Depending on npm configuration, it may create a lockfile.

4. Obtain the missing files from the project maintainer and place them at the exact paths listed in **Project Structure**. No source or generation instructions for these files are included.

### Configuration

No environment variables, credentials, external services, or command-line configuration options are referenced in the available code.

The question bank is loaded from `data/questions.json`, resolved relative to `index.js`, not the terminal's current working directory. It must be valid JSON with a `categories` object whose entries provide a `name` and a `questions` array. The entry point does not validate this structure before using it.

The complete question schema cannot be documented because both the data file and its consumer, `src/quiz.js`, are missing. Categories and questions cannot be enumerated from the current repository.

### Running the Application

From the repository directory, the declared start command is:

```sh
npm start
```

The equivalent direct invocation is:

```sh
node index.js
```

**Both commands currently fail** because the imported local modules are absent. These are the supported launch commands, not a claim that the current checkout is runnable.

Although `index.js` includes a Node.js shebang, `package.json` has no `bin` mapping. No globally installed CLI command is defined.

### Usage

Once the missing files have been supplied, the entry point defines this interaction flow:

1. Create the input interface and load the question bank.
2. Clear the terminal and display the Quiz CLI welcome banner.
3. Choose a category from the names in the question bank.
4. Choose **All questions**, **3 questions**, or **5 questions**, depending on the category's question count.
5. Press Enter to begin.
6. Answer each question. The application instructs users to select an answer by entering its number; exact prompt formatting and accepted inputs depend on the missing modules.
7. Press Enter between questions when prompted.
8. View the results produced by `quiz.showResults()`.
9. Respond to **Would you like to play again?** to repeat or exit. The accepted confirmation syntax is not available in the inspected code.

For example, if a supplied category contains at least three questions, selecting **3 questions** passes that category's first three questions to `Quiz`. This example does not establish any category name, question text, scoring rule, or answer key.

When the player finishes, the entry point prints `Thanks for playing! Keep learning!` followed by a rocket emoji. The normal completion path closes the input interface in `finally`. On a caught error, `process.exit(1)` terminates the process immediately; the code should not be relied on to execute cleanup afterward.

### Testing

The package declares:

```sh
npm test
```

This runs `node --test`. No test files are included, so the script alone does not demonstrate coverage or verify the intended quiz behavior. No tests or application runs were executed during this repository inspection.

### Troubleshooting

| Problem | Explanation and next step |
| --- | --- |
| Startup reports a missing module, such as `ERR_MODULE_NOT_FOUND` | The entry point statically imports absent files under `src/`. Obtain the expected implementations; installing npm packages does not replace these relative imports. Static import errors occur before `main()` begins. |
| The question bank cannot be opened, such as an `ENOENT` error | After the imports are fixed, startup requires `data/questions.json` at the path relative to `index.js`. Restore that file. |
| JSON parsing fails | Ensure the question bank contains valid JSON. `loadQuestions()` uses `JSON.parse()` and does not accept comments or other non-JSON syntax. |
| Category access or quiz initialization fails | Check that `categories` is an object and each category provides `name` and a `questions` array. Consult the restored `Quiz` implementation for individual question requirements. |
| Three- or five-question options are missing | This is intentional when the selected category has fewer than three or five questions, respectively. |
| You expect an installed `quiz-cli` command | No `bin` entry exists. Use `npm start` or `node index.js` from the repository instead. |
| The runtime is older than Node.js 18 | Upgrade to meet the package's declared engine requirement. |

### Inspection Limitations

All three files listed on `main` were checked. Both text files were read in full, and the referenced `src/` and `data/` directories returned no files. `.DS_Store` was accessible in the listing but could not be read as UTF-8 because it is binary metadata. The missing implementation and data prevent verification of scoring, input validation, color behavior, actual questions, and successful end-to-end execution.
