# /split-pdf

Acquire, split, and take structured notes on an academic PDF.

## Before starting
1. Load the full spec from `split-pdf/spec.md`
2. Load tag vocabulary from `split-pdf/tag_reference.md`
   - If not found at $READINGS_ROOT/tag_reference.md: STOP and notify user
3. Confirm PyPDF2 is available

## Usage
Provide either:
- A URL to a PDF
- A local file path

Then follow the steps in `split-pdf/spec.md` exactly.