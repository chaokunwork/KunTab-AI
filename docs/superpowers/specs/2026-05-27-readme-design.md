# Spec: KunTab Repository README files

## 1. Goal Description

This specification outlines the design and content of the English README (`README.md`) and Chinese README (`README.zh-CN.md`) for the open-source repository `KunTab`. It ensures that when users visit the GitHub page, they are presented with a highly professional, visually appealing, and clear introduction of the extension, its features, its development guides, and how to contribute.

## 2. Requirements & Deliverables

- **Deliverables**:
  - `README.md`: English documentation serving as the primary landing page on GitHub.
  - `README.zh-CN.md`: Simplified Chinese documentation for local language speakers.
- **Tone**: Professional, modern, user-friendly, developer-ready.
- **Design Style (Approach A)**:
  - Header with clear branding, description, badges, and quick language switching links.
  - Visual Feature Showcase (bullet points with custom icons/emojis, highlighting the high-quality UI/UX).
  - Clean development setup commands.
  - Deployment/Installation guidelines for developer mode loading.
  - Technical architecture overview.

## 3. Detailed Structure & Content

### 3.1. README.md (English)
- **Title**: `# 🌌 KunTab`
- **Subtitle**: A premium, beautiful, and feature-rich Chrome New Tab Extension for bookmarks management. Built with WXT, React 19, and TypeScript.
- **Badges**: GitHub license badge, build status badge, WXT version badge, React version badge.
- **Language Switch**: English | [简体中文](README.zh-CN.md)
- **Features List**:
  - **Elegant Dashboard**: Customizable wallpaper background with blur, overlay opacity adjustments, and sleek translucent cards (glassmorphism feel).
  - **Bookmark Management**: Interactive sidebar folder tree, deep nesting support, folder CRUD, bookmark CRUD, and mark-as-frequent shortcut.
  - **Power Search & Command Shortcuts**: Integrated customizable search engines with prefix prefixes (e.g. `g ` for Google, `gh ` for GitHub).
  - **Sleek Customization Settings**: Multilingual UI, themes (Light / Dark / Auto), compact layouts, custom font-sizing, custom wallpaper config.
  - **Data Security**: Secure JSON & HTML bookmark/settings export/import (duplicate avoidance logic included).
- **Getting Started**:
  - Clone, `npm install`, `npm run dev`.
  - Step-by-step developer loading instructions for Chrome/Firefox.
- **Build**:
  - `npm run build`, `npm run zip`.

### 3.2. README.zh-CN.md (Chinese)
- Same contents translated into idiomatic, modern technical Chinese.

## 4. Verification Plan

- Ensure both files render beautifully on GitHub markdown (local verification).
- Check that all file links (e.g. pointing to `README.zh-CN.md` or `README.md`) are absolute relative and valid.
- Verify command line snippets are correct and aligned with `package.json` scripts.
