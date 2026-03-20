# Car Number Plate Extraction System (ANPR)

This project implements a complete number plate recognition pipeline in three steps:

1. **Detection**: locate the license plate region in each camera frame (contour-based rectangle detection).
2. **Alignment**: apply a perspective transform to “straighten” the detected plate.
3. **OCR**: extract characters from the aligned plate using **Tesseract OCR**.

After OCR, the system:

4. **Validates** the plate format using a configurable regular expression.
5. **Confirms** the plate after multiple observations (majority voting across frames).
6. **Saves** confirmed plates to `data/plates.csv`.
7. Saves debug screenshots to `screenshots/` (`detection.png`, `alignment.png`, `ocr.png`).

## Folder Structure

- `src/detect.py` - detect a plate region (returns 4 corner points)
- `src/align.py` - perspective correction using 4 corner points
- `src/ocr.py` - preprocess + Tesseract OCR
- `src/main.py` - camera loop + confirmation + CSV output

## Installation

1. Install Python 3.X.X
2. Install dependencies:

```bash
cd anpr-project
python3 -m pip install -r requirements.txt
```

3. Install Tesseract (if not already installed):

```bash
brew install tesseract
```

On macOS, `pytesseract` typically finds the `tesseract` binary automatically. If not, set:

```bash
export TESSERACT_CMD=/opt/homebrew/bin/tesseract
```

## Running

```bash
cd anpr-project
python3 src/main.py
```

Common options:

```bash
python3 src/main.py --camera 0 --buffer-size 5 --min-confirmations 3 --cooldown 10
python3 src/main.py --image "/path/to/plate_photo.png"
python3 src/main.py --camera 0 --tesseract-cmd "/usr/local/bin/tesseract"
```

Screenshots are written to `screenshots/` when a plate is confirmed.

## Plate Format Validation

By default, plates are validated with a generic regex:

`^[A-Z]{3}[0-9]{3}[A-Z]$` (7 characters total, e.g. `ABC123D`)

This format is currently hardcoded in `src/pipeline.py`.

## Real-Vehicle Testing (Required)

When testing on real vehicles:

1. Ask the vehicle owner for permission (explain it is a school project).
2. Use only the license plate region for testing.
3. A laptop webcam is acceptable.

Test on multiple vehicles to demonstrate:

- plate detection
- plate alignment
- OCR result
- final confirmed plate (saved to CSV)

## Screenshots


Run the program and wait until a plate is confirmed. The project writes:

- `screenshots/<run-folder>/detection.png` - frame with detected plate outline
- `screenshots/<run-folder>/alignment.png` - perspective-corrected plate
- `screenshots/<run-folder>/ocr.png` - OCR visualization (OCR stage frame)
- `screenshots/<run-folder>/ocr_threshold.png` - thresholded plate image used by Tesseract

