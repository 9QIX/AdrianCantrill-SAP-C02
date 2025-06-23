# Accessing Cantrill.io SAP-C02 Course Resources

This guide explains how to access and keep up-to-date with the **companion resources** for the Cantrill.io AWS SAP-C02 course. The resources include **study notes, lesson files, scripts**, and **demo bundles**, all hosted in a GitHub repository. Instructions are provided for **Windows**, **macOS**, and **Ubuntu Linux** users.

## Overview

- Course resources are stored in a **GitHub repository**.
- You can access them in two ways:

  - **Download ZIP**: Quick, static snapshot.
  - **Git Clone**: Recommended for syncing updates.

- Requires installing [Git](https://git-scm.com) and optionally [Visual Studio Code](https://code.visualstudio.com) for editing.

## Windows Setup

### 1. Download the GitHub Repository

**Option 1 (Quick & Static)**:

- Go to the GitHub course repo.
- Click `Clone or Download` → `Download ZIP`.
- Extract the files locally.

> This does **not** sync updates.

**Option 2 (Recommended – Git Clone)**:

- Install Git from [git-scm.com](https://git-scm.com).
- Follow installation steps (click Next/Install with default settings).
- Confirm installation via `git --version` in Command Prompt.

### 2. Clone the Repository via Command Line

#### Commands:

```bash
cd %USERPROFILE%\Documents
mkdir repos
cd repos
git clone https://github.com/YOUR_COURSE_REPO_URL.git
```

**Explanation:**

```bash
cd %USERPROFILE%\Documents     # Change to the Documents folder
mkdir repos                    # Make a new folder named 'repos'
cd repos                       # Enter the 'repos' directory
git clone <repo-url>          # Clone the repository to your local machine
```

### 3. Update the Repository

To pull the latest changes:

```bash
cd <repo-folder>
git pull
```

> This ensures your demo files and scripts remain up-to-date with course revisions.

## macOS Setup

### 1. Open Terminal and Navigate

```bash
cd ~
mkdir repos
cd repos
```

### 2. Install Git (if not installed)

Run any `git` command:

```bash
git
```

macOS will prompt you to install **Command Line Developer Tools**. Click **Install** and accept the license.

### 3. Clone the Repository

Follow same steps as Windows:

```bash
git clone https://github.com/YOUR_COURSE_REPO_URL.git
```

### 4. Update the Repository

Navigate into the repo folder and run:

```bash
git pull
```

## Ubuntu Linux Setup

### 1. Create Folder and Navigate

```bash
cd ~
mkdir repos
cd repos
```

### 2. Install Git (if not already)

```bash
sudo apt update
sudo apt install git
```

### 3. Clone the Repository

```bash
git clone https://github.com/YOUR_COURSE_REPO_URL.git
```

### 4. Update the Repository

Navigate into the folder and run:

```bash
git pull
```

## Recommended Editor: Visual Studio Code

### 1. Download

- Go to [https://code.visualstudio.com](https://code.visualstudio.com)
- Download the version matching your OS (Windows, macOS, or Linux).
- Follow installation instructions.

### 2. Benefits of VS Code

- **Syntax highlighting** for JSON, YAML, Python, etc.
- Useful for editing AWS config files and CloudFormation templates.
- Lightweight yet powerful.
- Free and cross-platform.

> If you prefer Atom, Sublime Text, or another code editor, feel free to use that instead.

## Where to Find Lesson Resources

- **Lesson Text/Description**: Usually contains important links or commands.
- **GitHub Repository**: Always contains **the most up-to-date** resources and command references.

## Final Tips

- Use `git pull` regularly to stay updated.
- Bookmark or pin **Visual Studio Code** for convenience.
- The **GitHub repository is the authoritative source** for any changes, updates, or fixes to course material.
