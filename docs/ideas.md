# Ideas to look at later

A short list of tools and approaches. One line each on why they are here. Choosing among them is part of the later design, after the book and the first photo experiment.

- **OpenCV** — finds pieces and the frame in a photo: load the image, threshold it so the pieces stand out from the grey board, and detect contours.
- **NumPy** — holds the grids of numbers OpenCV uses, and is how we will measure a piece's outline.
- **Pillow** — opens, draws on, and saves images with less machinery than OpenCV. The book teaches it, so it is the gentle on-ramp.
- **Jupyter notebooks** — run one step, look at the picture, then run the next. Good for learning. The lasting program can be ordinary Python files in `src/`.
- **Flask** — a small Python web page the phone can open on the home Wi-Fi. A reasonable first choice because a single page is only a few new ideas.
- **FastAPI** — the other web option, comfortable if we later want the phone and the vision code to talk through a clearer API.
- **CSV** — the first feedback log: one row per correction, openable in a spreadsheet, and covered in the book.
- **SQLite** — a single database file for the same corrections once there are enough rows to ask questions. The 3rd edition of the book has a chapter on it.
- **Phone on the same Wi-Fi** — the camera and the screen for normal use. You snap a still when you want an update, because the puzzle may be out of webcam reach, and the page draws blue and green on that photo.
- **Raspberry Pi and a camera module** — a possible later home for the finished program, sitting next to the puzzle table, if we ever want a machine that stays there.
- **Arduino and servos** — an optional way to aim laser pointers so they copy the blue and green marks. USB serial from the VMware guest uses the Removable Devices menu.
- **A small projector** — an optional way to shine those same marks on the board, with less moving hardware than steered lasers.
- **A live webcam or video stream** — a later upgrade that watches continuously. The first versions wait for a photo you chose to take.
- **Machine learning** — a later way to improve identification using the correction rows (photo, guess, confidence, what you said). Rule-tuning comes first, while the log is still small.

## The awkward fact about the pieces

Every piece back in a section shows the same letter, repeated. The letter tells you the section ("this pile is A") and then stops being helpful. Matching one loose piece to one hole has to use the **shape** of each side (tab, blank, or flat edge) and the piece's **place** relative to the frame and the pieces already down. The feedback log exists because that shape match will be wrong sometimes, and the misses are the data we keep.
