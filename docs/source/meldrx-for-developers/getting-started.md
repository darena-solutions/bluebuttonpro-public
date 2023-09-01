# App Quick Start

To help you start building apps immediately, we provide several tools and templates to help you get started.

## App Builder: `create-meldrx-app`

`create-meldrx-app` is a CLI tool that can bootstrap a MeldRx application quickly and easily.
This tool will ask you a few questions about what kind of app you want to build and will then generate a new MeldRx app that you can run immediately.

### Prerequisites

Before getting started, please make sure you have the following:
- A MeldRx Developer account (sign up at [https://meldrx.com](https://meldrx.com))
- A MeldRx app. For guidance, see [MeldRx Apps](meldrx-app).
- A MeldRx workspace. For guidance, see [MeldRx Workspaces](meldrx-workspace).

### Installation
```bash
npm i -g create-meldrx-app
```

### Using the Tool
```bash
npx create-meldrx-app [app-directory] [meldrx-app-id] [workspace-id]
```

After running the tool, you will be asked a few questions about the type of app you want to build (Backend, Patient, Clinician) and the platform you are targeting (Web, Mobile, Backend, etc.).

Once these questions have been answered, a new app will be bootstrapped for you that will connect to your MeldRx Workspace.

## App Templates

You can also download one of our app templates and modify it as needed.

### Template: Patient Sphere (NextJs)
- Description: NextJs app showing patient clinical data. Also provides a tool to make FHIR queries for testing.
- Template: [https://github.com/darena-solutions/meldrx-template-app-patient-viewer-nextjs](https://github.com/darena-solutions/meldrx-template-app-patient-viewer-nextjs)
- App User Type: `Patient`, `Provider`
- Live Demo: [Patient Sphere](https://ambitious-mud-04db85510.3.azurestaticapps.net/)

### Template: Blank (NextJs)
- Description: Blank NextJs app that can be used to get started as a patient- or provider-facing app
- Template: [https://github.com/darena-solutions/meldrx-template-app-blank-nextjs](https://github.com/darena-solutions/meldrx-template-app-blank-nextjs)
- App User Type: `Patient`, `Provider`

