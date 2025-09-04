# APK Explorer & Editor - Technical Architecture Documentation

## Overview

APK Explorer & Editor (AEE) is an Android application that provides comprehensive APK exploration, editing, and management capabilities. This document outlines the technical architecture, component relationships, and system design patterns used in the application.

## System Architecture

```mermaid
graph TD
    A[User Interface Layer] --> B[Application Layer]
    B --> C[Service Layer]
    B --> D[Utility Layer]
    D --> E[Data Access Layer]
    
    A1[Activities] --> A
    A2[Fragments] --> A
    A3[Adapters] --> A
    A4[Dialogs] --> A
    
    B1[MainActivity] --> B
    B2[Navigation Controller] --> B
    B3[Fragment Manager] --> B
    
    C1[InstallerService] --> C
    C2[Background Tasks] --> C
    
    D1[APK Operations] --> D
    D2[File Management] --> D
    D3[Signing & Security] --> D
    D4[Smali Processing] --> D
    
    E1[Internal Storage] --> E
    E2[External Storage] --> E
    E3[Package Manager] --> E
    E4[System APIs] --> E
```

## Component Architecture

### Core Components Overview

```mermaid
classDiagram
    class MainActivity {
        +BottomNavigationView bottomNav
        +Fragment currentFragment
        +onCreate()
        +handleNavigation()
    }
    
    class APKExploreActivity {
        +APKData apkData
        +RecyclerView fileList
        +exploreAPK()
        +extractFiles()
    }
    
    class APKTasksActivity {
        +APKSigner signer
        +SmaliToDex converter
        +signAPK()
        +buildAPK()
    }
    
    class InstallerService {
        +SplitAPKInstaller installer
        +installAPK()
        +handleInstallation()
    }
    
    MainActivity --> APKExploreActivity
    MainActivity --> APKTasksActivity
    APKTasksActivity --> InstallerService
```

### Fragment Architecture

```mermaid
graph LR
    A[MainActivity] --> B[BottomNavigationView]
    B --> C[APKsFragment]
    B --> D[ApplicationsFragment]
    B --> E[ProjectsFragment]
    B --> F[AboutFragment]
    
    C --> C1[APKsAdapter]
    C --> C2[APKData]
    
    D --> D1[ApplicationsAdapter]
    D --> D2[AppData]
    
    E --> E1[ProjectsAdapter]
    E --> E2[Projects]
```

## Data Flow Architecture

### APK Processing Flow

```mermaid
flowchart TD
    A[User Selects APK] --> B[APKData.initialize]
    B --> C{APK Valid?}
    C -->|Yes| D[Extract APK Contents]
    C -->|No| E[Show Error Dialog]
    
    D --> F[Parse Manifest]
    D --> G[Extract Resources]
    D --> H[Process DEX Files]
    
    F --> I[Display APK Info]
    G --> J[Show File Explorer]
    H --> K[Enable Smali Editing]
    
    I --> L[APKDetailsFragment]
    J --> M[APKExplorerFragment]
    K --> N[TextEditorActivity]
```

### File Operations Flow

```mermaid
sequenceDiagram
    participant U as User
    participant UI as UI Layer
    participant APK as APKExplorer
    participant FS as File System
    participant PM as Package Manager
    
    U->>UI: Select APK File
    UI->>APK: loadAPK(path)
    APK->>FS: Read APK File
    FS-->>APK: APK Data
    APK->>APK: Parse Contents
    APK->>PM: Get Package Info
    PM-->>APK: Package Details
    APK-->>UI: APK Structure
    UI-->>U: Display Explorer
```

## Utility Layer Architecture

### Core Utilities

```mermaid
classDiagram
    class APKEditorUtils {
        +extractFiles()
        +copyFiles()
        +deleteFiles()
        +createDirectories()
    }
    
    class APKSigner {
        +signAPK()
        +verifySignature()
        +generateKeystore()
        +zipAlign()
    }
    
    class APKExplorer {
        +getAPKContents()
        +getManifestData()
        +getResourceFiles()
        +getDexFiles()
    }
    
    class SmaliToDex {
        +assembleDex()
        +disassembleDex()
        +validateSmali()
    }
    
    class DexToSmali {
        +disassemble()
        +extractClasses()
        +generateSmali()
    }
    
    APKEditorUtils <-- APKSigner
    APKExplorer <-- APKEditorUtils
    SmaliToDex <-- APKSigner
    DexToSmali <-- APKExplorer
```

### Task Management

```mermaid
stateDiagram-v2
    [*] --> Idle
    Idle --> Processing: Start Task
    Processing --> Success: Task Completed
    Processing --> Error: Task Failed
    Processing --> Cancelled: User Cancelled
    Success --> Idle
    Error --> Idle
    Cancelled --> Idle
    
    Processing --> SubTask1: Extract APK
    Processing --> SubTask2: Process Smali
    Processing --> SubTask3: Sign APK
    Processing --> SubTask4: Install APK
```

## Activity Navigation Flow

```mermaid
graph TD
    A[StartActivity] --> B[MainActivity]
    B --> C[SettingsActivity]
    B --> D[APKExploreActivity]
    B --> E[InstallerActivity]
    
    D --> F[TextEditorActivity]
    D --> G[TextViewActivity]
    D --> H[ImageViewActivity]
    D --> I[APKTasksActivity]
    
    I --> J[APKSignActivity]
    I --> K[DocumentationActivity]
    
    E --> L[InstallerFilePickerActivity]
    E --> M[FilePickerActivity]
```

## Service Architecture

### Background Services

```mermaid
graph LR
    A[InstallerService] --> B[Notification Manager]
    A --> C[Package Installer]
    A --> D[File Operations]
    
    B --> B1[Progress Updates]
    B --> B2[Completion Status]
    B --> B3[Error Notifications]
    
    C --> C1[Split APK Installation]
    C --> C2[Regular APK Installation]
    C --> C3[Bundle Installation]
    
    D --> D1[File Validation]
    D --> D2[Permission Checks]
    D --> D3[Storage Management]
```

## Data Layer Architecture

### Data Models

```mermaid
erDiagram
    APKData {
        string name
        string packageName
        string versionName
        int versionCode
        string installLocation
        string minSDK
        string targetSDK
        string[] permissions
        string[] activities
        string[] services
    }
    
    AppData {
        string appName
        string packageName
        string versionName
        Drawable icon
        boolean isSystemApp
        long installTime
        long updateTime
    }
    
    ProjectData {
        string projectName
        string projectPath
        string originalAPK
        string modifiedAPK
        Date createdDate
        Date modifiedDate
        string[] modifications
    }
    
    APKData ||--o{ AppData : "installed_from"
    ProjectData ||--o{ APKData : "based_on"
```

## Security Architecture

### APK Signing Process

```mermaid
flowchart TD
    A[Modified APK] --> B[Validate APK Structure]
    B --> C{Structure Valid?}
    C -->|No| D[Show Error]
    C -->|Yes| E[ZipAlign APK]
    E --> F[Generate/Load Keystore]
    F --> G[Sign APK with Key]
    G --> H[Verify Signature]
    H --> I{Signature Valid?}
    I -->|No| J[Signing Failed]
    I -->|Yes| K[Signed APK Ready]
```

### Permission Management

```mermaid
graph TD
    A[App Launch] --> B[Check Storage Permission]
    B --> C{Permission Granted?}
    C -->|No| D[Request Permission]
    C -->|Yes| E[Initialize File Access]
    D --> F{User Granted?}
    F -->|No| G[Limited Functionality]
    F -->|Yes| E
    E --> H[Full App Features Available]
```

## Build and Deployment Architecture

### Build Process

```mermaid
graph LR
    A[Source Code] --> B[Gradle Build]
    B --> C[Compile Java]
    B --> D[Process Resources]
    B --> E[Generate R.java]
    
    C --> F[DEX Generation]
    D --> F
    E --> F
    
    F --> G[Package APK]
    G --> H[Sign APK]
    H --> I[Align APK]
    I --> J[Final APK]
```

### Flavor Configuration

```mermaid
graph TD
    A[Build Configuration] --> B[Play Flavor]
    A --> C[Full Flavor]
    
    B --> B1[Application ID: com.apk.explorer]
    B --> B2[Limited Features]
    B --> B3[Play Store Compliant]
    
    C --> C1[Application ID: com.apk.editor]
    C --> C2[Full Features]
    C --> C3[Direct Installation]
```

## Performance Considerations

### Memory Management

```mermaid
graph TD
    A[Large APK Processing] --> B[Stream Processing]
    A --> C[Chunked Reading]
    A --> D[Memory Monitoring]
    
    B --> E[Avoid Full Load]
    C --> F[Process in Batches]
    D --> G[GC Management]
    
    E --> H[Better Performance]
    F --> H
    G --> H
```

### Async Operations

```mermaid
sequenceDiagram
    participant UI as UI Thread
    participant BG as Background Thread
    participant FS as File System
    participant USR as User
    
    USR->>UI: Initiate APK Operation
    UI->>UI: Show Progress Dialog
    UI->>BG: Start Background Task
    BG->>FS: Process APK Files
    FS-->>BG: File Data
    BG->>BG: Process Data
    BG-->>UI: Progress Updates
    UI-->>USR: Update Progress
    BG-->>UI: Task Complete
    UI->>UI: Hide Progress Dialog
    UI-->>USR: Show Results
```

## Error Handling Architecture

```mermaid
graph TD
    A[Operation Started] --> B{Try Operation}
    B -->|Success| C[Return Result]
    B -->|Exception| D[Catch Exception]
    D --> E[Log Error]
    E --> F[Show User Dialog]
    F --> G[Provide Recovery Options]
    G --> H[User Choice]
    H -->|Retry| B
    H -->|Cancel| I[Return to Previous State]
    H -->|Report| J[Send Error Report]
```

## Extension Points

The architecture provides several extension points for future enhancements:

1. **Plugin System**: Utility layer can be extended with new APK processing tools
2. **Custom Signers**: APKSigner can support additional signing methods
3. **New File Formats**: APKExplorer can be extended to support other Android formats
4. **Advanced Editing**: Text editor can be enhanced with syntax highlighting and validation
5. **Cloud Integration**: Data layer can be extended with cloud storage providers

## Dependencies and Third-Party Libraries

### Key Dependencies

```mermaid
graph LR
    A[AEE App] --> B[AndroidX Libraries]
    A --> C[Material Design Components]
    A --> D[sCommon Utilities]
    A --> E[CrashReporter]
    
    B --> B1[AppCompat]
    B --> B2[Fragment]
    B --> B3[RecyclerView]
    
    C --> C1[Bottom Navigation]
    C --> C2[Material Themes]
    C --> C3[UI Components]
    
    D --> D1[Common Utils]
    D --> D2[Theme Utils]
    D --> D3[Adapters]
```

## Conclusion

The APK Explorer & Editor follows a well-structured Android architecture with clear separation of concerns. The modular design enables maintainability and extensibility while providing robust APK processing capabilities. The use of standard Android patterns ensures familiarity for developers and consistency with platform conventions.

## Future Architectural Improvements

1. **MVVM Pattern**: Consider migrating to Model-View-ViewModel architecture
2. **Dependency Injection**: Implement DI framework for better testability
3. **Repository Pattern**: Add repository layer for data access abstraction
4. **Coroutines**: Migrate from traditional async tasks to Kotlin Coroutines
5. **Modularization**: Split into feature modules for better build performance