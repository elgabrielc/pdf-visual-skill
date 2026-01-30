---
name: pdf-visual
description: Visually inspect a PDF file when text parsing fails or returns garbled content. Use this when WebFetch or other tools fail to extract readable text from a PDF.
argument-hint: <pdf-url-or-path> [prompt]
---

# PDF Visual Inspection

When a PDF cannot be parsed properly (WebFetch returns garbled text, empty content, or parsing errors), this skill downloads the PDF and reads it visually.

## Process

1. **Determine the PDF source:**
   - If `$0` is a URL: download it to the scratchpad directory
   - If `$0` is a local path: use it directly

2. **Download (if URL):**
   ```bash
   curl -sL -w "%{http_code}" "$0" -o "<scratchpad>/pdf-visual-<timestamp>.pdf"
   ```

3. **Validate the download:**
   - Check the HTTP status code (must be 200)
   - Verify the file exists and has content: `ls -la <file>`
   - Check the file type: `file <downloaded-file>`
   - If the file type is NOT "PDF document", the download failed (likely got an HTML error page)
   - If validation fails, report the error to the user and suggest alternatives

4. **Read visually:**
   - Use the Read tool on the PDF file
   - Claude can read PDFs visually as a multimodal LLM

5. **Extract information:**
   - If `$1` (prompt) is provided: focus on answering that specific question
   - Otherwise: provide a comprehensive summary of the PDF contents

6. **Report findings:**
   - Summarize the key information extracted
   - Note any sections that were unclear or unreadable
   - Provide page references where relevant

7. **Cleanup:**
   - After successfully reading and reporting, delete the temporary PDF file:
     ```bash
     rm "<scratchpad>/pdf-visual-<timestamp>.pdf"
     ```
   - Do NOT delete if the source was a local path (user's file)
   - Do NOT delete if there was an error (user may want to inspect the file)

## Error Handling

If download or validation fails:
- Report the specific error (404, 403, invalid file type, etc.)
- If HTTP error: suggest the user verify the URL is correct
- If file type is HTML instead of PDF: the server returned an error page
- If the PDF is corrupted or unreadable: report this to the user
- Keep the downloaded file for user inspection when errors occur

## Usage Examples

```
/pdf-visual https://example.com/document.pdf
/pdf-visual https://example.com/manual.pdf "What are the measurement tool features?"
/pdf-visual /path/to/local/file.pdf
/pdf-visual /path/to/local/file.pdf "Extract the installation instructions"
```

## Notes

- Downloaded PDFs are stored temporarily in the session scratchpad directory
- The Read tool handles PDF rendering automatically
- For very large PDFs, focus on specific sections if a prompt is provided
- Always validate downloads before attempting to read
- Temporary files are automatically cleaned up after successful reads
