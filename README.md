# Flutter Quiz App

A quiz application built with Flutter and Dart covering fundamental Flutter concepts from Academind's Flutter course. The app quizzes users on core Flutter concepts, tracks their chosen answers, shuffles options dynamically, and provides a scrollable summary of their results at the end.

## What the App Does

- **Start Screen:** A landing screen featuring the Flutter quiz logo with opacity overlay, styled with Google Fonts (Lato), and an outlined button to start the quiz.
- **Questions Screen:** Displays multiple-choice questions one at a time. Answer buttons are generated dynamically from data and shuffled on each attempt.
- **Results Screen:** Compares the user's selected answers against the correct answers, calculates the score dynamically, and displays a scrollable summary list.

## Key Concepts and Implementation Details

### 1. Lifting State Up
State should live where it is needed by multiple components. Because the user's chosen answers needed to be evaluated and displayed on the Results Screen, keeping `selectedAnswers` inside `QuestionsScreen` wouldn't work. The state was lifted up to the parent `Quiz` widget (`_QuizState`), which acts as the controller managing active screens and accumulating chosen answers.

### 2. Bridging StatefulWidget and State with `widget.`
In Flutter, a stateful widget is split into two separate classes: the `StatefulWidget` configuration class and the `State` logic class. When callback functions like `onSelectAnswer` are passed in from a parent widget, they arrive in the `StatefulWidget` class. Inside the `_QuestionsScreenState` class, Flutter provides the `widget.` reference to access those constructor arguments cleanly.

### 3. Safe Shuffling with `List.of()`
Dart's built-in `.shuffle()` method modifies lists in place and returns nothing. In the dataset, the first answer is always the correct one. Calling `.shuffle()` directly on the original answers list would permanently mutate the answer key. To prevent this, `List.of(answers)` creates a fresh copy of the list before shuffling it, preserving the original data.

### 4. Dynamic Widget Generation (`.map` and Spread Operator `...`)
Instead of duplicating answer buttons manually, `.map()` transforms each answer string into an `AnswerButton` widget. Because `.map()` returns an `Iterable` and `Column` children expect individual widgets, the spread operator (`...`) unpacks the transformed widgets directly into the list of column children.

### 5. Handling Layout Overflows
- **Horizontal Overflow:** Inside a `Row`, an unconstrained `Column` with long text tries to expand infinitely, breaking outside screen bounds. Wrapping that inner column with `Expanded` constrains it to the available row width, enabling automatic text wrapping.
- **Vertical Overflow:** On the Results Screen, the question summary exceeded screen height and pushed buttons off screen. Wrapping the summary column in a `SingleChildScrollView` inside a fixed-height `SizedBox(height: 300)` made the list scrollable while keeping the layout intact.

### 6. Working with Maps and Type Casting
To bundle question details together, summary items are stored as `Map<String, Object>`. Because the map holds mixed data types (integers and strings), Dart treats the values as `Object`. Explicit type casting with `as int` and `as String` is used when doing arithmetic (like displaying 1-based question indexes) or passing values to `Text` widgets.

### 7. Modern Dart Syntax
- **Getters:** Replaced zero-argument methods like `getShuffledAnswers()` and `getSummaryData()` with getters (`get shuffledAnswers`, `get summaryData`) for cleaner property-like access.
- **Arrow Functions (`=>`):** Used concise arrow syntax for single-expression functions, such as filtering correct answers with `.where((data) => data['user_answer'] == data['correct_answer']).length`.
- **Private Identifiers (`_`):** Used leading underscores to mark classes (`_QuizState`, `_QuestionsScreenState`) as private to their respective files.

## Project Structure

```text
lib/
├── data/
│   └── questions.dart         # Question dataset
├── models/
│   └── quiz_question.dart     # Question data model with shuffledAnswers getter
├── answer_button.dart         # Reusable styled button with rounded borders
├── main.dart                  # App entry point
├── questions_screen.dart      # Question prompt and dynamic answer buttons
├── questions_summary.dart     # Scrollable summary rows for results
├── quiz.dart                  # Root stateful widget managing navigation and answers
├── results_screen.dart        # Score calculation and results view
└── start_screen.dart          # Landing screen with logo and start trigger
```

## Running the Project

1. Ensure the Flutter SDK is installed and configured:
   ```bash
   flutter doctor
   ```
2. Get project dependencies:
   ```bash
   flutter pub get
   ```
3. Run on a connected emulator or device:
   ```bash
   flutter run
   ```
