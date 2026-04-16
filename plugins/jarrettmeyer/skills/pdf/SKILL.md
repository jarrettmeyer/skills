---
name: pdf
description: >-
  Read and process PDF files. Use this skill whenever the user asks to read, summarize,
  extract text from, analyze, or convert a PDF file — even if the request seems simple.
  The Read tool's PDF support is unreliable on this platform (false size errors, empty
  content), so this skill provides a fallback chain that actually works. Trigger on any
  mention of PDF files, even if the user just says "read this" and the path ends in .pdf.
allowed-tools: Bash, Read, Write
disable-model-invocation: false
---

# PDF Reader

The Read tool frequently fails on PDFs in this environment — false "exceeds 20 MB" errors
on small files and empty content returns. Do not stop at the first failure. Work through
the fallback chain until you have usable text.

## Prerequisites

The `pdfinfo`, `pdftotext`, and `pdftoppm` commands come from the **poppler** package.

```bash
brew install poppler
```

## Step 1: Get PDF Metadata

Before extracting text, check what you're dealing with:

```bash
pdfinfo "<pdf-path>"
```

This gives you page count, file size, encryption status, and format info. Use it to set
expectations for the user (e.g., "This is a 15-page document").

## Step 2: Extract Text

Try each method in order. Stop at the first one that produces usable text.

### Method A: pdftotext

The most reliable method for text-based PDFs.

```bash
pdftotext "<pdf-path>" -
```

This prints extracted text to stdout. If the document has tables or columns, try with the
`-layout` flag to preserve spatial formatting:

```bash
pdftotext -layout "<pdf-path>" -
```

### Method B: pandoc (better for structured Markdown)

If you need Markdown output with headings, lists, and structure preserved:

```bash
pandoc "<pdf-path>" -t markdown
```

Pandoc uses pdftotext internally but produces better-structured output. Prefer this when
the user wants a Markdown conversion.

### Method C: Read tool

Try the built-in Read tool on the PDF path. This sometimes works but frequently fails on
this platform. Only use as a fallback if the CLI tools above fail.

### Method D: OCR (scanned / image-based PDFs)

If pdftotext returns empty or near-empty output, the PDF likely contains scanned images
rather than text. Use tesseract for OCR:

```bash
tmpdir=$(mktemp -d)
pdftoppm "<pdf-path>" "$tmpdir/page" -png
for img in "$tmpdir"/page-*.png; do
  tesseract "$img" - 2>/dev/null
done
rm -rf "$tmpdir"
```

### Method E: Report failure

If all methods fail, tell the user clearly: "I couldn't extract text from this PDF. The
file may be encrypted, corrupted, or in an unusual format." Suggest they try opening it
in a PDF viewer and copying the text manually.

## Step 3: Do What the User Asked

Once you have the extracted text:

- **Summarize / analyze / answer questions**: Work with the text directly in conversation
- **Convert to Markdown**: Use `pandoc "<pdf-path>" -t markdown` for the best structure.
  Write the `.md` file next to the PDF with the same base name, unless the user specifies
  a different path
- **No specific ask beyond "read this"**: Present a brief summary of the document and ask
  what they'd like to do with it
