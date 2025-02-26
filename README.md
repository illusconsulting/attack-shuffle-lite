# (attackShuffle).Lite

**(attackShuffle).Lite** is a browser-based tool that helps security practitioners model a systems' assets, map them to relevant MITRE ATT&CK® techniques, visualize control and capability mappings, and run a lightweight probabilistic “attack analysis.” It uses client-side JavaScript, IndexedDB (via [Dexie.js](https://dexie.org/)), and several local JSON data sets (containing MITRE ATT&CK objects, mappings, and references to compliance frameworks). This tool is heavily influenced from several excellent resources, including [Top ATT&CK Techniques](https://top-attack-techniques.mitre-engenuity.org/#/), [Attack Flow](https://ctid.mitre.org/projects/attack-flow/), [Cyber Defense Matrix](https://cyberdefensematrix.com/), [Mappings Explorer](https://ctid.mitre.org/projects/mappings-explorer), [How to Measure Anything in Cybersecurity Risk](https://hubbardresearch.com/shop/how-to-measure-anything-in-cybersecurity-risk-2e-signed-by-doug-hubbard/), and many others. 

A live demo is hosted at:  
[https://illusconsulting.github.io/attack-shuffle-lite/](https://illusconsulting.github.io/attack-shuffle-lite/)

---

## Table of Contents
1. [Key Features](#key-features)  
2. [Demo & Screenshots](#demo--screenshots)  
3. [Project Structure](#project-structure)  
4. [Getting Started](#getting-started)  
    - [Prerequisites](#prerequisites)  
    - [Installation & Local Setup](#installation--local-setup)  
5. [Usage Guide](#usage-guide)  
    1. [Step 1: Initialize Data](#step-1-initialize-data)  
    2. [Step 2: Define Asset Types](#step-2-define-asset-types)  
    3. [Step 3: ATT&CK Inventory](#step-3-attck-inventory)  
    4. [Step 4: Visualization (Radial Tree)](#step-4-visualization-radial-tree)  
    5. [Step 5: CDM View](#step-5-cdm-view)  
    6. [Step 6: Organization Details](#step-6-organization-details)  
    7. [Step 7: Attack Analysis](#step-7-attack-analysis)  
6. [Data Files](#data-files)  
7. [Technology Stack](#technology-stack)  
8. [Extending & Customizing](#extending--customizing)  
9. [Contributing](#contributing)  
10. [License](#license)  

---

## Key Features

- **Multi-Step Asset Definition**: Walk through a guided form to define your environment (AWS, Azure, GCP, M365), select asset categories (Devices, Networks, Apps & Workloads, Data, Identities), and configure monitoring coverage.
- **IndexedDB Storage**: Uses [Dexie.js](https://dexie.org/) for client-side persistence, keeping all data in your browser’s local database. No server is required.
- **Technique Mappings**: Dynamically loads MITRE ATT&CK techniques and multiple framework mappings (NIST 800-53, CIS Controls, AWS/GCP/Azure/M365 capabilities, etc.).
- **Visualization**: 
  - **Radial Tree** display of how techniques relate to mitigations, controls, or cloud capabilities.  
  - **Cyber Defense Matrix** (CDM)–style view categorizing top techniques and mapped controls.
- **Attack Analysis**: Lightweight simulation that estimates potential impact given your organization’s coverage and security maturity. Outputs a Sankey diagram and risk flow summary.

---

## Demo & Screenshots

A live version is available at:  
[**(attackShuffle).Lite Demo**](https://illusconsulting.github.io/attack-shuffle-lite/)

<details>
<summary>Click to view sample screenshots</summary>

1. **Initialization**  
   Loads JSON data into IndexedDB with progress logs.

2. **Asset Types & Monitoring Coverage**  
   ![Asset Step Screenshot](docs/img/asset-type-step.png)

3. **Techniques Inventory**  
   Summaries for each asset category and environment.

4. **Radial Tree**  
   ![Radial Tree Visualization](docs/img/radial-tree.png)

5. **CDM View**  
   Displays top techniques & relevant controls across five asset categories.

6. **Attack Analysis**  
   Runs repeated simulations to show potential risk flows in a Sankey diagram.

</details>

---

## Project Structure

```
attack-shuffle-lite/
├── index.html           # Main single-page application
├── favicon.ico
├── LICENSE
├── README.md            # You're reading this file
├── static-variables.json
├── weight-keywords.json
├── enterprise-attack.json
├── [multiple mapex JSON files for assets, frameworks, etc.]
└── ... (other script files or config)
```

- **index.html**  
  The core application. Contains all HTML, TailwindCSS styles, and JavaScript logic (using `<script>` blocks). Manages the multi-step form and data flow.

- **JSON data files**  
  Contain static or mapping data:
  - `enterprise-attack.json`: MITRE ATT&CK objects (attack-pattern, relationships, mitigations, etc.).  
  - `static-variables.json`, `weight-keywords.json`, `attack-prev-tid.json`: Additional scoring references and keywords used in the technique calculations.  
  - Various `_attack-.json` files: MAPEX (“mapping exchange”) files linking techniques to particular controls, cloud capabilities, or compliance frameworks.

---

## Getting Started

### Prerequisites

- A modern web browser (Chrome, Firefox, Edge, or Safari).  
- Internet or local server setup to view the site—although you can run it from a file:// URL, certain browsers block `fetch()` calls from file:// by default. For best results, serve it locally via a simple static server or use GitHub Pages.

### Installation & Local Setup

1. **Clone or Download** this repository:
   ```bash
   git clone https://github.com/illusconsulting/attack-shuffle-lite.git
   cd attack-shuffle-lite
   ```

2. **Open `index.html`** in your browser. You can:
   - Double-click (some browsers may restrict local file fetches), OR  
   - Serve locally:
     ```bash
     npx http-server .
     ```
     Then visit `http://localhost:8080` (or whichever port is shown).

3. The web app loads and initializes its data from the bundled JSON files. This may take a few seconds on the first run.

---

## Usage Guide

Once you open the app, you’ll follow a **multi-step wizard**. The steps are shown at the top (“Initialize,” “Asset Types,” “ATT&CK Inventory,” etc.):

### Step 1: Initialize Data
- **What happens**:  
  The app automatically loads all JSON files into your browser’s IndexedDB. You’ll see progress logs indicating how many records were imported.
- **When complete**:  
  A “Next” button appears. Click it to move on.

### Step 2: Define Asset Types
- **Select Asset Category**: Devices, Networks, Applications & Workloads, Data, or Identities.  
- **Select Specific Asset Type**: Based on the chosen category (e.g., “Windows Server OS,” “Linux Server OS,” “Virtual Machine,” etc.).  
- **Choose Environment**: (AWS, Azure, GCP, M365, etc.)  
- **Monitoring Coverage**: Radio buttons for Network, Process, File, Cloud, Hardware coverage (High, Medium, Low, None).  
- **Click “Add Asset Type”**: Saves the record, which appears in the “Current Asset Instances” table below.  
  - For “Data” assets, you’ll also select an estimated record count.

### Step 3: ATT&CK Inventory
- Summaries of ATT&CK techniques mapped to each asset instance. 
- The table shows how many relevant techniques are “abused by” or “targeted by” each asset, plus their average technique score.  
- You can click on a row to view detailed techniques for that asset: technique IDs, names, and computed “attack-score.”

### Step 4: Visualization (Radial Tree)
- Choose what you want to visualize (e.g., “mitigations,” “nist-controls,” “cis-safeguards,” “aws-capabilities,” etc.).  
- A **radial tree** is generated, grouping technique nodes and linking them to the relevant control or mitigation references.

### Step 5: CDM View
- A simplified **Cyber Defense Matrix** layout that organizes assets (rows) and categories (columns) with top 5 techniques and controls.  
- You can select which capability frameworks to show in the top filter checkboxes.

### Step 6: Organization Details
- Checkbox prompts about security practices (e.g., “My organization leverages AI for InfoSec,” “Conducts Incident Response Tests,” etc.).  
- Checking more items can reduce your overall projected breach cost during the final analysis step.

### Step 7: Attack Analysis
- Configure simulation parameters:
  - **Cost of Breach**, **# Iterations**, and “attack effectiveness” vs. “maturity” ranges.  
  - Choose one set of controls/capabilities to emphasize (e.g., “nist-controls” only).  
- **Run Attack Analysis** to simulate possible outcomes.  
- A **Sankey diagram** visualizes risk flows, and a table summarizes potential losses with confidence intervals.

---

## Data Files

Several JSON data files are included to support offline usage:

- **`enterprise-attack.json`**: STIX-like representation of MITRE ATT&CK techniques, relationships, mitigations.  
- **`static-variables.json`, `weight-keywords.json`, `attack-prev-tid.json`**: Provide scoring logic, coverage keywords (e.g., “network” → implies certain technique data sources), or historical “prevalence” references.  
- **`cis_safeguard_to_techniques.json`**: Maps CIS Safeguards to relevant technique IDs.  
- **`[cloud provider].json`** (AWS, GCP, Azure, M365): Maps cloud-specific capabilities to relevant ATT&CK techniques.  
- **`enterprise-assets-[category].json`**: Defines how each asset type can be “abused by” or “targeted by” certain techniques, bridging the gap between high-level assets and the ATT&CK catalog.

All data is fetched on initialization and stored in IndexedDB, so no separate server or database is required.

---

## Technology Stack

- **Frontend**: 
  - **HTML5**, **CSS** (with [Tailwind CSS](https://tailwindcss.com/) for styling)  
  - **JavaScript** (vanilla + [jQuery](https://jquery.com/) for convenience)  
  - [**D3.js**](https://d3js.org/) for visualizations (radial tree & Sankey diagrams)  

- **IndexedDB** for browser-based data storage, accessed via [**Dexie.js**](https://dexie.org/).  
- **JSON** data is fetched and parsed at runtime. No server components are needed.  

---

## Extending & Customizing

1. **Add or Replace Data**  
   - You can swap out `enterprise-attack.json` for a newer version of MITRE ATT&CK, or add custom mapping files.  
   - Update `filesArr` in the script to define which JSONs to import (or remove).  

2. **Customize Scoring**  
   - The logic for calculating “attack-score” appears in the code near the end of `index.html` (look for functions like `computeCoverageWeight` and `computeActionabilityScore`). Tweak them as desired.

3. **Modify Visuals**  
   - The radial tree and Sankey diagrams are created with D3. Adjust layout, color scales, or node labeling in the relevant D3 sections.

4. **Multi-Step Form**  
   - Each step is represented by a `.form-step` block in `index.html`. You can add new steps or remove steps if you only need a subset of the workflow.

5. **Styling**  
   - **TailwindCSS** is loaded from a CDN in `index.html`. You can override classes or add your own styles if you prefer a different look.

---

## Contributing

Contributions are welcome! If you find a bug or have ideas for improvements:

1. **Fork** the repository  
2. **Create** a feature branch (`git checkout -b feature/my-new-feature`)  
3. **Commit** your changes (`git commit -am 'Add new feature'`)  
4. **Push** to your branch (`git push origin feature/my-new-feature`)  
5. **Open a Pull Request** on GitHub

Feel free to open issues for questions or proposals.

---

## License

This project is licensed under the [GNU AFFERO GENERAL PUBLIC LICENSE](LICENSE). See the [LICENSE](LICENSE) file for details.

---

**MITRE ATT&CK®** is a registered trademark of The MITRE Corporation. This project is not affiliated with or endorsed by MITRE.  
