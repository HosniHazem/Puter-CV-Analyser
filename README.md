 # AI Resume Analyzer

 An AI-powered resume analyzer built with React Router v7, TypeScript, Vite, and Tailwind CSS. Users can upload a resume PDF and receive structured, ATS-oriented feedback tailored to a specific job title and description. Storage, AI, and KV operations are integrated via Puter.js.

 
 ## Features
 - **Upload PDF resumes** and auto-convert first page to an image preview
 - **Job-targeted analysis**: provide company, job title, and description for tailored feedback
 - **Structured AI feedback** with scores and actionable tips across ATS, tone/style, content, structure, and skills
 - **Persisted analysis** using Puter KV with retrievable results by ID
 - **Authentication hooks** via Puter auth (sign in/out, user state)
 - **SSR by default** using React Router v7 (SPA mode can be toggled)

 
 ## Tech Stack
 - React 19, React DOM 19
 - React Router v7 (SSR by default)
 - TypeScript, Vite
 - Tailwind CSS v4
 - Zustand for client state
 - pdfjs-dist for PDF processing
 - Puter.js for auth, file system (FS), AI, and key-value (KV) storage

 
 ## Prerequisites
 - Node.js 20+
 - npm 10+
 - Puter.js available in the runtime (window.puter). This typically means:
   - Running in an environment that injects Puter.js (e.g., Puter platform), or
   - Including Puter.js via its official integration (if running standalone). The code expects `window.puter` to exist at runtime.

 
 ## Getting Started (Local Development)
 1. Install dependencies:
    ```bash
    npm ci
    ```
 2. Start the dev server (React Router dev):
    ```bash
    npm run dev
    ```
 3. Open the URL printed in your terminal.

 Notes:
 - The app expects `window.puter` at runtime. If Puter.js is not present, features depending on auth/FS/AI/KV will be disabled and you may see an error like "Puter.js not available".

 
 ## Build and Start (Production)
 - Build the app (SSR build):
   ```bash
   npm run build
   ```
 - Start the production server:
   ```bash
   npm run start
   ```
 - The server honors `PORT` if provided; otherwise it typically defaults to `3000`.

 
 ## Docker
 A multi-stage Dockerfile is provided.
 
 - Build the image:
   ```bash
   docker build -t ai-resume-analyzer .
   ```
 - Run the container (expose port 3000 by default):
   ```bash
   docker run --rm -p 3000:3000 ai-resume-analyzer
   ```
 - If you need a different port:
   ```bash
   docker run --rm -e PORT=8080 -p 8080:8080 ai-resume-analyzer
   ```

 
 ## Project Scripts
 - `npm run dev` — Start React Router dev server
 - `npm run build` — Build for production (SSR)
 - `npm run start` — Serve built app with React Router Serve
 - `npm run typecheck` — Generate types and run `tsc`

 
 ## Configuration and Environment
 - SSR is enabled by default (`react-router.config.ts` sets `ssr: true`). Set to `false` to switch to SPA mode if desired.
 - Puter integration lives in `app/lib/puter.ts` and relies on `window.puter` for:
   - `auth` (signIn, signOut, getUser, isSignedIn)
   - `fs` (read, write, upload, readdir, delete)
   - `ai` (chat, img2txt). Feedback uses `ai.chat` with `model: "claude-3-7-sonnet"`.
   - `kv` (get, set, delete, list, flush)
 - No `.env` variables are required by default in this repository. If your Puter setup requires tokens/keys, configure them according to the Puter documentation.

 
 ## App Structure (high level)
 - `app/`
   - `routes/`
     - `upload.tsx` — Main flow to upload resume, run analysis, and store results
     - `resume.tsx` — Displays feedback by ID
     - `auth.tsx`, `home.tsx`, `wipe.tsx` — Auth hooks, landing, and KV wipe utilities
   - `components/` — Navbar, FileUploader, and UI components
   - `lib/` — `puter.ts` (Puter integration), `pdf2img.ts`, `utils.ts`
   - `root.tsx` — Root layout and app shell
 - `constants/index.ts` — AI response shape and analysis prompt builder
 - `public/` — Static assets (images, sample resumes)
 - `vite.config.ts` — Vite config with Tailwind and React Router plugin
 - `react-router.config.ts` — Router build/runtime configuration

 
 ## How It Works (Overview)
 1. User provides company, job title, and description, then uploads a PDF on `/upload`.
 2. File is uploaded to Puter FS; first page is converted to an image for preview.
 3. A prompt is prepared (`constants/prepareInstructions`) and sent to Puter AI alongside the resume file.
 4. The AI returns structured JSON feedback parsed and stored in Puter KV under `resume:{uuid}`.
 5. User is redirected to `/resume/{uuid}` to view results.

 
 ## Troubleshooting
 - **Puter.js not available**: Ensure you’re running in an environment that provides `window.puter` or include Puter.js per their docs.
 - **PDF fails to convert**: The conversion uses `pdfjs-dist`; ensure the PDF is readable and not encrypted/corrupt. Large PDFs may take longer.
 - **No feedback or JSON parse error**: The AI must return a JSON object matching the `Feedback` interface (see `constants/index.ts`). If the AI returns extra text, ensure prompts are not altered.
 - **Port conflicts**: Set `PORT` to a free port when running `npm run start` or Docker.

 
 ## License
 No license file is included. Please add a license if you plan to distribute.


