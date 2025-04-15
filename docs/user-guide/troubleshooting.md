# Troubleshooting Guide

This guide addresses common issues that users and developers might encounter with the Presence virtual teleportation application for Apple Vision Pro, along with detailed solutions.

## Table of Contents
1. [User Troubleshooting](#user-troubleshooting)
   - [Application Issues](#application-issues)
   - [Video Playback Issues](#video-playback-issues)
   - [Immersive Experience Issues](#immersive-experience-issues)
   - [Control Issues](#control-issues)
2. [Developer Troubleshooting](#developer-troubleshooting)
   - [Xcode Build Issues](#xcode-build-issues)
   - [Runtime Debugging](#runtime-debugging)
   - [Video Resource Issues](#video-resource-issues)
   - [Performance Analysis](#performance-analysis)

## User Troubleshooting

### Application Issues

#### Application Won't Launch

**Symptoms**:
- App icon appears briefly then disappears
- App crashes immediately after launch
- App seems to freeze on the loading screen

**Solutions**:
1. **Restart your Apple Vision Pro**:
   - Press and hold the top button and either volume button until the sliders appear
   - Drag the power slider to turn off your device
   - After the device turns off, press and hold the top button to restart

2. **Check for Updates**:
   - Ensure visionOS is updated to the latest version
   - Check if a Presence app update is available in the App Store

3. **Reinstall the Application**:
   - Delete the Presence app by holding the app icon and selecting "Remove App"
   - Reinstall from the App Store

#### Application Runs Slowly

**Symptoms**:
- User interface feels sluggish
- Transitions between screens are delayed
- General performance issues

**Solutions**:
1. **Close Background Applications**:
   - Use the App Switcher to close other running applications
   - Restart your Apple Vision Pro

2. **Check Storage Space**:
   - Go to Settings > General > Storage
   - Ensure you have at least 1GB of free space

3. **Reset visionOS Spatial Preferences**:
   - Go to Settings > Privacy & Security > Spatial Experiences
   - Reset spatial mapping data

### Video Playback Issues

#### Video Won't Load

**Symptoms**:
- "Loading..." message persists
- Black screen when attempting to play video
- Error message about missing video file

**Solutions**:
1. **Check the Debug Message**:
   - Look for specific error messages on the screen
   - If it shows "ERROR: Could not find Cable Car.mp4", the application may need to be reinstalled

2. **Restart the Experience**:
   - Exit the immersive experience and try again
   - Force close the app and relaunch

3. **Check Network Connection** (for future streaming features):
   - Ensure your device has a stable Wi-Fi connection
   - Try switching to another network if available

#### Video Playback Is Choppy

**Symptoms**:
- Stuttering or jerky video playback
- Frame drops during movement
- Audio/video synchronization issues

**Solutions**:
1. **Reduce Device Load**:
   - Close other applications
   - Ensure your device isn't overheating

2. **Wait for Initial Buffering**:
   - Allow a few seconds after the video starts before moving your head quickly
   - The first few frames may be slower to load

3. **Check Video Resolution**:
   - Higher resolution videos (4K+) may cause performance issues on some devices
   - Contact the developer if persistent issues occur

### Immersive Experience Issues

#### Can't Enter Immersive Mode

**Symptoms**:
- Button to enter immersive mode doesn't respond
- App freezes when attempting to enter immersive mode
- Returns to home screen immediately

**Solutions**:
1. **Check Permissions**:
   - Ensure Presence has permission to use immersive features
   - Go to Settings > Privacy & Security > Immersive Experiences
   - Toggle permission for Presence

2. **Restart the App**:
   - Force close and relaunch the application
   - Try again after a device restart

3. **Check System Resources**:
   - Ensure no other apps are using immersive spaces
   - Close background applications

#### Distorted Visuals in Immersive Mode

**Symptoms**:
- Warped or stretched video
- Visible seams or tears in the 360° environment
- Incorrect proportions or alignment

**Solutions**:
1. **Reset Your View**:
   - Exit and re-enter the immersive experience
   - Ensure you're centered in your physical space

2. **Check for Physical Obstructions**:
   - Make sure the Vision Pro sensors aren't blocked
   - Clean the external cameras if necessary

3. **Calibrate Your Device**:
   - Go to Settings > Accessibility > Vision > Eye Tracking
   - Recalibrate your eye tracking

### Control Issues

#### Playback Controls Not Responding

**Symptoms**:
- Tapping or pinching on controls doesn't work
- Progress bar can't be dragged
- Play/pause button unresponsive

**Solutions**:
1. **Check Hand Tracking**:
   - Ensure your hands are visible to the device
   - Try different lighting conditions if necessary

2. **Alternative Input Methods**:
   - Try using a connected Bluetooth controller if available
   - Use voice commands if supported

3. **Control Panel Focus**:
   - Look directly at the control you want to use
   - Ensure your pinch gesture is completed within the control's boundaries

#### Can't Exit Immersive Experience

**Symptoms**:
- Exit button doesn't respond
- Stuck in the immersive environment
- Default visionOS gestures not working

**Solutions**:
1. **Use System Gesture**:
   - Touch your thumb and index finger together and hold
   - When the home indicator appears, select it

2. **Force Close the App**:
   - If all else fails, use the system App Switcher
   - Swipe the app card up to force close

3. **Restart if Necessary**:
   - If you're completely stuck, restart your Apple Vision Pro
   - Press and hold the top button and either volume button

## Developer Troubleshooting

### Xcode Build Issues

#### Build Fails with visionOS SDK Errors

**Symptoms**:
- Compiler errors related to visionOS APIs
- Missing framework references
- Incompatible Swift version warnings

**Solutions**:
1. **Check Xcode and SDK Versions**:
   ```bash
   xcodebuild -version
   xcrun --sdk -version
   ```
   - Ensure you're using Xcode 15.0 or later
   - Verify the visionOS SDK is properly installed

2. **Clean the Build Folder**:
   - In Xcode, select Product > Clean Build Folder (Shift+Cmd+K)
   - Try building again

3. **Check Target Settings**:
   - Verify the deployment target is set correctly
   - Ensure "Supports Immersive Space" is enabled in the target settings

#### Code Signing Issues

**Symptoms**:
- "Failed to code sign" errors
- Provisioning profile warnings
- Unable to install on device

**Solutions**:
1. **Check Developer Account**:
   - Verify your Apple Developer account is active
   - Ensure your device is registered in your developer account

2. **Update Signing Configuration**:
   - In Xcode, go to the target's Signing & Capabilities tab
   - Select your team and let Xcode manage signing automatically
   - If using manual signing, verify provisioning profiles

3. **Troubleshoot with Terminal**:
   ```bash
   xcrun security find-identity -v -p codesigning
   ```
   - Verify your signing certificates are properly installed

### Runtime Debugging

#### Identifying Video Loading Issues

**Symptoms**:
- "Could not find video" errors in console
- Black sphere instead of video content
- App hangs during video initialization

**Solutions**:
1. **Debug Video File Path**:
   - Add the following code to `playLocalVideo` in VideoStreamManager:
   ```swift
   print("Searching for video: \(name).\(ext)")
   print("Bundle path: \(Bundle.main.bundlePath)")
   print("Available videos: \(Bundle.main.paths(forResourcesOfType: ext, inDirectory: nil))")
   ```

2. **Check Bundle Resources**:
   - In Xcode, verify the video is properly added to the target
   - Right-click the video file in Xcode and select "Show File Inspector"
   - Ensure "Target Membership" includes your app

3. **Test with Sample Video**:
   - Replace your video with a known working sample
   - Add a small test video directly in Xcode
   - Modify the code to use the test video:
   ```swift
   // In ContentView.swift
   videoStreamManager.playLocalVideo(named: "TestVideo", withExtension: "mp4")
   ```

#### Debugging Immersive View Issues

**Symptoms**:
- Immersive space won't open
- RealityKit content not appearing
- Sphere visible but without video texture

**Solutions**:
1. **Add Debug Overlays**:
   - Enhance the debug message display in Immersive360View:
   ```swift
   Text(debugMessage)
       .foregroundColor(.white)
       .padding()
       .background(Color.black.opacity(0.7))
       .cornerRadius(8)
       .position(x: 200, y: 200) // Position for visibility
   ```

2. **Debug Entity Creation**:
   - Add detailed logging to the RealityView content creation:
   ```swift
   RealityView { content in
       do {
           let sphere = ModelEntity(
               mesh: .generateSphere(radius: 100),
               materials: [SimpleMaterial(color: .blue, roughness: 0.0, isMetallic: false)]
           )
           sphere.scale = .init(x: -1, y: 1, z: 1)
           content.add(sphere)
           print("Sphere created and added successfully")
       } catch {
           print("Failed to create entity: \(error)")
       }
   }
   ```

3. **Test with Xcode Debugger**:
   - Set breakpoints in key functions like `playVideo` and `applyVideoTexture`
   - Inspect variables at runtime to identify the source of issues
   - Use the Debug Navigator to monitor memory and CPU usage

### Video Resource Issues

#### Diagnosing Texture Creation Problems

**Symptoms**:
- Video loads but doesn't appear on the sphere
- Texture creation fails
- Material updates not visible

**Solutions**:
1. **Debug Texture Creation Process**:
   - Add detailed logging to the `createTexture` method in Immersive360View:
   ```swift
   private func createTexture(from pixelBuffer: CVPixelBuffer) throws -> TextureResource {
       let ciImage = CIImage(cvPixelBuffer: pixelBuffer)
       let context = CIContext(options: nil)
       
       print("Creating texture from pixel buffer: \(CVPixelBufferGetWidth(pixelBuffer))x\(CVPixelBufferGetHeight(pixelBuffer))")
       
       guard let cgImage = context.createCGImage(ciImage, from: ciImage.extent) else {
           print("Failed to create CGImage")
           throw NSError(domain: "Failed to create CGImage", code: 2)
       }
       
       print("CGImage created successfully")
       
       do {
           let texture = try TextureResource(image: cgImage, options: .init(semantic: .color))
           print("TextureResource created successfully")
           return texture
       } catch {
           print("Failed to create TextureResource: \(error)")
           throw error
       }
   }
   ```

2. **Test with Different Video Formats**:
   - Convert your video to different resolutions and formats
   - Try both H.264 and HEVC encoded videos
   - Test with square power-of-two textures if possible (e.g., 2048x1024)

3. **Validate with Static Image**:
   - Replace video with a static image texture for testing:
   ```swift
   if let image = UIImage(named: "TestPattern360"), let cgImage = image.cgImage {
       let texture = try? TextureResource(image: cgImage, options: .init(semantic: .color))
       if let texture = texture {
           var material = SimpleMaterial()
           material.color = .init(texture: .init(texture))
           sphere.model?.materials = [material]
       }
   }
   ```

#### Optimizing Video Performance

**Symptoms**:
- High CPU/GPU usage
- Frame drops during playback
- Memory warnings or crashes during long sessions

**Solutions**:
1. **Profile with Instruments**:
   - In Xcode, select Product > Profile (Cmd+I)
   - Choose the "Time Profiler" instrument
   - Launch on your device and capture performance data
   - Look for hot spots in the video processing code

2. **Optimize Frame Extraction**:
   - Add frame dropping logic to the VideoUpdater:
   ```swift
   private var lastFrameTime: CFTimeInterval = 0
   
   @objc private func updateFrame() {
       let currentTime = CACurrentMediaTime()
       // Only process frames at 30fps max
       if currentTime - lastFrameTime < 0.033 {
           return
       }
       lastFrameTime = currentTime
       
       // Rest of the update logic...
   }
   ```

3. **Monitor Memory Usage**:
   - Add memory tracking to help diagnose leaks:
   ```swift
   func printMemoryUsage() {
       var info = mach_task_basic_info()
       var count = mach_msg_type_number_t(MemoryLayout<mach_task_basic_info>.size)/4
       
       let kerr: kern_return_t = withUnsafeMutablePointer(to: &info) {
           $0.withMemoryRebound(to: integer_t.self, capacity: Int(count)) {
               task_info(mach_task_self_,
                         task_flavor_t(MACH_TASK_BASIC_INFO),
                         $0,
                         &count)
           }
       }
       
       if kerr == KERN_SUCCESS {
           print("Memory in use (MB): \(info.resident_size / 1024 / 1024)")
       }
   }
   ```

### Performance Analysis

#### Frame Rate Analysis

**Symptoms**:
- User reports of stuttering
- Inconsistent playback speed
- Slow response to head movements

**Solutions**:
1. **Add FPS Counter**:
   - Implement a simple FPS counter in Immersive360View:
   ```swift
   @State private var frameTime: Double = 0
   @State private var frameCount: Int = 0
   @State private var fps: Double = 0
   
   // Add to the body
   .onAppear {
       Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { _ in
           fps = Double(frameCount)
           frameCount = 0
       }
   }
   
   // Update in your display link or frame update
   frameCount += 1
   
   // Display in your UI
   Text("FPS: \(Int(fps))")
       .foregroundColor(.white)
       .padding(4)
       .background(Color.black.opacity(0.7))
       .cornerRadius(4)
       .position(x: 100, y: 50)
   ```

2. **Use Xcode Gauges**:
   - Enable the FPS gauge in Xcode's Debug Navigator
   - Monitor CPU, GPU, and memory usage in real-time
   - Look for correlations between drops in FPS and specific operations

3. **Profile with Metal System Trace**:
   - Use Instruments with the Metal System Trace template
   - Identify GPU bottlenecks and texture upload delays
   - Look for frame timing issues

#### Memory Leak Detection

**Symptoms**:
- Increasing memory usage over time
- Performance degradation during extended sessions
- App crashes after running for a while

**Solutions**:
1. **Use Instruments Memory Graph**:
   - In Xcode, select Debug > Debug Workflow > View Memory Graph
   - Look for objects with unexpected retain counts
   - Check for reference cycles involving the VideoStreamManager, CADisplayLink, or AVPlayer

2. **Add Deallocation Logging**:
   - Add print statements to deinit methods to verify cleanup:
   ```swift
   deinit {
       print("VideoStreamManager being deallocated")
       cleanupCurrentPlayback()
   }
   ```

3. **Test with Repeated Scene Changes**:
   - Create a test that repeatedly opens and closes the immersive space
   - Monitor memory usage to detect leaks
   - Look for objects that aren't being released properly

By following these troubleshooting steps, both users and developers should be able to identify and resolve most issues with the Presence application.

← [Features](features.md) | [Architecture](../implementation/architecture.md) →
