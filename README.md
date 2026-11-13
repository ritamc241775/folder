# InsightGen QA Generator

An AI-powered test design workbench. Paste requirements or upload a text, Markdown, CSV, JSON, PDF, or DOCX document. NVIDIA NIM analyzes explicit and implicit behavior, identifies risks and gaps, and generates structured, de-duplicated test cases with CSV and JSON export.

## Quick start (Windows)

1. Install Python 3.10 or newer.
2. Copy `.env.example` to `.env` and add your NVIDIA API key from [NVIDIA Build](https://build.nvidia.com/).
3. Double-click `setup-and-run.ps1`, or run it from PowerShell:

   ```powershell
   Set-ExecutionPolicy -Scope Process Bypass
   .\setup-and-run.ps1
   ```
4. Open http://127.0.0.1:8766.

## Run the React frontend with npm

The optional React/Vite frontend runs separately from the FastAPI backend. Start the backend in one PowerShell terminal:

```powershell
cd C:\Users\RITAM\Desktop\InsightGen-QA-Generator
.\.venv\Scripts\python.exe -m uvicorn backend.app:app --host 127.0.0.1 --port 8766
```

Then start the React frontend in a second terminal:

```powershell
cd C:\Users\RITAM\Desktop\InsightGen-QA-Generator\frontend-react
npm install
npm run dev
```

Open the Vite URL shown in the terminal (normally http://127.0.0.1:5173). Vite proxies `/api` requests to the FastAPI server on port 8766. Keep both terminals running. The original FastAPI-served frontend at port 8766 remains available too.

## Plain HTML/CSS/JS frontend

The no-React version is in `frontend2/` and is served by FastAPI on the same port. Start the backend, then open http://127.0.0.1:8766/frontend2/. No npm command is needed for this version.

The app runs locally. Requirement text and uploaded documents are sent to the configured NVIDIA NIM endpoint to generate cases. Files and generated results are held in memory for the request and are not saved by the app. Never put your API key in the browser or share the `.env` file.

## What it generates

- Positive, negative, boundary, edge, validation, security, error-handling, and data-integrity cases
- API, UI/UX, accessibility, integration, performance, regression, and exploratory coverage where relevant
- Preconditions, steps, realistic test data, expected result, priority, and comments for every case
- Separate assumptions, ambiguities, dependencies, risks, and a coverage summary

The AI is instructed to ground cases in the supplied requirements, label assumptions, avoid duplicates, and not present inferred behavior as a confirmed requirement. Review generated cases with the product and engineering teams before using them as acceptance evidence.

## Configuration

Set `NVIDIA_API_KEY` in `.env`. The default text model is the NVIDIA Nemotron model used by the companion InsightGen project. If you provide screenshot/image files, set `NVIDIA_VISION_MODEL` to an image-capable model enabled for your NVIDIA account; the app uses that model for the request containing images.

| Variable                | Default                                   | Purpose                                                          |
| ----------------------- | ----------------------------------------- | ---------------------------------------------------------------- |
| `NVIDIA_API_KEY`      | —                                        | NVIDIA Build API key                                             |
| `NVIDIA_BASE_URL`     | `https://integrate.api.nvidia.com/v1`   | NVIDIA NIM OpenAI-compatible endpoint                            |
| `NVIDIA_MODEL`        | `nvidia/nemotron-3.5-lightning-30b-a3b` | Text generation model                                            |
| `NVIDIA_VISION_MODEL` | `stepfun-ai/step-5-preview`             | Model used when image inputs are attached; verify account access |
| `APP_HOST`            | `127.0.0.1`                             | Local server bind address                                        |
| `APP_PORT`            | `8765`                                  | Local server port                                                |

The provider and model availability can change. Use the model ID shown in your NVIDIA Build account if a default is unavailable.

## Supported inputs and limits

- Paste text or upload `.txt`, `.md`, `.csv`, `.json`, `.pdf`, `.docx`, `.png`, `.jpg`, or `.webp`.
- Maximum 15 MB per file; up to 5 files per generation; extracted text is capped at 45,000 characters.
- Images are passed to the configured vision model. If that model does not accept images, the API will return an error; no mock test cases are substituted.
- Generated output is not persisted. Export CSV or JSON before closing the page.

## Project layout

```text
backend/app.py       FastAPI endpoints, document extraction, NVIDIA NIM call
frontend/index.html  Single-page generator interface
frontend/app.js      Generation flow, case table, CSV/JSON export
frontend/styles.css  Responsive workbench styling
```
