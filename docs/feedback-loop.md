# Feedback loop

The program will guess, and some guesses will be wrong. You need a way to say so, and the program needs to remember what it said and what you said. That memory is how identification gets better later.

This is a starting point. Column names can change when we design the program. Nothing writes this file yet.

## What you can tell it

Speak in ordinary sentences. The program can store them as a short label:

- **This piece does not fit there.** You tried the suggestion (or you can see it is impossible) and the piece will not seat in the green spot.
- **This piece marked missing is actually here.** The program thought a position was empty because it could not find a piece. You found the piece.
- **That's the wrong piece.** The green spot may be right, but the blue piece is a different one.
- **I placed a different piece.** You overrode the suggested order. See [assist modes](assist-modes.md). The row should also store your order preference (you chose your own piece) and which piece you placed.

You can add a note in your own words any time. The label is for sorting later. The note is for you.

## What gets saved with every correction

Each correction is one row, stored **next to the guess it corrects**:

| Field | Plain meaning |
| --- | --- |
| timestamp | When you made the correction |
| photo | The picture the program was looking at |
| predicted piece | Which loose piece it chose |
| predicted position | The row and column it chose (the green spot) |
| confidence | How sure it was, as a number from 0 to 1 |
| actual outcome | What you said was true, including the piece you placed if you overrode the order |
| correction kind | One of the labels above, or `order_override` when you placed a different piece |
| order preference | Whether you followed the suggested order or chose your own |
| notes | Optional sentence in your own words |

**Confidence** can be simple at the start. If the outline match is a score, save that score. A confident wrong answer is a more useful lesson than a shy one, because it shows the matcher was sure and still mistaken. The habit of saving the number matters more than making the number clever.

Save the photo file the row points at. A later change to the matcher can be tried again on the same picture.

## Where the rows live

Start with a **CSV** file, for example `corrections.csv` in the project folder. A CSV is a text table. LibreOffice Calc can open it. *[Automate the Boring Stuff](https://automatetheboringstuff.com/)* teaches CSV files (2nd edition chapter 16, 3rd edition chapter 18), so this is a realistic first version. You can append a row with a short script, or even type one by hand while you are learning.

**SQLite** is the next step when you want questions like "show every wrong-piece correction from section A." A SQLite database is a single file, for example `corrections.sqlite`. The 3rd edition of the book has a SQLite chapter (chapter 16). If your paper book is the 2nd edition, stay on CSV until you feel like learning SQL. Both formats can hold the same fields.

Use `.csv` or `.sqlite` for these records. The Python gitignore ignores files that end in `.log`, and it also ignores a file named exactly `db.sqlite3`, so `corrections.log` or `db.sqlite3` would stay off GitHub. `corrections.csv` and `corrections.sqlite` will be seen by git.

The missing-piece list is its own small file, `missing-pieces.csv` (the positions the program could not fill). When you later say "this piece marked missing is actually here," add a feedback row **and** update `missing-pieces.csv`. The feedback row remembers the mistake. The missing-piece file should describe the board as you now believe it is.

## How the rows help later

Two stages, in this order:

1. **Tuning the rules.** You read the rows, notice a pattern (a tab was treated as a flat edge, or a low-confidence guess was usually wrong), and adjust the matching rule. Then you re-run an old photo and compare.
2. **A model, once there are enough rows.** The same rows are labeled examples: here is the photo, here is what the program predicted, here is what John said. That is the kind of set a later machine-learning experiment needs. Collecting rows from the first real guesses means that experiment has something to learn from.

Piece backs in a section all show the same letter, so the letter will not separate piece 12 from piece 13. Shape and position do that work. The corrections are the record of where shape-matching fails.

## What you do on the phone

When the page shows a blue circle and a green mark, it should also offer the sentences above, plus "I placed a different piece." Sending one of them writes a row. Building that button is a roadmap milestone, after a CSV row can be written at all.

Until the page exists, a row you add yourself still counts. The loop is the saved pair (guess, correction), and the phone is just a convenient way to type it.
