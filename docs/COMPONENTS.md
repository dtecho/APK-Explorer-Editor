# APK Explorer & Editor - Component Documentation

## Component Overview

This document provides detailed information about the individual components within the APK Explorer & Editor application architecture.

## Activity Components

### Core Activities

```mermaid
classDiagram
    class StartActivity {
        +onCreate()
        +initializeApp()
        +checkPermissions()
        +navigateToMain()
    }
    
    class MainActivity {
        +BottomNavigationView bottomNav
        +ViewPager2 viewPager
        +Fragment[] fragments
        +onCreate()
        +setupNavigation()
        +handleBackPress()
    }
    
    class APKExploreActivity {
        +APKData currentAPK
        +RecyclerView fileExplorer
        +Toolbar toolbar
        +exploreAPKContents()
        +handleFileSelection()
        +extractResource()
        +showFileDetails()
    }
    
    class APKTasksActivity {
        +List~Task~ activeTasks
        +ProgressBar progressIndicator
        +executeTask()
        +updateProgress()
        +handleTaskCompletion()
    }
    
    StartActivity --> MainActivity : navigate
    MainActivity --> APKExploreActivity : launch
    APKExploreActivity --> APKTasksActivity : process
```

### Specialized Activities

```mermaid
graph TD
    A[Specialized Activities] --> B[TextEditorActivity]
    A --> C[ImageViewActivity]
    A --> D[SettingsActivity]
    A --> E[InstallerActivity]
    A --> F[APKSignActivity]
    
    B --> B1[Syntax Highlighting]
    B --> B2[Auto-completion]
    B --> B3[File Validation]
    
    C --> C1[Image Rendering]
    C --> C2[Zoom Controls]
    C --> C3[Format Support]
    
    D --> D1[Theme Selection]
    D --> D2[Language Settings]
    D --> D3[Storage Options]
    
    E --> E1[APK Selection]
    E --> E2[Installation Progress]
    E --> E3[Error Handling]
    
    F --> F1[Keystore Management]
    F --> F2[Signing Process]
    F --> F3[Verification]
```

## Fragment Components

### Navigation Fragments

```mermaid
stateDiagram-v2
    [*] --> APKsFragment
    APKsFragment --> ApplicationsFragment : Bottom Nav
    ApplicationsFragment --> ProjectsFragment : Bottom Nav
    ProjectsFragment --> AboutFragment : Bottom Nav
    AboutFragment --> APKsFragment : Bottom Nav
    
    APKsFragment --> APKDetails : Select APK
    APKDetails --> APKExplorer : Explore
    APKExplorer --> APKsFragment : Back
    
    ApplicationsFragment --> AppDetails : Select App
    AppDetails --> ApplicationsFragment : Back
    
    ProjectsFragment --> ProjectDetails : Select Project
    ProjectDetails --> ProjectsFragment : Back
```

### Detail Fragments

```mermaid
classDiagram
    class APKDetailsFragment {
        +APKData apkInfo
        +RecyclerView detailsList
        +displayBasicInfo()
        +showPermissions()
        +displayCertificates()
    }
    
    class APKExplorerFragment {
        +TreeView fileTree
        +ContextMenu fileActions
        +navigateDirectory()
        +previewFile()
        +extractFile()
    }
    
    class ManifestFragment {
        +XMLParser parser
        +TextView xmlContent
        +parseManifest()
        +highlightSyntax()
        +validateXML()
    }
    
    class PermissionsFragment {
        +List~Permission~ permissions
        +RecyclerView permissionsList
        +loadPermissions()
        +categorizePermissions()
        +showPermissionDetails()
    }
    
    APKDetailsFragment --> ManifestFragment : show manifest
    APKDetailsFragment --> PermissionsFragment : show permissions
    APKExplorerFragment --> APKDetailsFragment : file details
```

## Service Components

### Background Services

```mermaid
sequenceDiagram
    participant A as Activity
    participant S as InstallerService
    participant N as NotificationManager
    participant PM as PackageManager
    
    A->>S: startInstallation(apkPath)
    S->>N: createProgressNotification()
    S->>S: validateAPK()
    S->>PM: installPackage()
    PM-->>S: installation progress
    S->>N: updateProgress()
    PM-->>S: installation complete
    S->>N: showCompletionNotification()
    S-->>A: broadcast result
```

### Service Lifecycle

```mermaid
stateDiagram-v2
    [*] --> Created
    Created --> Started : startService()
    Started --> Running : onStartCommand()
    Running --> Paused : onPause()
    Paused --> Running : onResume()
    Running --> Stopped : stopService()
    Stopped --> Destroyed : onDestroy()
    Destroyed --> [*]
    
    Running --> Background : moveToBackground()
    Background --> Foreground : moveToForeground()
    Foreground --> Running
```

## Adapter Components

### RecyclerView Adapters

```mermaid
classDiagram
    class BaseAdapter {
        <<abstract>>
        +List~T~ items
        +onCreateViewHolder()
        +onBindViewHolder()
        +getItemCount()
        +notifyDataSetChanged()
    }
    
    class APKsAdapter {
        +List~APKData~ apkList
        +Filter apkFilter
        +bindAPKInfo()
        +handleAPKClick()
        +applyFilter()
    }
    
    class ApplicationsAdapter {
        +List~AppData~ appList
        +PackageManager packageManager
        +bindAppInfo()
        +loadAppIcon()
        +handleAppClick()
    }
    
    class FileExplorerAdapter {
        +List~FileItem~ files
        +FileIconProvider iconProvider
        +bindFileItem()
        +handleFileClick()
        +showContextMenu()
    }
    
    BaseAdapter <|-- APKsAdapter
    BaseAdapter <|-- ApplicationsAdapter
    BaseAdapter <|-- FileExplorerAdapter
```

### Adapter Patterns

```mermaid
graph LR
    A[Data Source] --> B[Adapter]
    B --> C[ViewHolder]
    C --> D[View Binding]
    D --> E[UI Display]
    
    B --> F[Click Handling]
    B --> G[Context Menu]
    B --> H[Filtering]
    B --> I[Sorting]
    
    F --> J[Navigation]
    G --> K[Actions Menu]
    H --> L[Search Results]
    I --> M[Ordered Display]
```

## Utility Components

### File Management Utilities

```mermaid
classDiagram
    class FileManager {
        +createDirectory(path)
        +copyFile(src, dest)
        +deleteFile(path)
        +listFiles(directory)
        +getFileSize(path)
        +isFileReadable(path)
    }
    
    class APKExtractor {
        +extractAPK(apkPath, destPath)
        +extractResource(apkPath, resourcePath)
        +listAPKContents(apkPath)
        +validateAPK(apkPath)
    }
    
    class ZipManager {
        +createZip(files, zipPath)
        +extractZip(zipPath, destPath)
        +addToZip(zipPath, filePath)
        +removeFromZip(zipPath, entryName)
    }
    
    FileManager --> APKExtractor : uses
    APKExtractor --> ZipManager : uses
```

### Security Utilities

```mermaid
graph TD
    A[Security Utils] --> B[Certificate Manager]
    A --> C[Key Store Manager]
    A --> D[Signature Validator]
    
    B --> B1[Read Certificates]
    B --> B2[Validate Certificate Chain]
    B --> B3[Extract Public Key]
    
    C --> C1[Generate KeyStore]
    C --> C2[Load Existing KeyStore]
    C --> C3[Manage Keys]
    
    D --> D1[Verify APK Signature]
    D --> D2[Check Signature Match]
    D --> D3[Validate Integrity]
```

## Dialog Components

### Custom Dialogs

```mermaid
classDiagram
    class BaseDialog {
        <<abstract>>
        +Context context
        +DialogInterface.OnClickListener listener
        +show()
        +dismiss()
        +setTitle()
        +setMessage()
    }
    
    class ProgressDialog {
        +ProgressBar progressBar
        +TextView statusText
        +setProgress(int)
        +setStatus(String)
        +setCancelable(boolean)
    }
    
    class ConfirmationDialog {
        +String title
        +String message
        +String positiveText
        +String negativeText
        +setPositiveAction()
        +setNegativeAction()
    }
    
    class FilePickerDialog {
        +File currentDirectory
        +List~File~ fileList
        +navigateToDirectory()
        +selectFile()
        +createNewFolder()
    }
    
    BaseDialog <|-- ProgressDialog
    BaseDialog <|-- ConfirmationDialog
    BaseDialog <|-- FilePickerDialog
```

### Dialog Flow

```mermaid
sequenceDiagram
    participant U as User
    participant A as Activity
    participant D as Dialog
    participant H as Handler
    
    U->>A: Trigger Action
    A->>D: create dialog
    A->>D: show()
    D-->>U: Display Dialog
    U->>D: User Input
    D->>H: handle input
    H->>A: callback with result
    A->>D: dismiss()
    A->>A: process result
```

## Data Flow Between Components

### Inter-Component Communication

```mermaid
graph TD
    A[User Input] --> B[Activity/Fragment]
    B --> C[Adapter]
    B --> D[Utility Classes]
    B --> E[Service]
    
    C --> F[RecyclerView]
    D --> G[File System]
    D --> H[Android APIs]
    E --> I[Background Processing]
    
    F --> J[View Updates]
    G --> K[Data Storage]
    H --> L[System Integration]
    I --> M[Notifications]
    
    J --> N[User Feedback]
    K --> O[Persistent Data]
    L --> P[Platform Features]
    M --> Q[Status Updates]
```

### Event Handling

```mermaid
sequenceDiagram
    participant UI as UI Component
    participant C as Controller
    participant M as Model
    participant V as View
    
    UI->>C: User Event
    C->>M: Update Model
    M-->>C: Model Changed
    C->>V: Update View
    V-->>UI: Refresh Display
```

## Component Dependencies

### Dependency Graph

```mermaid
graph TD
    A[UI Components] --> B[Controller Components]
    B --> C[Service Components]
    B --> D[Utility Components]
    D --> E[Data Components]
    
    A1[Activities] --> A
    A2[Fragments] --> A
    A3[Adapters] --> A
    A4[Dialogs] --> A
    
    B1[MainActivity] --> B
    B2[Navigation] --> B
    
    C1[InstallerService] --> C
    C2[Background Tasks] --> C
    
    D1[APK Utils] --> D
    D2[File Utils] --> D
    D3[Security Utils] --> D
    
    E1[APK Data] --> E
    E2[App Data] --> E
    E3[Project Data] --> E
```

## Component Testing Strategy

### Unit Testing Structure

```mermaid
graph LR
    A[Component Tests] --> B[Activity Tests]
    A --> C[Fragment Tests]
    A --> D[Adapter Tests]
    A --> E[Utility Tests]
    
    B --> B1[UI Interactions]
    B --> B2[Navigation Logic]
    
    C --> C1[Fragment Lifecycle]
    C --> C2[Data Binding]
    
    D --> D1[Item Binding]
    D --> D2[Click Handling]
    
    E --> E1[Business Logic]
    E --> E2[Data Processing]
```

This component documentation provides detailed insights into how each part of the APK Explorer & Editor application is structured and how components interact with each other to deliver the application's functionality.