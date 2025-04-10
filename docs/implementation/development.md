# Development 

## Project Structure

The Presence app follows this structure based on the provided files:

```
Presence/
├── App/                      # App lifecycle
│   └── PresenceApp.swift     # Main app entry point
├── Views/                    # SwiftUI views
│   ├── ContentView.swift     # Main interface
│   ├── AVPlaybackControlsView.swift # Video playback controls
│   ├── Immersive360View.swift # 360° video immersive view
│   ├── ImmersiveView.swift   # Alternative immersive view using RealityKitContent
│   └── AVPlayerView.swift    # UIKit player wrapper
├── Models/                   # Data models and managers
│   ├── VideoStreamManager.swift # Video playback and streaming manager
│   ├── VideoMaterial.swift   # RealityKit material handling
│   └── AVPlayerViewModel.swift # UIKit player view model
├── Resources/                # Assets and resources
│   ├── Assets.xcassets       # Image assets
│   ├── RealityKitContent     # RealityKit content bundle
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

### 6. AVPlayerViewModel and AVPlayerView

The `AVPlayerViewModel` class manages UIKit-based video playback:

```swift
@MainActor
@Observable
class AVPlayerViewModel: NSObject {
    var isPlaying: Bool = false
    private var avPlayerViewController: AVPlayerViewController?
    private var avPlayer = AVPlayer()
    
    func makePlayerViewController() -> AVPlayerViewController { ... }
    func play() { ... }
    func reset() { ... }
}
```

The accompanying `AVPlayerView` provides SwiftUI integration:

```swift
struct AVPlayerView: UIViewControllerRepresentable {
    let viewModel: AVPlayerViewModel

    func makeUIViewController(context: Context) -> some UIViewController {
        return viewModel.makePlayerViewController()
    }

    func updateUIViewController(_ uiViewController: UIViewControllerType, context: Context) {
        // Update the AVPlayerViewController as needed
    }
}
```

These components provide:
- UIKit AVPlayerViewController integration with SwiftUI
- Delegation handling for player events
- Clean separation of player logic and view representation

### 7. ImmersiveView

The `ImmersiveView` provides an alternative immersive experience using RealityKitContent:

```swift
struct ImmersiveView: View {
    @Environment(AppModel.self) var appModel
    
    var body: some View {
        RealityView { content in
            // Add the initial RealityKit content
            if let immersiveContentEntity = try? await Entity(named: "Immersive", in: realityKitContentBundle) {
                content.add(immersiveContentEntity)
            }
        }
    }
}
```

This view:
- Uses RealityView to present 3D content
- Loads named entities from a RealityKit content bundle
- Integrates with an AppModel environment object

## Development Workflow

### Building and Running

1. **Select Target Device**:
   - Choose "Vision Pro Simulator" from the device dropdown in Xcode
   - For physical device testing, connect your Apple Vision Pro and select it

2. **Build and Run**:
   - Press `Cmd + R` or click the Play button to build and run the app
   - The first build may take several minutes to complete

### State Management

The app uses two different state management approaches as shown in your files:

1. **SwiftUI Environment Objects** (for the 360° video player):
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

2. **Observable Macro** (for the AVPlayer integration):
   ```swift
   @MainActor
   @Observable
   class AVPlayerViewModel: NSObject {
       var isPlaying: Bool = false
       private var avPlayerViewController: AVPlayerViewController?
       private var avPlayer = AVPlayer()
       // ...
   }
   ```

3. **Environment Values** (for the ImmersiveView):
   ```swift
   struct ImmersiveView: View {
       @Environment(AppModel.self) var appModel
       // ...
   }
   ```

These different approaches are used based on the specific needs of each component and integration pattern.

## Video Playback Approaches

Your code demonstrates multiple approaches to video playback:

### 1. AVPlayer with Manual Texture Updates (Immersive360View)

In this approach, you:
- Create an AVPlayer with a local file
- Set up an AVPlayerItemVideoOutput to extract frames
- Use a CADisplayLink to time the frame extraction
- Convert CVPixelBuffer frames to textures
- Apply textures to a RealityKit material

```swift
// Set up player
let player = AVPlayer(url: videoURL)
self.player = player

// Create a separate task for video setup
Task {
    do {
        try await applyVideoTexture(to: sphere, with: player)
        
        // Start playback
        player.play()
    } catch {
        // Handle errors
    }
}
```

### 2. VideoStreamManager Approach

This approach separates the video playback logic into a manager class:

```swift
func playLocalVideo(named name: String, withExtension ext: String) {
    cleanupCurrentPlayback()
    
    // Find the video file
    guard let fileURL = findVideoFile(name, ext) else {
        return
    }
    
    // Create a simple AVPlayer with the file URL
    player = AVPlayer(url: fileURL)
    player?.play()
    isPlaying = true
    
    // Set up observers
    setupObservers()
}
```

### 3. UIKit AVPlayerViewController Integration (AVPlayerView and AVPlayerViewModel)

This approach wraps a UIKit AVPlayerViewController for playback:

```swift
func makePlayerViewController() -> AVPlayerViewController {
    let avPlayerViewController = AVPlayerViewController()
    avPlayerViewController.player = avPlayer
    avPlayerViewController.delegate = self
    self.avPlayerViewController = avPlayerViewController
    return avPlayerViewController
}

func play() {
    guard !isPlaying, let videoURL else { return }
    isPlaying = true

    let item = AVPlayerItem(url: videoURL)
    avPlayer.replaceCurrentItem(with: item)
    avPlayer.play()
}
```

The SwiftUI integration is handled by a UIViewControllerRepresentable:

```swift
struct AVPlayerView: UIViewControllerRepresentable {
    let viewModel: AVPlayerViewModel

    func makeUIViewController(context: Context) -> some UIViewController {
        return viewModel.makePlayerViewController()
    }

    func updateUIViewController(_ uiViewController: UIViewControllerType, context: Context) {
        // Update the AVPlayerViewController as needed
    }
}
```

## Multiple Immersive Experiences

Two different approaches to immersive experiences:

### 1. Sphere-based 360° Video Playback (Immersive360View)

This approach uses a RealityKit sphere with an inverted scale to display 360° video from the inside:

```swift
// Create a large sphere
let sphere = ModelEntity(
    mesh: .generateSphere(radius: 100),
    materials: [SimpleMaterial(color: .blue, roughness: 0.0, isMetallic: false)]
)

// Make it visible from the inside
sphere.scale = .init(x: -1, y: 1, z: 1)

// Add to the scene
content.add(sphere)

// Try to load and play video
playVideo(sphere: sphere)
```

### 2. RealityKit Content-based Experience (ImmersiveView)

This approach loads pre-authored RealityKit content from a bundle:

```swift
RealityView { content in
    // Add the initial RealityKit content
    if let immersiveContentEntity = try? await Entity(named: "Immersive", in: realityKitContentBundle) {
        content.add(immersiveContentEntity)
    }
}
```

These two approaches can be used for different types of immersive experiences within the app.

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
