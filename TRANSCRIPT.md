# Angular Multi-Version Firebase Hosting POC - Session Transcript

**Date:** February 1, 2026
**Project Location:** `C:\work\claude-code\poc-angular-firebase-multi-version`

---

## Overview

This document captures all activities performed to create and deploy an Angular POC application with two versions (v1 and v2) to Firebase Hosting within a single Firebase project.

---

## Session Activities

### 1. Initial Discussion

**User Question:** Can I deploy two versions of a single Angular app within a single region (staging/UAT/production) on Firebase?

**Answer:** Yes, Firebase Hosting supports multiple sites per project. Two options were provided:
- **Option 1 (Recommended):** Multiple Hosting Sites - each version gets its own URL
- **Option 2:** Path-based routing - different paths on the same site

User chose **Option 1** for the POC.

---

### 2. Planning Phase

A plan was created with the following steps:
1. Install Firebase CLI
2. Create Angular Application
3. Create Version-Specific Environment Files
4. Update Angular Components
5. Configure Angular Build Targets
6. Firebase Setup
7. Create Multiple Hosting Sites
8. Configure Deploy Targets
9. Update firebase.json
10. Add NPM Scripts

**User Input:**
- Firebase project needed to be created
- Base name: `angular-poc`

---

### 3. Angular Project Creation (Step 1-2)

User created the Angular project separately using:
```bash
npx @angular/cli new angular-multi-version-poc --routing --style=scss --standalone
```

**Result:** Angular 20.3.0 project created with standalone components

---

### 4. Environment Files Created (Step 3)

#### File: `src/environments/environment.ts`
```typescript
export const environment = {
  production: false,
  version: '1.0.0',
  versionName: 'Version 1',
  theme: {
    primary: '#1976d2',
    accent: '#2196f3',
    name: 'blue'
  },
  features: ['Dashboard', 'Reports', 'Settings']
};
```

#### File: `src/environments/environment.v1.ts`
```typescript
export const environment = {
  production: true,
  version: '1.0.0',
  versionName: 'Version 1',
  theme: {
    primary: '#1976d2',
    accent: '#2196f3',
    name: 'blue'
  },
  features: ['Dashboard', 'Reports', 'Settings']
};
```

#### File: `src/environments/environment.v2.ts`
```typescript
export const environment = {
  production: true,
  version: '2.0.0',
  versionName: 'Version 2',
  theme: {
    primary: '#388e3c',
    accent: '#4caf50',
    name: 'green'
  },
  features: ['Dashboard', 'Reports', 'Settings', 'Analytics', 'API Access']
};
```

---

### 5. App Component Updated (Step 4)

#### File: `src/app/app.ts`
```typescript
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { TitleCasePipe } from '@angular/common';
import { environment } from '../environments/environment';

@Component({
  selector: 'app-root',
  imports: [RouterOutlet, TitleCasePipe],
  templateUrl: './app.html',
  styleUrl: './app.scss'
})
export class App {
  readonly version = environment.version;
  readonly versionName = environment.versionName;
  readonly theme = environment.theme;
  readonly features = environment.features;
}
```

#### File: `src/app/app.html`
- Displays version badge with theme-specific colors
- Shows application info (version, theme, environment)
- Lists available features dynamically
- Footer with version tag

#### File: `src/app/app.scss`
- CSS variables for theming (`--primary-color`, `--accent-color`)
- Theme-specific badge styles (`.theme-blue`, `.theme-green`)
- Card layouts for info and features sections

---

### 6. Angular Build Configuration Updated (Step 5)

#### File: `angular.json`

Added two new build configurations:

**v1 Configuration:**
```json
{
  "v1": {
    "outputPath": "dist/v1",
    "budgets": [...],
    "outputHashing": "all",
    "fileReplacements": [
      {
        "replace": "src/environments/environment.ts",
        "with": "src/environments/environment.v1.ts"
      }
    ]
  }
}
```

**v2 Configuration:**
```json
{
  "v2": {
    "outputPath": "dist/v2",
    "budgets": [...],
    "outputHashing": "all",
    "fileReplacements": [
      {
        "replace": "src/environments/environment.ts",
        "with": "src/environments/environment.v2.ts"
      }
    ]
  }
}
```

Also added serve configurations for local development:
- `ng serve --configuration=v1`
- `ng serve --configuration=v2`

---

### 7. Firebase Configuration Created (Step 6-9)

#### File: `firebase.json`
```json
{
  "hosting": [
    {
      "target": "v1",
      "public": "dist/v1/browser",
      "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
      "rewrites": [{ "source": "**", "destination": "/index.html" }]
    },
    {
      "target": "v2",
      "public": "dist/v2/browser",
      "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
      "rewrites": [{ "source": "**", "destination": "/index.html" }]
    }
  ]
}
```

#### File: `.firebaserc`
```json
{
  "projects": {
    "default": "angular-poc-75e76"
  },
  "targets": {
    "angular-poc-75e76": {
      "hosting": {
        "v1": ["angular-poc-75e76-v1"],
        "v2": ["angular-poc-75e76-v2"]
      }
    }
  },
  "etags": {}
}
```

---

### 8. NPM Scripts Added (Step 10)

#### File: `package.json` (scripts section)
```json
{
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "start:v1": "ng serve --configuration=v1",
    "start:v2": "ng serve --configuration=v2",
    "build": "ng build",
    "build:v1": "ng build --configuration=v1",
    "build:v2": "ng build --configuration=v2",
    "build:all": "npm run build:v1 && npm run build:v2",
    "deploy:v1": "firebase deploy --only hosting:v1",
    "deploy:v2": "firebase deploy --only hosting:v2",
    "deploy:all": "npm run build:all && firebase deploy --only hosting",
    "watch": "ng build --watch --configuration development",
    "test": "ng test"
  }
}
```

---

### 9. Build Verification

**Command:** `npm run build:all`

**Results:**
- v1 build output: `dist/v1/browser/` (246.64 kB)
- v2 build output: `dist/v2/browser/` (246.67 kB)
- Both builds completed successfully

---

### 10. Firebase Hosting Sites Created

**Firebase Account:** rohitj559@gmail.com
**Project ID:** angular-poc-75e76

**Commands Executed:**
```bash
firebase hosting:sites:create angular-poc-75e76-v1 --project angular-poc-75e76
firebase hosting:sites:create angular-poc-75e76-v2 --project angular-poc-75e76
```

**Results:**
- Site `angular-poc-75e76-v1` created
- Site `angular-poc-75e76-v2` created

---

### 11. Deployment

**Command:** `npm run deploy:all`

**Deployment Log:**
```
=== Deploying to 'angular-poc-75e76'...
i  deploying hosting
i  hosting[angular-poc-75e76-v1]: beginning deploy...
i  hosting[angular-poc-75e76-v1]: found 5 files in dist/v1/browser
+  hosting[angular-poc-75e76-v1]: file upload complete
i  hosting[angular-poc-75e76-v2]: beginning deploy...
i  hosting[angular-poc-75e76-v2]: found 5 files in dist/v2/browser
+  hosting[angular-poc-75e76-v2]: file upload complete
+  hosting[angular-poc-75e76-v1]: version finalized
+  hosting[angular-poc-75e76-v2]: version finalized
+  hosting[angular-poc-75e76-v1]: release complete
+  hosting[angular-poc-75e76-v2]: release complete
+  Deploy complete!
```

---

## Final Result

### Deployed URLs

| Version | URL | Theme | Features |
|---------|-----|-------|----------|
| v1 | https://angular-poc-75e76-v1.web.app | Blue | Dashboard, Reports, Settings |
| v2 | https://angular-poc-75e76-v2.web.app | Green | Dashboard, Reports, Settings, Analytics, API Access |

### Firebase Console
https://console.firebase.google.com/project/angular-poc-75e76/overview

---

## Project Structure

```
angular-multi-version-poc/
├── src/
│   ├── app/
│   │   ├── app.ts
│   │   ├── app.html
│   │   ├── app.scss
│   │   ├── app.config.ts
│   │   ├── app.routes.ts
│   │   └── app.spec.ts
│   ├── environments/
│   │   ├── environment.ts
│   │   ├── environment.v1.ts
│   │   └── environment.v2.ts
│   ├── index.html
│   ├── main.ts
│   └── styles.scss
├── dist/
│   ├── v1/browser/
│   └── v2/browser/
├── angular.json
├── firebase.json
├── .firebaserc
├── package.json
├── tsconfig.json
└── tsconfig.app.json
```

---

## Available Commands

| Command | Description |
|---------|-------------|
| `npm run start` | Serve default app locally |
| `npm run start:v1` | Serve v1 locally |
| `npm run start:v2` | Serve v2 locally |
| `npm run build:v1` | Build v1 |
| `npm run build:v2` | Build v2 |
| `npm run build:all` | Build both versions |
| `npm run deploy:v1` | Deploy v1 to Firebase |
| `npm run deploy:v2` | Deploy v2 to Firebase |
| `npm run deploy:all` | Build and deploy both versions |

---

## Branch-Based Deployment Setup

### 12. Restructuring to Branch-Based Deployment

The project was restructured to use separate Git branches for each version, providing a cleaner workflow where each branch deploys to its respective Firebase hosting site.

**Goal:**
- `v1` branch → deploys to `angular-poc-75e76-v1.web.app`
- `v2` branch → deploys to `angular-poc-75e76-v2.web.app`

---

### 13. Creating v1 Branch

**Commands:**
```bash
git checkout -b v1
```

**Changes Made:**
1. Updated `src/environments/environment.ts` with v1 config (blue theme, 3 features)
2. Removed `environment.v1.ts` and `environment.v2.ts` (no longer needed)
3. Simplified `angular.json` - removed v1/v2 build configurations
4. Simplified `firebase.json` - single site targeting `angular-poc-75e76-v1`
5. Simplified `package.json` scripts

#### v1 Branch: `src/environments/environment.ts`
```typescript
export const environment = {
  production: true,
  version: '1.0.0',
  versionName: 'Version 1',
  theme: {
    primary: '#1976d2',
    accent: '#2196f3',
    name: 'blue'
  },
  features: ['Dashboard', 'Reports', 'Settings']
};
```

#### v1 Branch: `firebase.json`
```json
{
  "hosting": {
    "site": "angular-poc-75e76-v1",
    "public": "dist/angular-multi-version-poc/browser",
    "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
    "rewrites": [{ "source": "**", "destination": "/index.html" }]
  }
}
```

#### v1 Branch: `package.json` (scripts)
```json
{
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "deploy": "ng build && firebase deploy",
    "watch": "ng build --watch --configuration development",
    "test": "ng test"
  }
}
```

**Commit:**
```
Configure v1 branch for Version 1 deployment

- Set environment to v1 (blue theme, 3 features)
- Simplify configs for single-version deployment
- Deploy target: angular-poc-75e76-v1.web.app
```

---

### 14. Creating v2 Branch

**Commands:**
```bash
git checkout master
git checkout -b v2
```

**Changes Made:**
1. Updated `src/environments/environment.ts` with v2 config (green theme, 5 features)
2. Removed `environment.v1.ts` and `environment.v2.ts`
3. Simplified `angular.json`
4. Simplified `firebase.json` - single site targeting `angular-poc-75e76-v2`
5. Simplified `package.json` scripts

#### v2 Branch: `src/environments/environment.ts`
```typescript
export const environment = {
  production: true,
  version: '2.0.0',
  versionName: 'Version 2',
  theme: {
    primary: '#388e3c',
    accent: '#4caf50',
    name: 'green'
  },
  features: ['Dashboard', 'Reports', 'Settings', 'Analytics', 'API Access']
};
```

#### v2 Branch: `firebase.json`
```json
{
  "hosting": {
    "site": "angular-poc-75e76-v2",
    "public": "dist/angular-multi-version-poc/browser",
    "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
    "rewrites": [{ "source": "**", "destination": "/index.html" }]
  }
}
```

**Commit:**
```
Configure v2 branch for Version 2 deployment

- Set environment to v2 (green theme, 5 features)
- Simplify configs for single-version deployment
- Deploy target: angular-poc-75e76-v2.web.app
```

---

### 15. Pushing Branches to GitHub

**Commands:**
```bash
git push -u origin v1
git push -u origin v2
```

**Result:**
- Branch `v1` pushed to https://github.com/rohitj559/angular-multi-version-poc/tree/v1
- Branch `v2` pushed to https://github.com/rohitj559/angular-multi-version-poc/tree/v2

---

### 16. Branch-Based Deployment

**Deploy v1:**
```bash
git checkout v1
npm run deploy
```

**Deploy v2:**
```bash
git checkout v2
npm run deploy
```

**Deployment Results:**
- v1 deployed to https://angular-poc-75e76-v1.web.app
- v2 deployed to https://angular-poc-75e76-v2.web.app

---

## Final Branch Structure

| Branch | Theme | Features | Firebase Site | URL |
|--------|-------|----------|---------------|-----|
| `v1` | Blue | 3 | angular-poc-75e76-v1 | https://angular-poc-75e76-v1.web.app |
| `v2` | Green | 5 | angular-poc-75e76-v2 | https://angular-poc-75e76-v2.web.app |
| `master` | - | - | - | Contains original multi-version setup |

---

## GitHub Repository

**Repository:** https://github.com/rohitj559/angular-multi-version-poc

**Branches:**
- `master` - Original setup with both versions in one branch
- `v1` - Version 1 (blue theme) - https://github.com/rohitj559/angular-multi-version-poc/tree/v1
- `v2` - Version 2 (green theme) - https://github.com/rohitj559/angular-multi-version-poc/tree/v2

---

## Simplified Commands (Branch-Based)

| Action | Command |
|--------|---------|
| Switch to v1 | `git checkout v1` |
| Switch to v2 | `git checkout v2` |
| Deploy current branch | `npm run deploy` |
| Serve locally | `npm run start` |
| Build only | `npm run build` |

---

## Key Learnings

1. **Firebase Project IDs** have a random suffix (e.g., `angular-poc-75e76` instead of `angular-poc`)
2. **Multiple Hosting Sites** can be created within a single Firebase project
3. **Deploy Targets** map logical names (v1, v2) to actual site names
4. **Angular File Replacements** allow swapping environment files at build time
5. **Separate Output Paths** keep builds isolated (`dist/v1`, `dist/v2`)
6. **Branch-Based Deployment** provides cleaner workflow - each branch has its own config
7. **Simplified Scripts** - `npm run deploy` works on any branch without version-specific commands

---

*Generated by Claude Code on February 1-2, 2026*
