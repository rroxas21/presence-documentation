# How to Edit and Revise the Presence Application

This document provides guidelines for modifying and extending the Presence virtual teleportation application for Apple Vision Pro. It outlines the key areas where modifications can be made, best practices to follow, and the impact of changes on different components.

## Table of Contents
1. [Modifying Video Playback](#modifying-video-playback)
2. [Customizing the Immersive Experience](#customizing-the-immersive-experience)
3. [Extending the User Interface](#extending-the-user-interface)
4. [Adding New Features](#adding-new-features)
5. [Performance Considerations](#performance-considerations)
6. [Testing Modifications](#testing-modifications)

## Modifying Video Playback

The video playback functionality is primarily handled by the `VideoStreamManager` class. Here are the key areas for modification:

### Changing Video Sources

To modify how videos are loaded or to add support for different sources:

1. Locate the `playLocalVideo` method in `VideoStreamManager.swift`:
   ```swift
   func playLocalVideo(named name: String, withExtension ext: String) {
       cleanupCurrentPlayback()
       
       // Find the video URL...
       // Setup player...
   }
   ```

2. Modify or extend this method to support your additional sources:
   - For network streaming, enhance the `setupStream` method
   - For different local storage locations, modify the URL lookup logic
   - For different file formats, update the extension handling

### Modifying Playback Controls

To change how video playback is controlled:

1. Modify the relevant methods in `VideoStreamManager.swift`:
   ```swift
   func play() { ... }
   func pause() { ... }
   func togglePlayPause() { ... }
   func seek(to time: Double) { ... }
   ```

2. To add new control features (e.g., playback speed), add new methods and properties:
   ```swift
   // Example for adding playback speed control
   @Published var playbackRate: Float = 1.0
   
   func setPlaybackSpeed(_ rate: Float) {
       playbackRate = rate
       player?.rate = rate
   }
   ```

3. Update the `PlaybackControlsView.swift` UI to expose these new controls.

### Changing Video Processing

To modify how video frames are processed and displayed:

1. Modify the `VideoMaterial.swift` class, specifically the `updateTexture` method:
   ```swift
   func updateTexture(with pixelBuffer: CVPixelBuffer) {
       // Convert CVPixelBuffer to CIImage
       // Create a CIContext for rendering
       // Create a CGImage from the CIImage
       // Update the material's base color texture
   }
   ```

2. To add video effects or processing:
   - Add image processing using CIFilters before creating the texture
   - Modify the material properties for different visual effects
   - Consider adding parameters to control these effects

## Customizing the Immersive Experience

The immersive experience is handled by two main components: `Immersive360View.swift` and `ImmersiveView.swift`.

### Modifying the 360° Video Environment

To change how the 360° video environment is presented:

1. Modify the sphere creation in `Immersive360View.swift`:
   ```swift
   // Create a large sphere
   let sphere = ModelEntity(
       mesh: .generateSphere(radius: 100),
       materials: [SimpleMaterial(color: .blue, roughness: 0.0, isMetallic: false)]
   )
   
   // Make it visible from the inside
   sphere.scale = .init(x: -1, y: 1, z: 1)
   ```

2. Adjustments you can make:
   - Change the sphere radius for different perceived distances
   - Modify the initial material properties
   - Add additional geometry for reference points or decorative elements

### Modifying the RealityKit Content Experience

To change the RealityKit content-based experience:

1. Modify the content loading in `ImmersiveView.swift`:
   ```swift
   RealityView { content in
       // Add the initial RealityKit content
       if let immersiveContentEntity = try? await Entity(named: "Immersive", in: realityKitContentBundle) {
           content.add(immersiveContentEntity)
       }
   }
   ```

2. Possible modifications:
   - Use different named entities from your RealityKit content bundle
   - Programmatically modify the loaded entities
   - Add interaction handlers to the loaded content

### Switching Between Immersive Experiences

To modify how the application switches between different immersive experiences:

1. Update the immersive space definitions in your app structure:
   ```swift
   // In PresenceApp.swift
   ImmersiveSpace(id: "360Viewer") { ... }
   ImmersiveSpace(id: "ImmersiveContent") { ... }
   ```

2. Modify the space opening logic in your content view:
   ```swift
   Button("Open Experience") {
       Task {
           // Determine which space to open based on user selection or content type
           await openImmersiveSpace(id: selectedExperienceType)
       }
   }
   ```

## Extending the User Interface

The user interface is primarily defined in SwiftUI views like `ContentView.swift` and `PlaybackControlsView.swift`.

### Modifying the Main Interface

To change the main application interface:

1. Modify `ContentView.swift`:
   ```swift
   var body: some View {
       VStack {
           Text("360° Video Viewer")
               .font(.largeTitle)
               .padding()
           
           // Add or modify UI elements here
           
           Button(action: { ... }) {
               Text("Play 360° Video")
                   // Modify styling here
           }
       }
   }
   ```

2. Add new UI sections or controls as needed:
   - Video library browser
   - Settings panel
   - User account interface
   - Experience selection menu

### Customizing Playback Controls

To modify the playback control interface:

1. Update `PlaybackControlsView.swift`:
   ```swift
   var body: some View {
       VStack {
           // Progress bar
           GeometryReader { geometry in
               // Modify the progress bar appearance and behavior
           }
           
           // Playback time
           HStack {
               // Modify time display format or layout
           }
           
           // Control buttons
           HStack(spacing: 30) {
               // Add, remove, or modify control buttons
           }
           
           // Exit button
           Button(action: { ... }) {
               // Modify exit button appearance
           }
       }
   }
   ```

2. Considerations for modifications:
   - Keep controls at comfortable viewing distances
   - Ensure sufficient contrast against video backgrounds
   - Consider adding tooltips or help text for controls
   - Add animations for better user feedback

## Adding New Features

When adding new features to the Presence application, follow these guidelines:

### Adding New Video Sources

To add support for new video sources:

1. Extend the `VideoStreamManager` with new source handling:
   ```swift
   // Example for adding cloud storage support
   func playCloudVideo(identifier: String) {
       cleanupCurrentPlayback()
       
       // Implement cloud video fetching logic
       fetchCloudVideo(identifier) { url in
           guard let url = url else {
               print("Failed to fetch cloud video")
               return
           }
           
           // Use existing player setup with the retrieved URL
           self.player = AVPlayer(url: url)
           self.player?.play()
           self.isPlaying = true
           
           // Add a basic notification when playback ends
           NotificationCenter.default.addObserver(
               self,
               selector: #selector(self.playerDidFinishPlaying),
               name: .AVPlayerItemDidPlayToEndTime,
               object: self.player?.currentItem
           )
       }
   }
   ```

2. Add the necessary UI components to allow users to select these new sources.

### Adding Video Effects

To add video effects processing:

1. Extend the `VideoMaterial` class with effect capabilities:
   ```swift
   // Add an effects enum
   enum VideoEffect {
       case normal
       case grayscale
       case sepia
       // Add more effects as needed
   }
   
   // Add a current effect property
   private var currentEffect: VideoEffect = .normal
   
   // Add a method to set the current effect
   func setEffect(_ effect: VideoEffect) {
       currentEffect = effect
   }
   
   // Modify the updateTexture method to apply the selected effect
   func updateTexture(with pixelBuffer: CVPixelBuffer) {
       let ciImage = CIImage(cvPixelBuffer: pixelBuffer)
       
       // Apply the appropriate effect
       let processedImage: CIImage
       switch currentEffect {
       case .normal:
           processedImage = ciImage
       case .grayscale:
           let filter = CIFilter(name: "CIColorMonochrome")
           filter?.setValue(ciImage, forKey: kCIInputImageKey)
           filter?.setValue(CIColor(red: 0.7, green: 0.7, blue: 0.7), forKey: kCIInputColorKey)
           filter?.setValue(1.0, forKey: kCIInputIntensityKey)
           processedImage = filter?.outputImage ?? ciImage
       case .sepia:
           let filter = CIFilter(name: "CISepiaTone")
           filter?.setValue(ciImage, forKey: kCIInputImageKey)
           filter?.setValue(0.8, forKey: kCIInputIntensityKey)
           processedImage = filter?.outputImage ?? ciImage
       }
       
       // Continue with the existing texture creation process...
   }
   ```

2. Add UI controls to allow users to select different effects.

### Adding Multi-user Features

To prepare for adding multi-user features:

1. Create a new session management class:
   ```swift
   class SessionManager: ObservableObject {
       @Published var isConnected = false
       @Published var sessionID: String?
       @Published var connectedUsers: [String] = []
       
       func createSession() -> String {
           // Generate a unique session ID
           let newSessionID = UUID().uuidString
           sessionID = newSessionID
           isConnected = true
           return newSessionID
       }
       
       func joinSession(_ id: String) {
           // Implement session joining logic
           sessionID = id
           isConnected = true
       }
       
       func leaveSession() {
           sessionID = nil
           isConnected = false
           connectedUsers = []
       }
   }
   ```

2. Integrate this with the video playback system for synchronized experiences.

## Performance Considerations

When modifying the Presence application, be mindful of these performance considerations:

### Video Playback Performance

1. **Resolution and Format**:
   - High-resolution videos (4K+) may cause performance issues
   - Consider providing multiple resolution options
   - H.265/HEVC is more efficient but requires more processing power

2. **Texture Updates**:
   - Frequent texture updates can impact performance
   - Consider implementing frame dropping for smoother playback
   - Monitor memory usage during continuous updates

3. **Background Processing**:
   - Move intensive operations off the main thread
   - Use `DispatchQueue.global().async` for image processing
   - Update UI elements on the main thread

Example of optimized texture updating:
```swift
// Process frames on a background queue
DispatchQueue.global(qos: .userInteractive).async { [weak self] in
    guard let self = self, let pixelBuffer = pixelBuffer else { return }
    
    // Process the frame
    let processedImage = self.processPixelBuffer(pixelBuffer)
    
    // Update the UI on the main thread
    DispatchQueue.main.async {
        self.updateMaterialWithProcessedImage(processedImage)
    }
}
```

### Immersive Space Performance

1. **Scene Complexity**:
   - Keep the number of entities and complexity minimal
   - Use low-poly models for better performance
   - Consider level-of-detail (LOD) techniques for complex scenes

2. **Loading Times**:
   - Pre-load assets when possible
   - Implement loading screens for larger environments
   - Consider progressive enhancement techniques

## Testing Modifications

After making modifications, thoroughly test your changes:

### Simulator Testing

1. Basic functionality testing:
   - Test UI interactions and navigation
   - Verify video loading and playback controls
   - Check immersive space transitions

2. Performance testing:
   - Monitor frame rates in Debug Navigator
   - Check for memory leaks with Instruments
   - Test with different video resolutions

### Device Testing

1. **Apple Vision Pro testing**:
   - Test the full immersive experience
   - Verify comfort and usability of controls
   - Check for performance issues with real hardware

2. **Usability testing**:
   - Test with different user positions and environments
   - Verify readability of text and visibility of controls
   - Test for motion sickness or discomfort during extended use

### Debug Features

Use the existing debug features in the app to help with testing:

1. Debug overlays:
   ```swift
   Text(debugMessage)
       .foregroundColor(.white)
       .padding()
       .background(Color.black.opacity(0.7))
       .cornerRadius(8)
   ```

2. Add additional debug logging as needed:
   ```swift
   print("Video loaded successfully: \(url.lastPathComponent)")
   print("Current state: \(isPlaying ? "playing" : "paused"), time: \(currentTime)/\(duration)")
   ```
← [Development](development.md) | [Resources](../resources.md) →
