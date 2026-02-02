# Angular Multi-Version Firebase Deployment - Flow Diagrams

## 1. Overall Architecture

```mermaid
flowchart TB
    subgraph GitHub["GitHub Repository"]
        master["master branch<br/>(original setup)"]
        v1branch["v1 branch<br/>(blue theme)"]
        v2branch["v2 branch<br/>(green theme)"]
    end

    subgraph Firebase["Firebase Project: angular-poc-75e76"]
        site1["Hosting Site<br/>angular-poc-75e76-v1"]
        site2["Hosting Site<br/>angular-poc-75e76-v2"]
    end

    subgraph URLs["Live URLs"]
        url1["https://angular-poc-75e76-v1.web.app<br/>Version 1 | Blue | 3 Features"]
        url2["https://angular-poc-75e76-v2.web.app<br/>Version 2 | Green | 5 Features"]
    end

    v1branch -->|npm run deploy| site1
    v2branch -->|npm run deploy| site2
    site1 --> url1
    site2 --> url2
```

## 2. Initial Setup Flow

```mermaid
flowchart TD
    A[Start] --> B[Create Angular Project]
    B --> C[Create Environment Files]
    C --> D[Update App Component]
    D --> E[Configure angular.json<br/>v1 & v2 build targets]
    E --> F[Firebase Login]
    F --> G[Create Firebase Hosting Sites]
    G --> H[Configure firebase.json<br/>& .firebaserc]
    H --> I[Add NPM Scripts]
    I --> J[Build & Deploy]
    J --> K[Both versions live]

    subgraph "Environment Files"
        C1[environment.ts]
        C2[environment.v1.ts]
        C3[environment.v2.ts]
    end
    C --> C1
    C --> C2
    C --> C3
```

## 3. Branch-Based Deployment Flow

```mermaid
flowchart TD
    A[Start: master branch] --> B{Create v1 branch}
    B --> C[Simplify for v1 only]
    C --> D[Set environment.ts<br/>to v1 config]
    D --> E[Update firebase.json<br/>target: v1 site]
    E --> F[Commit & Push v1]

    A --> G{Create v2 branch}
    G --> H[Simplify for v2 only]
    H --> I[Set environment.ts<br/>to v2 config]
    I --> J[Update firebase.json<br/>target: v2 site]
    J --> K[Commit & Push v2]

    F --> L[Deploy v1]
    K --> M[Deploy v2]

    L --> N["v1.web.app"]
    M --> O["v2.web.app"]
```

## 4. Deployment Workflow

```mermaid
flowchart LR
    subgraph Developer["Developer Actions"]
        A[git checkout v1] --> B[Make changes]
        B --> C[git commit]
        C --> D[npm run deploy]
    end

    subgraph Build["Build Process"]
        D --> E[ng build]
        E --> F[dist/angular-multi-version-poc/browser]
    end

    subgraph Deploy["Firebase Deploy"]
        F --> G[firebase deploy]
        G --> H[Upload to Hosting]
        H --> I[Release]
    end

    I --> J["Live: v1.web.app"]
```

## 5. Project Structure Comparison

```mermaid
flowchart TB
    subgraph Master["master branch (Original)"]
        M1[environment.ts]
        M2[environment.v1.ts]
        M3[environment.v2.ts]
        M4[angular.json<br/>v1 & v2 configs]
        M5[firebase.json<br/>both targets]
        M6["Scripts:<br/>build:v1, build:v2<br/>deploy:v1, deploy:v2"]
    end

    subgraph V1["v1 branch (Simplified)"]
        V1A[environment.ts<br/>v1 config only]
        V1B[angular.json<br/>standard config]
        V1C[firebase.json<br/>v1 target only]
        V1D["Scripts:<br/>build, deploy"]
    end

    subgraph V2["v2 branch (Simplified)"]
        V2A[environment.ts<br/>v2 config only]
        V2B[angular.json<br/>standard config]
        V2C[firebase.json<br/>v2 target only]
        V2D["Scripts:<br/>build, deploy"]
    end
```

## 6. Version Differences

```mermaid
flowchart LR
    subgraph V1["Version 1"]
        V1T["Theme: Blue<br/>#1976d2"]
        V1F["Features:<br/>- Dashboard<br/>- Reports<br/>- Settings"]
    end

    subgraph V2["Version 2"]
        V2T["Theme: Green<br/>#388e3c"]
        V2F["Features:<br/>- Dashboard<br/>- Reports<br/>- Settings<br/>- Analytics<br/>- API Access"]
    end

    V1 -->|"Evolution"| V2
```

## 7. Complete Activity Timeline

```mermaid
flowchart TD
    T1["1. Discussion<br/>Multi-version deployment options"] --> T2["2. Planning<br/>Choose Option 1: Multiple Sites"]
    T2 --> T3["3. Create Angular App<br/>ng new angular-multi-version-poc"]
    T3 --> T4["4. Create Environments<br/>v1 (blue) & v2 (green)"]
    T4 --> T5["5. Update Components<br/>Display version info"]
    T5 --> T6["6. Configure Builds<br/>angular.json v1/v2 targets"]
    T6 --> T7["7. Firebase Setup<br/>Login & create sites"]
    T7 --> T8["8. Initial Deploy<br/>Both versions live"]
    T8 --> T9["9. Push to GitHub<br/>rohitj559/angular-multi-version-poc"]
    T9 --> T10["10. Restructure to Branches<br/>v1 & v2 branches"]
    T10 --> T11["11. Branch Deployment<br/>Simplified workflow"]
    T11 --> T12["12. Final State<br/>Branch-based deployment"]
```

---

## How to View These Diagrams

1. **GitHub**: These diagrams render automatically when viewing this file on GitHub
2. **VS Code**: Install "Markdown Preview Mermaid Support" extension
3. **Online**: Copy the mermaid code to [mermaid.live](https://mermaid.live)

---

## Quick Reference

| Branch | Command | Result |
|--------|---------|--------|
| v1 | `git checkout v1 && npm run deploy` | Deploys to v1.web.app |
| v2 | `git checkout v2 && npm run deploy` | Deploys to v2.web.app |

---

*Generated by Claude Code on February 2, 2026*
