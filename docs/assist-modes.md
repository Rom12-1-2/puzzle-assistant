# Assist modes

You can build with the helper, or on your own, and you can switch whenever you want. The helper has to catch up from a photo of the board. It remembers your corrections. It does not assume you followed its advice during the time it was off.

Nothing here is built yet. This page is the idea, in plain language, so the later program has a target. Details (exact buttons, exact file columns) can change when we design it.

Updates are photos you take on purpose. You snap a picture when you want the program to look. A webcam that watches all the time is a separate, later phase on the [roadmap](../ROADMAP.md).

## The three modes

### Off / manual

You build on your own. The program stays quiet: no blue circle, no green mark.

The board can change a lot while help is off. You might fill a whole row in an order of your own, skip around, or stop for the day. When you want the helper back, you take a **fresh photo**. That photo is the re-sync.

Re-sync means the program looks at the picture and decides which spots are filled **now**. The next suggestion, if you turn guidance back on, starts from those filled spots. The order you happened to use while help was off can be anything. The photo is the record of the board.

A new photo does this any time, including in the middle of guided mode (a piece may have landed slightly differently than the last guess). It matters most after Off, because that is when the board and the program's last guess can drift apart.

### Guided

You ask for the next piece. The program chooses the next empty spot in reading order:

1. The top row, starting at the **top-left**.
2. Then the next cell to the **right**.
3. When that row is full, the row below, again from the left.

It then picks a loose piece whose outline fits that spot (tabs, blanks, and flat edges). On the phone you see your photo with two marks:

- **Blue** circles the loose piece to pick up.
- **Green** marks the spot where it goes.

Guided mode is the everyday "what next?" answer. The other modes exist so you are never stuck following that order.

### Reverse lookup: where does this go?

You found a piece, or you picked one up because it looked interesting, and you want its home. You point that piece out. On the phone page, pointing will probably mean tapping the piece in the photo, or sending a picture that shows the piece you are holding. The exact gesture can wait until we design the page.

The program answers with the **green** target spot: where that piece belongs in the section.

This is a different question from guided mode. Guided mode picks a spot first (the next cell in reading order) and then hunts for a piece. Reverse lookup starts from the piece in your hand and hunts for the spot.

## Placing a different piece than the one suggested

In guided mode you are allowed to ignore the blue circle and place some other piece. Tell the program you did that. Two things happen:

1. The choice is written to the [feedback log](feedback-loop.md), next to what the program had predicted. The row includes your **order preference**: you chose a different piece than the suggested order. The piece you actually placed is part of that row.
2. The program updates its idea of the board from what you said and, when you have one, from the next photo. The following suggestion starts from the board as it is.

Overriding the order is a normal way to use the helper. Those rows are useful later. If you often reject the same kind of suggestion, that is a clue for tuning the matcher, and eventually for a model.

## A short scene on section A

The frame of section A is done. You turn on guided mode. Green lands on the top-left hole. Blue circles one loose piece.

You would rather place a different piece. You seat that piece and tell the program, "I placed a different piece." The log stores the suggestion, your piece, and the fact that you preferred a different order.

You take a new photo. The top-left hole now looks filled. The next green mark moves one cell to the right.

Another day you turn the helper **off** and finish the rest of the top row yourself. You come back, take one photo, and turn guided mode on. The program sees a finished top row in the picture and offers the first empty cell of the second row. It does not replay the top-row steps it would have suggested yesterday.

Later you pick up a piece from the left-hand pile and ask where it goes. Green marks that piece's home, wherever it falls in the grid.

## What the phone shows

| You asked for | Blue mark | Green mark |
| --- | --- | --- |
| Guided: what is next? | The loose piece to pick up | The next empty spot in reading order |
| Where does this piece go? | The piece you pointed at, so you can see it heard you | That piece's home |
| Off | None | None, until you send a photo and switch mode |

Colors stay stable if we later add a projector or lasers. Those lights repeat the same blue and green. They are optional hardware, after the phone page already works.
