# File Browser Agent – Agent Skills

A Windows desktop application built with Electron, React, and TypeScript.

The application combines a file browser with an AI chat agent that can inspect files and folders and use tools to perform tasks.

This project was extended with an Agent Skills system that allows the AI agent to discover and activate reusable instructions stored in SKILL.md files.

## What I Implemented

The following Agent Skills functionality was implemented:

- Loading Skills from the user's Skills directory.
- Parsing SKILL.md files and extracting the Skill name, description, and instructions.
- Storing the loaded Skills in memory using SkillsStore.
- Creating a lightweight Skills catalog containing only Skill names and descriptions.
- Adding an activate_skill tool that allows the AI agent to activate a relevant Skill.
- Returning the full Skill instructions to the AI when a Skill is activated.
- Adding a skill:list IPC handler for listing installed Skills.
- Refreshing the Skills catalog after installing a new Skill.
- Adding an example hello-skill to demonstrate the functionality.

## How Agent Skills Work

When the application starts, it scans the Skills directory and loads the available Skills into memory.

Each Skill is stored as a folder containing a SKILL.md file.

Example:

    skills/
    └── hello-skill/
        └── SKILL.md

A SKILL.md file contains a name, description, and the instructions that the AI should follow.

Example:

    ---
    name: hello-skill
    description: Use when the user asks to say hello
    ---

    Start your answer with "Skill activated!"

The AI does not receive the full instructions of every Skill immediately.

Instead, it first receives a catalog containing the Skill names and descriptions.

If the AI determines that a Skill is relevant to the user's request, it calls the activate_skill tool with the Skill name.

The tool finds the Skill in SkillsStore and returns its full instructions to the AI.

The AI can then follow those instructions when generating its response.

## Example

The included hello-skill demonstrates the complete flow.

When the user asks the agent to say hello, the AI identifies that hello-skill is relevant and activates it.

The Skill contains the instruction to start the response with:

    Skill activated!

This demonstrates that the Skill was discovered, activated, and its instructions were provided to the AI.

## File Browser Features

The application can:

- Browse files and folders.
- Display file and folder information.
- List directory contents.
- Read text files.
- Load files such as PDFs and images.
- Run commands with user approval.
- Use AI tools to work with the selected files and folders.

## Skills Management

Skills are loaded from the following directory by default:

    C:\Users\<username>\.file-browser-agent\skills

Each Skill should have its own folder containing a SKILL.md file.

Skills can also be installed from ZIP files using the application's Add Skill functionality.

After a new Skill is installed, the Skills store is reloaded so the new Skill becomes available to the AI agent.

## API Key

The application requires an Anthropic API key.

The API key can be entered through the application.

For local development, an environment file can also be used.

Example:

    ANTHROPIC_API_KEY=your_api_key_here

Do not commit a real API key to GitHub.

## Installation

Install the project dependencies:

    npm install

If the Electron binary needs to be installed manually, run:

    node node_modules/electron/install.js

Start the application:

    npm run dev

## Requirements

- Node.js 18 or later
- Windows
- An Anthropic API key

## Technologies

- Electron
- React
- TypeScript
- Anthropic API
- Electron IPC
- fflate
- Zustand

## Project Structure

    src/
    ├── main/
    │   ├── ipc/
    │   │   ├── chat-handlers.ts
    │   │   ├── fs-handlers.ts
    │   │   ├── selection-handlers.ts
    │   │   └── skill-handlers.ts
    │   │
    │   └── services/
    │       ├── agent-tools.ts
    │       ├── anthropic-chat.ts
    │       ├── fs-service.ts
    │       └── skills.ts
    │
    ├── preload/
    │
    └── renderer/

## Main Skills Files

### skills.ts

Responsible for discovering and loading Skills from the Skills directory.

It parses SKILL.md files and stores the Skill information in memory.

### agent-tools.ts

Contains the activate_skill tool.

The tool receives a Skill name, finds the Skill in SkillsStore, and returns its full instructions.

### anthropic-chat.ts

Provides the AI agent with the available Skills catalog and instructs the agent to activate a Skill when it is relevant.

### skill-handlers.ts

Handles Skill-related IPC operations, including listing installed Skills and installing Skills from ZIP files.

### index.ts

Loads the Skills when the application starts.

## Example Skill

The following example demonstrates how a Skill is structured.

Skills are stored in:

    C:\Users\<username>\.file-browser-agent\skills

Each Skill has its own folder containing a SKILL.md file.

Example:

    C:\Users\<username>\.file-browser-agent\skills\
    └── hello-skill\
        └── SKILL.md

Example SKILL.md:

    ---
    name: hello-skill
    description: Use when the user asks to say hello
    ---

    Start your answer with "Skill activated!"

When the application starts, it loads the available Skills from this directory.

The AI receives only the Skill name and description initially.

When a Skill is relevant to the user's request, the AI calls the
`activate_skill` tool. The tool retrieves the full instructions from
the corresponding SKILL.md file and returns them to the AI.

This allows Skills to provide reusable instructions without including
their full content in the initial prompt.

## Project Goal

The goal of this project is to demonstrate how an AI agent can extend its behavior dynamically using reusable Skills.

The implementation keeps the initial Skills catalog small and loads the complete instructions only when a relevant Skill is activated.