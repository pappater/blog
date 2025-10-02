# Visualizing and Acting on NPM Audit Reports

## Introduction

Managing JavaScript project dependencies is critical for security and maintainability. The `npm audit` tool provides a list of vulnerabilities in your dependencies, but its output can be overwhelming and difficult to interpret, especially for large projects. To address this, I built a toolchain that not only parses and visualizes audit results, but also helps you identify the most actionable upgrades in your dependency tree.

## Problem Statement

- `npm audit` produces a JSON report of vulnerabilities, but does not show how vulnerable packages are connected in your dependency tree.
- It is often unclear which parent dependencies are responsible for introducing vulnerabilities.
- Manual analysis is time-consuming and error-prone.

## Solution Overview

This project consists of two main parts:

1. **A Node.js script (`find-vulnerable-parents.js`)**
   - Consumes `npm-audit.json` and the output of `npm ls --all --json`.
   - Identifies all vulnerable packages and traces their parent chains up to the root.
   - Outputs a CSV file summarizing each vulnerability, its version, parent chain, severity, and advisory URL.

2. **A React-based UI**
   - Allows users to drag and drop both `npm-audit.json` and `npm-ls.json` files.
   - Visualizes the vulnerabilities and their parent chains in a sortable table.
   - Provides CSV export functionality for further analysis or reporting.
   - Optionally, generates an LLM-powered summary of upgrade recommendations.

## How It Works

### 1. Generate the Required Files

Run the following commands in your project root:

```bash
npm audit --json > npm-audit.json
npm ls --all --json > npm-ls.json
```

### 2. Analyze with the Script

Run the provided Node.js script:

```bash
node scripts/find-vulnerable-parents.js
```

This will generate a `vulnerable-report.csv` file with detailed information about each vulnerability and its parent chain.

### 3. Visualize in the UI

- Open the React app (`audit-report-ui`).
- Drag and drop both `npm-audit.json` and `npm-ls.json` onto the drop area.
- The app will display a table of vulnerabilities, including package name, version, parent chain, severity, and advisory URL.
- You can export the table as a CSV file for sharing or further processing.
- Optionally, enter your OpenAI API key to generate a summary of recommended upgrades using an LLM.

## Features

- **Parent Chain Tracing:** See exactly how each vulnerable package is introduced into your project.
- **CSV Export:** Download the actionable report for use in CI/CD or documentation.
- **LLM Summary (Optional):** Get a human-readable summary of upgrade actions.
- **Modern UI:** Clean, responsive React interface with drag-and-drop support.

## Benefits

- Quickly identify the most impactful upgrades to reduce your project's vulnerability surface.
- Save time compared to manual analysis of audit reports.
- Improve communication with stakeholders by providing clear, actionable reports.

## Conclusion

This toolchain bridges the gap between raw audit data and actionable insights. By visualizing the parent chains of vulnerable packages and providing exportable reports, it empowers developers to make informed decisions about dependency upgrades and project security. 

---

*Repository: [deps-vuln](https://github.com/pappater/deps-vuln)*
