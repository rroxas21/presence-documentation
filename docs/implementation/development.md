# Development
## Project Structure

The Presence app follows this structure based on the provided files:

```
Presence/
├── App/                      # App lifecycle
│   └── PresenceApp.swift     # Main app entry point
├── Views/                    # SwiftUI views
│   ├── ContentView.swift     # Main interface
│   ├── PlaybackControlsView.swift # Video playback controls
│   ├── Immersive360View.swift # 360° video immersive view
│   └── AVPlayerView.swift    # UIKit player wrapper
├── Models/                   # Data models and managers
│   ├── VideoStreamManager.swift # Video playback and streaming manager
│   └── VideoMaterial.swift   # RealityKit material handling
├── Resources/                # Assets and resources
│   ├── Assets.xcassets       # Image assets
│   └── Videos/               # 360° video content (e.g., "Cable Car.mp4")
├── Info.plist                # App configuration
└── Presence.entitlements     # App entitlements
```

## Key Components

### 1. VideoStreamManager

The `VideoStreamManager` class is the central component for video handling, as shown in your implementation:

```swift
class VideoStreamManager: ObservableObject {
    @Published var isPlaying = false
    @Published var duration: Double = 0.0
    @Published var currentTime: Double = 0.0
    
    var videoMaterial = VideoMaterial()
    
    // Methods for video playback
    func playLocalVideo(named name: String, withExtension ext: String) { ... }
    func play() { ... }
    func pause() { ... }
    func togglePlayPause() { ... }
    func seek(to time: Double) { ... }
    func updateVideoTexture() { ... }
}
```

This class provides:
- Video loading from local bundle
- Playback control (play, pause, seek)
- Progress tracking and duration information
- Video frame extraction for texture updates

### 2. VideoMaterial

The `VideoMaterial` class handles the conversion from video frames to RealityKit textures:

```swift
class VideoMaterial {
    private var material: SimpleMaterial
    
    func updateTexture(with pixelBuffer: CVPixelBuffer) { ... }
    
    var simpleMaterial: SimpleMaterial { 
        return material 
    }
}
```

This class:
- Manages a RealityKit SimpleMaterial
- Converts pixel buffers to textures via CIImage and CGImage
- Updates material textures with new video frames

### 3. PlaybackControlsView

The `PlaybackControlsView` provides the user interface for controlling video playback:

```swift
struct PlaybackControlsView: View {
    @EnvironmentObject private var videoStreamManager: VideoStreamManager
    @Environment(\.dismissImmersiveSpace) var dismissImmersiveSpace
    
    // UI elements for playback control
    // - Progress bar with seek functionality
    // - Play/pause button
    // - Skip forward/backward buttons
    // - Time display
    // - Exit button
}
```

### 4. Immersive360View

The `Immersive360View` creates the immersive environment for 360° video playback:

```swift
struct Immersive360View: View {
    @State private var player = AVPlayer()
    @Environment(\.dismissImmersiveSpace) var dismissImmersiveSpace
    @State private var debugMessage = "Loading..."
    
    // RealityView for 3D content
    // Sphere creation and setup
    // Video texture application
}
```

### 5. ContentView

The `ContentView` serves as the main entry point and control center:

```swift
struct ContentView: View {
    @State private var isImmersiveViewPresented = false
    @EnvironmentObject private var videoStreamManager: VideoStreamManager
    
    // Main UI for launching the immersive experience
    // Status information
}
```

## Development Workflow

### Building and Running

1. **Select Target Device**:
   - Choose "Vision Pro Simulator" from the device dropdown in Xcode
   - For physical device testing, connect your Apple Vision Pro and select it

2. **Build and Run**:
   - Press `Cmd + R` or click the Play button to build and run the app
   - The first build may take several minutes to complete

### State Management

The app uses SwiftUI's environment objects for state management. Based on your files, the VideoStreamManager is shared across different views:

```swift
// In PresenceApp.swift (inferred from your implementation)
@main
struct PresenceApp: App {
    // Create a shared instance of VideoStreamManager
    @StateObject private var videoStreamManager = VideoStreamManager()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(videoStreamManager)
        }
        
        ImmersiveSpace(id: "360Viewer") {
            Immersive360View()
                .environmentObject(videoStreamManager)
        }
    }
}
```

This pattern allows sharing the VideoStreamManager across different views while maintaining a single source of truth for the application state.

## Working with 360° Videos

### Adding Video Content

1. **Adding Video Content**:
   - Place 360° video files (in MP4 format) in the `Resources/Videos` directory
   - Add reference to the video in the Xcode project:
     - Right-click on the `Resources/Videos` group
     - Select "Add Files to Presence..."
     - Choose your video file

2. **Video Format Requirements**:
   - Format: MP4 (H.264 or HEVC)
   - Resolution: 4K (3840×2160) or higher recommended
   - Projection: Equirectangular (standard 360° video format)

3. **Using VideoStreamManager for Playback**:
   
   To play local bundle videos (as shown in your ContentView):
   ```swift
   videoStreamManager.playLocalVideo(named: "Cable Car", withExtension: "mp4")
   ```
   
   Controlling playback:
   ```swift
   // Play/pause
   videoStreamManager.togglePlayPause()
   
   // Seek to specific time
   videoStreamManager.seek(to: 30.0) // Seek to 30 seconds
   ```

4. **Video Naming Considerations**:

   The VideoStreamManager includes fallback strategies for finding videos with different naming conventions, as shown in your code:
   
   ```swift
   // Try with spaces
   url = Bundle.main.url(forResource: name, withExtension: ext)
   
   // Try replacing spaces with underscores
   if url == nil {
       let nameWithoutSpaces = name.replacingOccurrences(of: " ", with: "_")
       url = Bundle.main.url(forResource: nameWithoutSpaces, withExtension: ext)
   }
   
   // Try without any special characters
   if url == nil {
       let nameSimplified = name.components(separatedBy: CharacterSet.alphanumerics.inverted).joined()
       url = Bundle.main.url(forResource: nameSimplified, withExtension: ext)
   }
   ```

## Immersive Space Implementation

The app uses visionOS's immersive spaces to create the 360° video experience, as shown in your files:

1. **Define the Immersive Space**:
   ```swift
   // In PresenceApp.swift (inferred from your implementation)
   ImmersiveSpace(id: "360Viewer") {
       Immersive360View()
           .environmentObject(videoStreamManager)
   }
   ```

2. **Launch the Immersive Space**:
   From your ContentView.swift:
   ```swift
   @Environment(\.openImmersiveSpace) var openImmersiveSpace
   
   Button("Play 360° Video") {
       videoStreamManager.playLocalVideo(named: "Cable Car", withExtension: "mp4")
       Task {
           isImmersiveViewPresented = true
           await openImmersiveSpace(id: "360Viewer")
       }
   }
   ```

3. **Exit the Immersive Space**:
   From your PlaybackControlsView.swift:
   ```swift
   @Environment(\.dismissImmersiveSpace) var dismissImmersiveSpace
   
   Button("Exit") {
       Task {
           await dismissImmersiveSpace()
       }
   }
   ```

4. **Track Immersive Space State**:
   From your ContentView.swift:
   ```swift
   // State to track whether the immersive space is open
   @State private var isImmersiveViewPresented = false
   
   // Listen for dismissal notifications
   .onAppear {
       NotificationCenter.default.addObserver(forName: Notification.Name("ImmersiveSpaceDismissed"), object: nil, queue: .main) { _ in
           isImmersiveViewPresented = false
       }
   }
   ```

## Testing

### Simulator Testing

The visionOS Simulator provides basic functionality for testing:

1. Launch the app in the Vision Pro Simulator
2. Use mouse clicks and keyboard shortcuts to navigate the simulated environment
3. Note: 
   - Video performance in the simulator may not match device performance
   - The debug text overlay in Immersive360View can help monitor loading state and errors

### Device Testing

For complete testing with actual performance metrics:

1. Connect your Apple Vision Pro to your Mac
2. Select your device in Xcode's device dropdown
3. Build and run directly to the device
4. Test all interactions in the actual hardware environment

### Debugging Video Playback

The app includes several built-in debugging features, as seen in your code:

1. **Console Logging**: 
   ```swift
   print("Found video URL: \(fileURL)")
   print("Video playback finished")
   ```

2. **UI Status Indicators**:
   ```swift
   Text("Video file status: \(videoStreamManager.isPlaying ? "Playing" : "Not playing")")
   ```

3. **File Location Debugging**:
   ```swift
   print("Files in bundle: \(Bundle.main.urls(forResourcesWithExtension: ext, subdirectory: nil) ?? [])")
   ```

4. **Debug Messages in Immersive View**:
   ```swift
   @State private var debugMessage = "Loading..."
   // Later updated with status:
   debugMessage = "Found video"
   debugMessage = "Video playing"
   debugMessage = "Error: \(error.localizedDescription)"
   ```

## Resources

- [Apple Developer Documentation](https://developer.apple.com/documentation/)
- [visionOS Development](https://developer.apple.com/visionos/)
- [RealityKit Framework](https://developer.apple.com/documentation/realitykit)
- [AVFoundation Framework](https://developer.apple.com/documentation/avfoundation)
- [SwiftUI Documentation](https://developer.apple.com/documentation/swiftui)
