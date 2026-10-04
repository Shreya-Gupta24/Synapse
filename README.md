<div align="center">

<br />

# 🧠 Synapse

### **Design in real time. Execute in cloud browsers. Replay every run.**

*A full-stack SaaS platform for building, running, and collaborating on AI-powered browser automations — visually.*

<br />

[![Next.js](https://img.shields.io/badge/Next.js_16-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)](https://nextjs.org)
[![React](https://img.shields.io/badge/React_19-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://typescriptlang.org)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)](https://tailwindcss.com)

<br />

[![Browserbase](https://img.shields.io/badge/Browserbase-0A0A0A?style=for-the-badge)](https://browserbase.com)
[![Trigger.dev](https://img.shields.io/badge/Trigger.dev-635BFF?style=for-the-badge)](https://trigger.dev)
[![Liveblocks](https://img.shields.io/badge/Liveblocks-111111?style=for-the-badge)](https://liveblocks.io)
[![Neon](https://img.shields.io/badge/Neon_Postgres-00E599?style=for-the-badge&logo=neon&logoColor=black)](https://neon.tech)
[![Clerk](https://img.shields.io/badge/Clerk-6C47FF?style=for-the-badge&logo=clerk&logoColor=white)](https://clerk.com)
[![Sentry](https://img.shields.io/badge/Sentry-362D59?style=for-the-badge&logo=sentry&logoColor=white)](https://sentry.io)
[![Railway](https://img.shields.io/badge/Railway-0B0D0E?style=for-the-badge&logo=railway&logoColor=white)](https://railway.app)

<br />

[Features](#-features) &nbsp;·&nbsp; [Architecture](#-architecture) &nbsp;·&nbsp; [Workflow Nodes](#-workflow-nodes) &nbsp;·&nbsp; [Getting Started](#-getting-started) &nbsp;·&nbsp; [Deploy](#-deploy-on-railway) &nbsp;·&nbsp; [Stack](#-tech-stack)

</div>

---

## 💡 What is Synapse?

Synapse is a **collaborative, no-code browser automation SaaS** that lets teams visually build automation workflows on a shared canvas, execute them inside real cloud browsers using AI, and watch every step run in real time.

```
 User types an instruction  →  AI understands it  →  Real browser executes it  →  You watch it live
```

Think of it as a **visual programming environment for the web** — where each node is a browser action, and the canvas is your automation logic.

---

## ✨ Features

| | Feature | Description |
|---|---|---|
| 🎨 | **Visual Workflow Canvas** | Drag-and-drop React Flow nodes to compose automations without writing scripts |
| 🤝 | **Real-Time Collaboration** | Multiple users edit the same canvas simultaneously with live cursors and presence |
| 🤖 | **AI Browser Control** | Natural-language instructions power real browser actions via Stagehand |
| 🔗 | **Data Passthrough** | Chain nodes with `{{ nodeId.field }}` expressions to pass outputs downstream |
| ⚡ | **Durable Execution** | Long-running jobs with automatic retries, cancellation, and fault tolerance |
| 📺 | **Live Run Console** | Watch each node's status, timing, and output stream in real time |
| 🎬 | **Session Replay** | Rewind and replay the full browser recording after every run |
| 🏢 | **Multi-Tenant Workspaces** | Every organization gets its own isolated workflows and collaboration rooms |
| 💳 | **SaaS Billing** | Clerk Billing gates Pro features like Agent nodes and session replay |
| 🔍 | **Error Monitoring** | Sentry captures frontend, server, edge, and background task errors |

---

## 🏗️ Architecture

### System Overview

```mermaid
flowchart TD
    subgraph Client["🖥️  Client (Next.js 19 / React 19)"]
        A[React Flow Canvas]
        B[Live Run Console]
        C[Session Replay Viewer]
    end

    subgraph Sync["🔄  Real-Time Sync"]
        D[Liveblocks Room\nNodes · Edges · Cursors · Presence]
    end

    subgraph Backend["🗄️  Backend (Next.js API Routes)"]
        E[Server Actions\nSave · Execute · Cancel]
        F[Liveblocks Auth]
        G[Replay Proxy]
    end

    subgraph DB["💾  Database"]
        H[(Neon Postgres\nDrizzle ORM)]
    end

    subgraph Jobs["⚙️  Background Jobs"]
        I[Trigger.dev Task Runner]
        J[Stagehand Agent]
        K[Browserbase Cloud Browser]
    end

    subgraph Infra["📦  Infrastructure"]
        L[Clerk Auth + Billing]
        M[Resend Email]
        N[Sentry Monitoring]
    end

    A <-->|live sync| D
    A -->|save snapshot| E
    E --> H
    E --> I
    I -->|AI instructions| J
    J -->|controls| K
    I -->|step events| B
    K -->|recording| G
    G --> C
    Backend --- Infra
```

### Execution Flow

```mermaid
sequenceDiagram
    participant U as 👤 User
    participant C as 🖥️ Canvas
    participant S as ⚙️ Server Action
    participant T as 🔁 Trigger.dev
    participant AI as 🤖 Stagehand
    participant BB as 🌐 Browserbase

    U->>C: Click "Run Workflow"
    C->>S: Validate graph + save snapshot
    S->>T: Enqueue workflow task
    T->>T: Topological sort nodes
    loop For each node (in order)
        T->>AI: Execute node instruction
        AI->>BB: Open / navigate / act / extract
        BB-->>AI: Result
        AI-->>T: Node output
        T-->>C: Stream live status + output
    end
    BB-->>C: Session recording available
    U->>C: Click "Replay" to review run
```

### Data Flow Between Nodes

```mermaid
flowchart LR
    N1["🔵 Start"]
    N2["🌐 Open URL\noutput: title, url"]
    N3["🤖 Extract\ninput: '{{n2.title}}'\noutput: extraction"]
    N4["📧 Send Email\nbody: '{{n3.extraction}}'"]

    N1 --> N2
    N2 --> N3
    N3 --> N4
```

Outputs from any node are stored by node ID and injected into downstream inputs using `{{ nodeId.field }}` template expressions — enabling complex multi-step data pipelines with no code.

---

## 🧩 Workflow Nodes

```mermaid
mindmap
  root((Synapse Nodes))
    🔵 Start
      Triggers the workflow
    🌐 Open URL
      Navigates browser to a URL
      Outputs title and url
    🎯 Act
      Natural-language browser action
      Click, type, scroll, submit
    🔎 Extract
      Pull structured data from pages
      Returns extracted content
    👁️ Observe
      Find elements and possible actions
      Returns selector and description
    🦾 Agent
      Autonomous multi-step AI task
      Pro plan required
    📧 Send Email
      HTML email via Resend
      Returns email ID
```

| Node | What it does | Key Outputs |
|------|-------------|-------------|
| **Start** | Entry point — triggers the workflow | — |
| **Open URL** | Navigates the cloud browser to any URL | `url`, `title` |
| **Act** | Executes a natural-language action on the page | `success`, `message`, `url` |
| **Extract** | Pulls structured content from the page via AI | `extraction` |
| **Observe** | Identifies elements and actions available on screen | `selector`, `description`, `matches` |
| **Agent** | Runs a fully autonomous multi-step browser task *(Pro)* | `success`, `completed`, `message` |
| **Send Email** | Sends a rich HTML email via Resend | `emailId` |

---

## 🚀 Getting Started

### Prerequisites

- Node.js 18+ and npm
- [Clerk](https://clerk.com) — Auth + Organizations + Billing
- [Neon](https://neon.tech) — Serverless Postgres
- [Trigger.dev](https://trigger.dev) — Background job runner
- [Liveblocks](https://liveblocks.io) — Real-time collaboration
- [Browserbase](https://browserbase.com) — Managed cloud browsers
- [Resend](https://resend.com) — Transactional email
- [Sentry](https://sentry.io) *(optional)* — Error monitoring

### 1. Clone and install

```bash
git clone <your-repo-url>
cd synapse
npm install
```

### 2. Configure environment

```bash
cp .env.example .env.local
```

```bash
# Clerk
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_SIGN_IN_FALLBACK_REDIRECT_URL=/
NEXT_PUBLIC_CLERK_SIGN_UP_FALLBACK_REDIRECT_URL=/
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=

# Neon Postgres
NEON_BRANCH=main
DATABASE_URL=
DATABASE_URL_UNPOOLED=

# Trigger.dev
TRIGGER_SECRET_KEY=

# Liveblocks
NEXT_PUBLIC_LIVEBLOCKS_PUBLIC_KEY=
LIVEBLOCKS_SECRET_KEY=

# Browserbase + Resend
BROWSERBASE_API_KEY=
RESEND_API_KEY=

# Sentry (optional)
NEXT_PUBLIC_SENTRY_DSN=
SENTRY_DSN=
SENTRY_AUTH_TOKEN=
```

### 3. Configure Clerk

Enable **Organizations** in your Clerk dashboard — every workflow is scoped to an organization. For Pro features (Agent node, session replay), create a Clerk Billing plan with the slug `pro`.

### 4. Set up the database

```bash
npm run db:generate   # generate Drizzle migrations
npm run db:migrate    # apply to Neon
```

### 5. Start the Trigger.dev worker

```bash
npx trigger.dev dev   # run in a separate terminal
```

### 6. Run the app

```bash
npm run dev
```

Visit [http://localhost:3000](http://localhost:3000), sign in, create an organization, and build your first automation. 🎉

---

## 🚢 Deploy on Railway

```mermaid
flowchart LR
    GH[GitHub Repo] -->|auto-deploy| RW[Railway\nNext.js App]
    RW --> NEON[(Neon Postgres)]
    RW --> TR[Trigger.dev\nBackground Worker]
    RW --> BB[Browserbase\nCloud Browsers]
    RW --> LB[Liveblocks\nReal-time Sync]
```

### Steps

| Step | Action |
|------|--------|
| 1 | Push repo to GitHub and create a Railway project |
| 2 | Choose **Deploy from GitHub repo** — Railpack auto-detects Node.js |
| 3 | Copy all `.env.local` values into Railway service variables |
| 4 | Run `npm run db:migrate` against the production database |
| 5 | Run `npx trigger.dev deploy` to register the workflow task |
| 6 | Add your Railway domain to Clerk's allowed origins |

> **Build:** `npm run build` &nbsp;|&nbsp; **Start:** `npm start`

---

## 📁 Project Structure

```text
synapse/
│
├── app/
│   ├── (auth)/              # Sign-in, sign-up, org selection (Clerk)
│   ├── (dashboard)/         # Workflow dashboard, editor, billing
│   └── api/
│       ├── liveblocks/      # Liveblocks auth + user resolution
│       └── replays/         # Browserbase session recording proxy
│
├── components/
│   ├── app-sidebar.tsx      # Organization and workflow navigation
│   └── ui/                  # Shared UI primitives (shadcn/ui)
│
├── features/
│   └── workflows/
│       ├── components/      # Canvas, toolbar, inspector, console, replay
│       ├── hooks/           # Billing plan + graph connection hooks
│       ├── lib/             # Validation, interpolation, graph utilities
│       ├── nodes/           # Node registry + executor implementations
│       ├── tasks/           # Trigger.dev workflow task definitions
│       ├── actions.ts       # Server actions: save, run, cancel
│       └── data.ts          # Organization-scoped DB queries
│
└── lib/
    ├── db/                  # Drizzle schema, Neon client, migrations
    ├── browserbase.ts       # Browserbase SDK client
    ├── liveblocks.ts        # Liveblocks server client
    └── resend.ts            # Resend email client
```

---

## 🛠️ Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start Next.js dev server |
| `npm run build` | Production build |
| `npm start` | Start production server |
| `npm run lint` | Run ESLint |
| `npm run format` | Prettier format (TS/TSX) |
| `npm run typecheck` | TypeScript type check |
| `npm run db:generate` | Generate Drizzle migrations |
| `npm run db:migrate` | Apply migrations to Neon |
| `npm run db:push` | Push schema directly (prototyping) |
| `npm run db:studio` | Open Drizzle Studio |

---

## 🧰 Tech Stack

```mermaid
graph TD
    subgraph Frontend["🖥️ Frontend"]
        A["Next.js 16\n(App Router)"]
        B["React 19"]
        C["React Flow\n(visual canvas)"]
        D["Tailwind CSS v4"]
        E["shadcn/ui"]
    end

    subgraph Realtime["🔄 Real-Time"]
        F["Liveblocks\n(cursors · presence · state)"]
    end

    subgraph Auth["🔐 Auth & Billing"]
        G["Clerk\n(auth · orgs · billing)"]
    end

    subgraph Jobs["⚙️ Jobs"]
        H["Trigger.dev\n(durable tasks · retries)"]
        I["Stagehand\n(AI browser control)"]
        J["Browserbase\n(cloud browsers · replay)"]
    end

    subgraph Data["💾 Data"]
        K["Neon\n(serverless Postgres)"]
        L["Drizzle ORM"]
    end

    subgraph Comms["📬 Comms & Ops"]
        M["Resend\n(email)"]
        N["Sentry\n(monitoring)"]
        O["Railway\n(hosting)"]
    end

    A --> B --> C
    A --> F
    A --> G
    A --> H
    H --> I --> J
    A --> K
    K --> L
```

| Layer | Technology | Role |
|-------|-----------|------|
| **Framework** | Next.js 16 + React 19 | Full-stack app with App Router and Server Actions |
| **Canvas** | React Flow | Visual drag-and-drop workflow editor |
| **Collaboration** | Liveblocks | Shared canvas state, live cursors, and user presence |
| **Jobs** | Trigger.dev | Durable background execution with retries and cancellation |
| **AI Automation** | Stagehand | Natural-language browser control and autonomous agents |
| **Browser Runtime** | Browserbase | Managed cloud browsers, model gateway, session recordings |
| **Auth** | Clerk | Authentication, organizations, and subscription gating |
| **Database** | Neon + Drizzle | Serverless Postgres with type-safe queries |
| **Email** | Resend | Transactional email from workflow nodes |
| **Monitoring** | Sentry | Full-stack error and performance tracking |
| **Hosting** | Railway | Zero-config deployment from GitHub |

---

<div align="center">

**Built with ❤️ using Next.js, Stagehand, Trigger.dev, and Liveblocks**

</div>
