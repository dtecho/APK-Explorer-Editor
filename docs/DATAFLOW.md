# APK Explorer & Editor - Data Flow Documentation

## Overview

This document details the data flow patterns, processing workflows, and information architecture within the APK Explorer & Editor application.

## APK Processing Pipeline

### Complete APK Processing Flow

```mermaid
flowchart TD
    A[User Selects APK File] --> B[File Path Validation]
    B --> C{File Exists & Readable?}
    C -->|No| D[Show Error Dialog]
    C -->|Yes| E[Initialize APKData Object]
    
    E --> F[Extract Basic Info]
    F --> G[Parse AndroidManifest.xml]
    G --> H[Extract Application Info]
    H --> I[Load APK Contents List]
    
    I --> J[Process Resources]
    I --> K[Process DEX Files]
    I --> L[Process Native Libraries]
    I --> M[Process Assets]
    
    J --> N[Resource Processing Complete]
    K --> O[DEX Processing Complete]
    L --> P[Native Lib Processing Complete]
    M --> Q[Assets Processing Complete]
    
    N --> R[Display APK Explorer]
    O --> R
    P --> R
    Q --> R
    
    R --> S[User Navigation & Interaction]
    S --> T[File Operations]
    S --> U[Edit Operations]
    S --> V[Export Operations]
```

### APK Data Structure Flow

```mermaid
erDiagram
    APK_FILE {
        string filePath
        long fileSize
        string fileName
        date modifiedDate
    }
    
    APK_MANIFEST {
        string packageName
        string versionName
        int versionCode
        int minSdkVersion
        int targetSdkVersion
        string applicationClass
        string[] permissions
        string[] activities
        string[] services
        string[] receivers
    }
    
    APK_RESOURCES {
        string[] drawables
        string[] layouts
        string[] values
        string[] raw
        string[] assets
        string[] fonts
    }
    
    APK_CODE {
        string[] dexFiles
        string[] nativeLibs
        string[] smaliFiles
        string[] jarFiles
    }
    
    APK_CERTIFICATE {
        string issuer
        string subject
        date validFrom
        date validTo
        string algorithm
        string fingerprint
    }
    
    APK_FILE ||--|| APK_MANIFEST : contains
    APK_FILE ||--o{ APK_RESOURCES : includes
    APK_FILE ||--o{ APK_CODE : includes
    APK_FILE ||--|| APK_CERTIFICATE : signed_with
```

## User Interaction Data Flow

### Navigation Flow

```mermaid
sequenceDiagram
    participant U as User
    participant M as MainActivity
    participant F as Fragment
    participant A as Adapter
    participant D as DataSource
    
    U->>M: App Launch
    M->>M: Initialize Bottom Navigation
    M->>F: Load Default Fragment (APKs)
    F->>D: Request APK List
    D-->>F: Return APK Data
    F->>A: Populate Adapter
    A-->>F: Adapter Ready
    F-->>M: Fragment Loaded
    M-->>U: Display UI
    
    U->>M: Select Different Tab
    M->>F: Switch Fragment
    F->>D: Request New Data
    D-->>F: Return Data
    F->>A: Update Adapter
    A-->>U: Display New Content
```

### APK Selection and Processing

```mermaid
graph TD
    A[User Clicks APK Item] --> B[APKsAdapter.onClick()]
    B --> C[Create Intent]
    C --> D[Start APKExploreActivity]
    D --> E[Load APK Data]
    
    E --> F[Display Loading Dialog]
    E --> G[Background Processing]
    
    G --> H[Read APK File]
    G --> I[Parse Manifest]
    G --> J[Extract File List]
    
    H --> K{Success?}
    I --> L{Success?}
    J --> M{Success?}
    
    K -->|No| N[Show Error]
    L -->|No| N
    M -->|No| N
    
    K -->|Yes| O[Continue Processing]
    L -->|Yes| O
    M -->|Yes| O
    
    O --> P[Hide Loading Dialog]
    P --> Q[Populate File Explorer]
    Q --> R[Enable User Interactions]
```

## File Operations Data Flow

### File Extraction Process

```mermaid
sequenceDiagram
    participant U as User
    participant UI as ExplorerActivity
    participant FP as FileProcessor
    participant FS as FileSystem
    participant APK as APKFile
    
    U->>UI: Select File to Extract
    UI->>UI: Show Extract Options Dialog
    U->>UI: Choose Extract Location
    UI->>FP: startExtraction(file, destination)
    FP->>FP: Validate Permissions
    FP->>APK: openAPKFile()
    APK-->>FP: APK Stream
    FP->>APK: findFileEntry(filename)
    APK-->>FP: File Entry
    FP->>FS: createOutputFile(destination)
    FP->>APK: extractFileData(entry)
    APK-->>FP: File Data Stream
    FP->>FS: writeFileData(outputFile, data)
    FS-->>FP: Write Complete
    FP-->>UI: Extraction Complete
    UI-->>U: Show Success Message
```

### File Modification Workflow

```mermaid
flowchart TD
    A[User Selects File to Edit] --> B[Check File Type]
    B --> C{Text File?}
    C -->|Yes| D[Open Text Editor]
    C -->|No| E{Image File?}
    E -->|Yes| F[Open Image Viewer]
    E -->|No| G{Binary XML?}
    G -->|Yes| H[Parse and Display XML]
    G -->|No| I[Show as Hex/Binary]
    
    D --> J[User Edits Text]
    J --> K[Validate Changes]
    K --> L{Valid?}
    L -->|No| M[Show Validation Error]
    L -->|Yes| N[Save Changes]
    
    H --> O[User Edits XML]
    O --> P[Validate XML Structure]
    P --> Q{Valid?}
    Q -->|No| R[Show XML Error]
    Q -->|Yes| S[Compile to Binary XML]
    S --> N
    
    N --> T[Update APK Contents]
    T --> U[Mark Project as Modified]
```

## Data Persistence Flow

### Project Management Data Flow

```mermaid
graph LR
    A[User Creates Project] --> B[Project Data Object]
    B --> C[Project Directory Creation]
    C --> D[Copy Original APK]
    D --> E[Create Working Directory]
    E --> F[Initialize Project Metadata]
    F --> G[Save Project Config]
    
    H[User Modifies Files] --> I[Track Changes]
    I --> J[Update Metadata]
    J --> K[Save Incremental Changes]
    
    L[User Builds APK] --> M[Collect Modified Files]
    M --> N[Rebuild APK Structure]
    N --> O[Sign APK]
    O --> P[Save Final APK]
    P --> Q[Update Project Status]
```

### Settings and Configuration Flow

```mermaid
sequenceDiagram
    participant U as User
    participant S as SettingsActivity
    participant SP as SharedPreferences
    participant A as Application
    
    U->>S: Open Settings
    S->>SP: Load Current Settings
    SP-->>S: Return Settings Values
    S-->>U: Display Current Settings
    
    U->>S: Modify Setting
    S->>SP: Save New Value
    SP-->>S: Confirm Save
    S->>A: Notify Setting Changed
    A->>A: Apply New Setting
    S-->>U: Update UI Reflection
```

## APK Building and Signing Flow

### APK Assembly Process

```mermaid
flowchart TD
    A[User Initiates Build] --> B[Validate Project State]
    B --> C{All Dependencies Ready?}
    C -->|No| D[Show Dependency Error]
    C -->|Yes| E[Create Build Directory]
    
    E --> F[Copy Modified Resources]
    E --> G[Process Modified DEX Files]
    E --> H[Copy Native Libraries]
    E --> I[Copy Unmodified Assets]
    
    F --> J[Resource Processing Complete]
    G --> K[Smali to DEX Compilation]
    H --> L[Native Lib Processing Complete]
    I --> M[Asset Processing Complete]
    
    K --> N{Smali Compilation Success?}
    N -->|No| O[Show Compilation Error]
    N -->|Yes| P[DEX Processing Complete]
    
    J --> Q[Package APK]
    P --> Q
    L --> Q
    M --> Q
    
    Q --> R[Create Unsigned APK]
    R --> S[APK Signing Process]
```

### Signing Workflow

```mermaid
sequenceDiagram
    participant B as BuildProcess
    participant S as APKSigner
    participant K as KeystoreManager
    participant Z as ZipAlign
    participant F as FileSystem
    
    B->>S: signAPK(unsignedAPK)
    S->>K: loadKeystore()
    K-->>S: Keystore Object
    S->>S: validateKeystore()
    S->>Z: alignAPK(unsignedAPK)
    Z-->>S: Aligned APK
    S->>S: generateSignature()
    S->>F: writeSignedAPK()
    F-->>S: Write Complete
    S-->>B: Signed APK Ready
```

## Error Handling Data Flow

### Error Processing Pipeline

```mermaid
graph TD
    A[Operation Triggered] --> B[Try Block Execution]
    B --> C{Exception Thrown?}
    C -->|No| D[Operation Success]
    C -->|Yes| E[Catch Exception]
    
    E --> F[Log Error Details]
    F --> G[Categorize Error Type]
    
    G --> H{User Error?}
    G --> I{System Error?}
    G --> J{Network Error?}
    G --> K{File Error?}
    
    H -->|Yes| L[Show User-Friendly Message]
    I -->|Yes| M[Show Technical Error]
    J -->|Yes| N[Show Connectivity Error]
    K -->|Yes| O[Show File Access Error]
    
    L --> P[Provide Recovery Options]
    M --> Q[Offer Error Report]
    N --> R[Suggest Retry]
    O --> S[Check Permissions]
    
    P --> T[User Choice]
    Q --> T
    R --> T
    S --> T
    
    T --> U{Retry?}
    U -->|Yes| B
    U -->|No| V[Return to Previous State]
```

## Background Processing Data Flow

### Async Task Management

```mermaid
stateDiagram-v2
    [*] --> TaskQueue
    TaskQueue --> Running: Start Task
    Running --> Progress: Update Progress
    Progress --> Running: Continue
    Running --> Completed: Task Success
    Running --> Failed: Task Error
    Running --> Cancelled: User Cancel
    
    Completed --> TaskQueue: Next Task
    Failed --> TaskQueue: Handle Error
    Cancelled --> TaskQueue: Cleanup
    
    TaskQueue --> [*]: Queue Empty
```

### Service Communication Flow

```mermaid
sequenceDiagram
    participant A as Activity
    participant S as Service
    participant BR as BroadcastReceiver
    participant N as NotificationManager
    
    A->>S: Start Background Operation
    S->>N: Create Progress Notification
    S->>S: Begin Processing
    
    loop Processing
        S->>N: Update Progress
        S->>BR: Broadcast Progress
        BR->>A: Update UI
    end
    
    S->>S: Complete Processing
    S->>N: Show Completion Notification
    S->>BR: Broadcast Completion
    BR->>A: Update UI Final State
    S->>S: Stop Service
```

## Memory Management Data Flow

### Large File Processing

```mermaid
graph TD
    A[Large APK File] --> B[Check Available Memory]
    B --> C{Memory Sufficient?}
    C -->|No| D[Use Streaming Process]
    C -->|Yes| E[Load into Memory]
    
    D --> F[Process in Chunks]
    F --> G[Stream Processing]
    G --> H[Incremental Updates]
    
    E --> I[In-Memory Processing]
    I --> J[Batch Operations]
    
    H --> K[Complete Processing]
    J --> K
    
    K --> L[Release Resources]
    L --> M[Notify Completion]
```

### Resource Cleanup Flow

```mermaid
sequenceDiagram
    participant A as Activity
    participant M as MemoryManager
    participant GC as GarbageCollector
    participant FS as FileSystem
    
    A->>A: Operation Complete
    A->>M: releaseResources()
    M->>FS: closeTempFiles()
    M->>M: clearImageCache()
    M->>M: releaseAPKHandles()
    M->>GC: suggestGC()
    GC->>GC: performCleanup()
    GC-->>M: Cleanup Complete
    M-->>A: Resources Released
```

## Data Validation Flow

### Input Validation Pipeline

```mermaid
flowchart TD
    A[User Input] --> B[Input Received]
    B --> C[Format Validation]
    C --> D{Format Valid?}
    D -->|No| E[Show Format Error]
    D -->|Yes| F[Content Validation]
    
    F --> G{Content Valid?}
    G -->|No| H[Show Content Error]
    G -->|Yes| I[Business Logic Validation]
    
    I --> J{Logic Valid?}
    J -->|No| K[Show Logic Error]
    J -->|Yes| L[Process Valid Input]
    
    E --> M[Return to Input]
    H --> M
    K --> M
    L --> N[Continue Operation]
```

This data flow documentation provides comprehensive coverage of how information moves through the APK Explorer & Editor application, from user interactions to final output generation.