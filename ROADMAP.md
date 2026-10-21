# Roadmap

This is a starting point. The coding plan will be worked out with you after your other project. Until then, this is the order to grow the helper: one testable step, then the next.

Check a box when that step works for you. Setup and the book happen on the laptop. From the proof of concept onward, the test piece is section A. A phase is finished when its **Done when** line is true. Then start the next phase.

Phases 0 through 7 are software. Phone photos and the Ubuntu virtual machine are enough for all of them. Phases 8 and 9 are hardware. Phase 10 ties together whatever you have built. Hardware waits until the software phases pass.

The normal update, all the way through the software phases, is a still photo you take when you want one. Continuous watching (a webcam or a video stream) is Phase 8.

You are learning from Al Sweigart's *[Automate the Boring Stuff with Python](https://automatetheboringstuff.com/)*. The site is free. Your paper copy may be a different edition, and chapter numbers differ, so every chapter below is named. Numbers are given for the 2nd edition and the 3rd edition (the edition the free site follows as of 2026). A 1st edition is usable too: follow the topic names.

## Setup phase: Ubuntu in VMware

You already have VMware Workstation Pro on the Windows 11 laptop. The guide is [docs/setup.md](docs/setup.md).

- [ ] Create an Ubuntu Desktop LTS virtual machine (26.04 LTS is the current long-term release)
- [ ] Install open-vm-tools so the window, clipboard, drag-and-drop, and shared folders work
- [ ] Share a Windows folder, or drag a test photo into Ubuntu
- [ ] Install Python 3, pip, venv, and git
- [ ] Set your git name and email
- [ ] Connect the VM to GitHub with an SSH key or the GitHub CLI
- [ ] Clone this repository
- [ ] Create the virtual environment and install [requirements.txt](requirements.txt)
- [ ] Take a VMware snapshot once the import check passes

**Done when:** in the Ubuntu VM, with the virtual environment activated, this command prints `ok`:

```bash
python -c "import cv2, numpy, PIL; print('ok')"
```

## Phase 0: Python basics

Read in book order. The notes say where to linger. Skipping ahead to images before functions is a frustrating way to learn, so let the book set the pace. The groups below are about which chapters this project will lean on.

### The language, in order

- [ ] Python basics (2nd ed. ch. 1; 3rd ed. ch. 1)
- [ ] Flow control, including if/else and loops (2nd ed. ch. 2; 3rd ed. ch. 2 and ch. 3). Loops walk every piece and every cell in a row.
- [ ] Functions (2nd ed. ch. 3; 3rd ed. ch. 4). A named function is how "find the next spot" will be wrapped later.
- [ ] Debugging (2nd ed. ch. 11; 3rd ed. ch. 5). In the 3rd edition this chapter comes before lists. Read it when you reach it. You will want it the first time a script stops with an error.
- [ ] Lists (2nd ed. ch. 4; 3rd ed. ch. 6). A row of pieces, and the order of empty spots, are lists.
- [ ] Dictionaries (2nd ed. ch. 5; 3rd ed. ch. 7). One record can hold a piece's row, column, and shape.
- [ ] Strings (2nd ed. ch. 6; 3rd ed. ch. 8). Section letters such as `A`.

### These feed the puzzle program

- [ ] Reading and writing files (2nd ed. ch. 9; 3rd ed. ch. 10). The missing-piece list and the feedback log are files.
- [ ] Organizing files (2nd ed. ch. 10; 3rd ed. ch. 11). Keeping phone photos sorted by section.
- [ ] CSV and JSON (2nd ed. ch. 16; 3rd ed. ch. 18). The first feedback log is a CSV. This is one of the chapters that matters most.
- [ ] SQLite databases (3rd ed. ch. 16). The 2nd edition has no matching chapter. Optional, after CSV works. Stay on CSV if your book is the 2nd edition and you are not ready for SQL.
- [ ] Images with Pillow (2nd ed. ch. 19, "Manipulating Images"; 3rd ed. ch. 21, "Making Graphs and Manipulating Images"). Open, draw on, and save pictures before OpenCV. This is the other chapter that matters most.
- [ ] Command-line programs (3rd ed. ch. 12). The 2nd edition teaches this in the introduction and in the projects. This is how you will run the helper.
- [ ] Installing third-party packages (Appendix A in both editions). Pillow, OpenCV, and Jupyter.

### Useful, and fine to treat lightly

- [ ] Regular expressions (2nd ed. ch. 7; 3rd ed. ch. 9). This project barely uses them. Skim them once so the idea is familiar.
- [ ] Recognizing text in images (3rd ed. ch. 22 only). Might read the letter that names a section. Telling one A piece from another still depends on shape.
- [ ] Keeping time (2nd ed. ch. 17; 3rd ed. ch. 19). A timestamp on each correction uses a small part of this chapter. The scheduling parts can wait.

### Leave these until you are curious

Web scraping, Excel, Google Sheets, PDF and Word, email and texts, controlling the keyboard and mouse, and speech. They are worthwhile chapters. They are off the path to the first marked-up photo.

**Done when:** a script you wrote reads a photo filename from a text file and appends one CSV row with that name and today's date, and you have opened a puzzle photo with Pillow and saved a second copy.

## Phase 1: Proof of concept

Software. One photo in, one marked-up photo out. No suggestions yet. The goal is to **see** the loose pieces and the frame.

- [ ] Copy one straight-down phone photo of section A into [photos/](photos/), with even light and the board filling the frame
- [ ] Load it with OpenCV
- [ ] Convert it to grayscale and threshold it so pieces stand out from the grey board
- [ ] Detect contours around the loose pieces
- [ ] Find the assembled frame as its own shape
- [ ] Save a new image with those outlines drawn on it
- [ ] Compare the outline count to what you see, and note the lighting in the [learning log](docs/learning-log.md)

A Jupyter notebook is a fine place for this experiment. A script you can run again is a fine place too.

Contours will sometimes merge two pieces or miss one. That is a normal first result. Save the photo and write down what happened.

**Done when:** one command reads a single section A photo and writes a new image where the loose pieces and the frame are outlined. You can count the outlined pieces, and the count is close to what you see in the picture.

## Phase 2: Next-piece suggestion

Software. This phase is the longest of the early ones, because "which piece?" needs a first version of shape matching. Take it in two visible steps: the green spot first, then the blue piece.

Guided order, which is the only order this phase knows:

1. Top row, starting at the top-left empty spot.
2. Then right along that row.
3. Then the next row, from the left.

- [ ] From the frame, list empty cells in that reading order
- [ ] Mark the first empty cell in green on a copy of the photo
- [ ] For that cell, describe the edges the piece must have (tab, blank, or flat), using the frame and any pieces already touching the hole
- [ ] For each loose piece, describe its four sides the same way
- [ ] Choose the loose piece whose outline best matches the cell, and circle it in blue
- [ ] Save a confidence number for that choice (a rough match score is enough)
- [ ] Keep one photo where the blue piece is obviously wrong, so Phase 4 has a first correction to record

Colors from here on: **blue** is the loose piece to pick up, **green** is the spot where it goes. Phase 6 is where you can turn this suggestion off or ask about a different piece. This phase always answers "what is next in reading order?"

**Done when:** the same section A photo produces an image with one green mark on the correct next cell (top-left, then right, then the next row) and one blue mark on a loose piece. The blue piece is right on a photo where the fit is obvious, such as a very distinct tab. Harder photos can be wrong, as long as you saved them and wrote down why.

## Phase 3: Missing-piece tracking

Software. Sometimes the piece for the green spot is absent from the table.

- [ ] Decide a minimum match score below which you will refuse to pick a piece
- [ ] When every loose piece falls under that score, write the cell (section, row, column) to `missing-pieces.csv`
- [ ] Leave the green mark, and draw the photo so it is obvious that no blue piece was chosen
- [ ] Cover or remove one piece, run again, and see that position appear in the file
- [ ] Run once more with the piece back in view, and see that the file can record the position as found (a line you edit is enough at this stage)

**Done when:** you hide one piece that the previous phase would have chosen, run the program, and `missing-pieces.csv` gains that grid position. The annotated image shows the green spot and no blue piece.

## Phase 4: Feedback log

Software. Full explanation: [docs/feedback-loop.md](docs/feedback-loop.md).

- [ ] Create `corrections.csv` with columns for timestamp, photo, predicted piece, predicted position, confidence, actual outcome, correction kind, order preference, and notes
- [ ] Append one row for "that's the wrong piece" using a photo from Phase 2
- [ ] Append one row for "this piece does not fit there"
- [ ] Append one row for "this piece marked missing is actually here," and update the missing-piece file to match
- [ ] Append one row for "I placed a different piece," with order preference set to show you chose your own piece
- [ ] Open the CSV in LibreOffice Calc (or print it from Python) and read the rows back

A short script is the goal. Typing a row by hand counts while you are still on the CSV chapter of the book. SQLite can replace the CSV later (3rd edition chapter 16). It is a checkbox you may leave open until the CSV feels easy.

- [ ] Optional: the same fields in a SQLite file, and one query that prints every `wrong_piece` row

**Done when:** `corrections.csv` has a real row for at least one correction, and that row includes the photo name, the predicted piece, the predicted position, a confidence number, what you said was true, and a timestamp. The other correction kinds are the rest of the boxes in this phase. Fill those in before you call the phase finished.

## Phase 5: Phone web app

Software. The phone becomes the screen. Flask is the simpler first page. FastAPI is the other option if you prefer it when you get here. Add whichever library you choose to `requirements.txt` at that time.

The page is for your phone on your home Wi-Fi. It is a private page in the virtual machine, not a site on the public internet.

- [ ] Switch the VM network adapter to Bridged, or set up port forwarding, using [docs/setup.md](docs/setup.md), so the phone can reach Ubuntu
- [ ] Serve a page that says hello, listening on the VM's network address so the phone can open it (a server bound only to localhost stays inside Ubuntu)
- [ ] Open that hello page on the phone
- [ ] Add a way to send one photo from the phone
- [ ] Run the Phase 2 steps on that photo
- [ ] Return the photo with the blue circle and the green mark, and see it on the phone
- [ ] Confirm you did this without copying the file onto Windows by hand

**Done when:** the phone, on the same Wi-Fi, submits one new photo and displays the annotated result. That photo was taken because you wanted an update. Nothing is watching the board between photos.

## Phase 6: Assist modes, re-sync, and reverse lookup

Software. Full explanation: [docs/assist-modes.md](docs/assist-modes.md). The phone page grows the controls. The vision code grows a re-sync.

- [ ] Add Off. The page stays quiet, and you can keep building
- [ ] Add Guided, using the Phase 2 reading order (top-left, then right, then the next row)
- [ ] Take a fresh photo after you have placed pieces with help off, and check that filled spots match the picture (re-sync)
- [ ] Turn Guided back on and confirm the next green spot is the first cell that is still empty in the new photo
- [ ] Add reverse lookup: you indicate a loose piece, and the page answers with the green target spot
- [ ] Add "I placed a different piece." The page writes a feedback row with your order preference and the piece you actually placed
- [ ] Add the other correction buttons from [docs/feedback-loop.md](docs/feedback-loop.md) so a wrong guess is logged from the phone

**Done when:** all three of these work on section A.

1. Guided mode marks the next cell in reading order.
2. After Off, you fill some spots yourself, take one new photo, and the program's filled spots match that photo.
3. You point at one loose piece and the green mark shows where it belongs.

Also, one order override from the phone shows up in `corrections.csv` with your order preference filled in.

## Phase 7: Improve identification from the feedback log

Software, and the last software phase before any hardware. Let real corrections pile up from normal puzzling before you expect much from this. A handful of rows is enough to retune a rule. A model wants more.

- [ ] A script reads `corrections.csv` and prints a short report: how many rows, and which correction kinds appear
- [ ] Change one matching rule because of that report (for example, how similar two outlines must be, or how you tell a tab from a flat edge)
- [ ] Keep a before/after pair of images for one photo the old rule got wrong
- [ ] Stretch goal: train a small model on the rows (photo, prediction, confidence, what you said) and compare it with the retuned rule on those same photos

**Done when:** you can show a before/after on at least one saved photo, produced by a rule you changed because of the log, or by a small model that beats the previous rule on that photo. One of those is enough. Hardware does not require the stretch goal. The stretch goal stays on the list.

## Phase 8: Live camera

Hardware. This is the first phase that needs a device plugged into the virtual machine. USB passthrough is in [docs/setup.md](docs/setup.md) under **VM > Removable Devices**.

Phone photos remain the everyday method. This phase adds continuous watching for times when a camera can actually see the board.

- [ ] Connect a webcam to the Ubuntu guest and confirm it shows up (for example `/dev/video0`)
- [ ] Grab a frame from that camera and run the same annotation you already run on phone photos
- [ ] Refresh the annotated view while you leave the pieces still, so you can see that it is live
- [ ] Send a phone photo through the web page during the same session, and confirm that path still works

A stream from the phone's camera, if you would rather avoid a webcam, can satisfy the same test. The done line is about continuous frames, not about a particular brand of camera.

**Done when:** the annotated view updates from a live camera while the board sits still, and a manually snapped phone photo still works in the same session.

## Phase 9: Lights

Hardware, and optional. Build this when you want the board itself to glow. Skip it and the phone page is still the full helper.

The lights repeat the colors the phone already uses. Blue on the loose piece. Green on the target spot. In reverse lookup, green is the home of the piece you asked about.

- [ ] Choose a pointer: a small projector, or laser pointers moved by servos (Arduino) or a galvo (a small mirror that steers a beam)
- [ ] If you use an Arduino, connect it with **VM > Removable Devices** and find its serial port in Ubuntu
- [ ] Put the light on a chosen mark on a blank area of the board before you aim at real pieces
- [ ] For one guided suggestion, put blue on the loose piece and green on the target, matching the phone image of that same moment
- [ ] Decide whether a Raspberry Pi should eventually run the finished program beside the table. The laptop VM can keep doing that job if you want it to

**Done when:** for one suggestion, the blue light and the green light land on the same piece and the same spot the phone is showing.

## Phase 10: Integration

One sitting that uses the pieces you have actually built. Lights are part of the test only if you built Phase 9.

- [ ] Guided mode suggests a piece, and you either place it or override it
- [ ] Switch Off, place a few pieces yourself, and re-sync with one new photo
- [ ] Ask where one piece in your hand belongs
- [ ] Reject one suggestion and see the new row in the feedback log
- [ ] If the lights exist, confirm they match the phone for that same suggestion
- [ ] Write down what still fails, in the learning log
- [ ] Try a second section only after section A feels boring and reliable

**Done when:** in one sitting on section A you use guided mode, switch it off, re-sync from a photo, ask where one piece goes, and log one correction. The phone shows each answer. If the lights exist, they agree with the phone on one of those answers.
