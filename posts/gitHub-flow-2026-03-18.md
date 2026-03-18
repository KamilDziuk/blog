# GitHub Flow 
2026-03-18

**Tags:**  `github-actions` `github` `git-workflow` `github-flow`   

## Introduction

Modern web development is not just about writing code — version control and deployment automation are equally important. One of the most popular workflows used by developers is **GitHub Flow**.

GitHub Flow is a lightweight and simple Git workflow that works especially well in web development projects, particularly when combined with CI/CD practices.

---

## What is GitHub Flow?

GitHub Flow is based on a few simple steps:

1. Create a branch from `main`.
2. Make changes.
3. Open a Pull Request.
4. Review the code.
5. Merge into `main`.
6. Deploy the application.

This workflow is commonly used in:

- Frontend projects (React, Vue, Next.js)
- Backend systems (Node.js APIs)
- Full-stack applications

---

## Advantages of GitHub Flow

- Simplicity and clarity
- Fast deployment cycles
- Strong CI/CD integration
- Ideal for team collaboration

---

## GitHub Flow + CI/CD

In practice, GitHub Flow is often combined with **GitHub Actions**.

This allows developers to:

- Automatically run tests on push
- Automatically validate Pull Requests
- Run builds and lint checks
- Deploy the application automatically

---

## Example: GitHub Actions for Frontend & Backend

* Sources – [ci.yml](https://github.com/KamilDziuk/djMatthew/blob/main/.github/workflow/ci.yml)


The following workflow demonstrates:

- Building the frontend
- Linting the frontend code
- Installing backend dependencies
- Running on push and pull requests

```yaml
name: Frontend & Backend Build

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "lts/*"

      - name: Install frontend without devDependencies
        working-directory: client
        run: npm install --omit=dev

      - name: Build frontend
        working-directory: client
        run: npm run build

      - name: Lint frontend
        working-directory: client
        run: npm run lint

      - name: Install backend without devDependencies
        working-directory: server
        run: npm install --omit=dev
````

---

## What Does This Workflow Do?

### 1. Trigger

The workflow runs on:

* Push to `main`
* Pull request targeting `main`

### 2. Environment

```yaml
runs-on: ubuntu-latest
```

Runs the workflow on a Linux environment.

### 3. Repository Checkout

```yaml
uses: actions/checkout@v4
```

Fetches the repository code to the runner.

### 4. Node.js Setup

```yaml
uses: actions/setup-node@v4
```

Installs the Node.js runtime for both frontend and backend.

### 5. Frontend

* Installs dependencies (without devDependencies for production optimization)
* Builds the frontend application
* Runs linting to ensure code quality

### 6. Backend

* Installs backend dependencies (without devDependencies)
* Prepares backend for testing or deployment

---

## Why Is This Important?

This workflow:

* Automatically validates code quality
* Detects errors before merging
* Ensures project consistency
* Speeds up development and delivery

---

## GitHub Flow Best Practices

* Keep pull requests small and frequent
* Require code reviews
* Use automated tests for validation
* Deploy only after merge into `main`

---

## My Practical Experience

In my projects, I use a combination of platforms:

* For **backend projects**, I mostly use **Render**. It allows fast deployment of Node.js, Python, and containerized apps, with built-in database support and automatic GitHub deployment.
* For **frontend projects**, I mainly use **Vercel**. It is optimized for modern frontend frameworks (Next.js, React, Vue), provides global CDN, and automatic preview deployments for pull requests.

This combination helps separate frontend and backend workflows and choose the platform best suited for the project type.

---

## Conclusion

GitHub Flow combined with GitHub Actions is a powerful tool for managing web development projects. It enables fast feature delivery, automated validation, and high code quality, making it ideal for modern frontend, backend, and full-stack applications.

---

## Sources

* [GitHub Actions](https://docs.github.com/actions)
* [Atlassian Git Workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)


