# System Architecture

## Overview

The Presence virtual teleportation system creates immersive shared experiences using Apple Vision Pro (AVP) headsets. The architecture allows users to virtually transport into another user's physical environment through 360° video playback. The current implementation focuses on pre-recorded 360° videos loaded from the app bundle, with planned expansion to real-time streaming capabilities.

## System Components

### 1. Hardware Components

- **Apple Vision Pro Headsets**: Primary user interface devices with visionOS
- **360° Cameras**: Used offline to capture immersive video content
- **Development Mac**: For developing and loading content into the application

### 2. Software Architecture

The software architecture is built on SwiftUI and RealityKit, utilizing Apple's native frameworks:

```
┌───────────────────────────────────────┐
│           SwiftUI Interface           │
├───────────────────────────────────────┤
│            RealityKit View            │
├───────────────────────────────────────┤
│ AVFoundation Media │ Spatial Rendering│
├─────────────────┼─────────────────────┤
│  Bundle Assets  │  Resource Management│
└─────────────────┴─────────────────────┘
```

#### 2.1 SwiftUI Interface Layer

- **SwiftUI Components**: Immersive space management and controls
- **User Controls**: Basic navigation buttons (Exit) and debug information display
- **Environment Management**: Handling immersive space lifecycle (opening/dismissing)

#### 2.2 RealityKit View Layer

- **RealityView**: Container for 3D content rendering
- **ModelEntity**: Sphere mesh for projecting 360° video content
- **Material Handling**: Dynamic material updates for video projection

#### 2.3 Media Processing

- **AVFoundation Integration**:
  - AVPlayer: Manages video playback from bundle resources
  - AVPlayerItemVideoOutput: Extracts frames for texture generation
  - DisplayLink-based Frame Updates: Real-time texture mapping

- **Texture Processing**:
  - CIImage/CGImage Processing: Converting video frames to textures
  - TextureResource Creation: Converting frame data for RealityKit consumption
  - Material Updates: Dynamically replacing sphere materials with new frames

#### 2.4 Resource Management

- **Bundle Assets**: 
  - Pre-packaged 360° video content (e.g., "Cable Car.mp4")
  - Future: Expanded library of environments

- **Memory Management**:
  - Reference Management: Preventing premature deallocation of critical components
  - Frame Processing: Efficient handling of video frame updates

## Data Flow

### Current Implementation (Bundle-based Video Playback)

```
┌────────────┐    ┌────────────────┐    ┌──────────────────┐    ┌────────────┐
│ App Bundle │───▶│ AVPlayer       │───▶│ AVPlayerItem     │───▶│ Video      │
│ 360° Video │    │ Initialization │    │ VideoOutput      │    │ Processing │
└────────────┘    └────────────────┘    └──────────────────┘    └────────────┘
                                                                       │
                                                                       ▼
┌────────────┐    ┌────────────────┐    ┌──────────────────┐    ┌────────────┐
│ RealityKit │◀───│ TextureResource│◀───│ CIImage/CGImage  │◀───│ PixelBuffer│
│ ModelEntity│    │ Creation       │    │ Conversion       │    │ Extraction │
└────────────┘    └────────────────┘    └──────────────────┘    └────────────┘
      │
      ▼
┌────────────┐
│ User View  │
│ Experience │
└────────────┘
```

### Frame Update Cycle

```
┌────────────┐    ┌────────────────┐    ┌────────────────┐    ┌────────────┐
│ CADisplayL-│───▶│ Check for New  │───▶│ Extract New    │───▶│ Create New │
│ ink Trigger│    │ Frame Available│    │ Pixel Buffer   │    │ Texture    │
└────────────┘    └────────────────┘    └────────────────┘    └────────────┘
                                                                     │
                                                                     ▼
                      ┌────────────────┐             ┌────────────────┐
                      │ Update User    │◀────────────│ Update Sphere  │
                      │ View           │             │ Material       │
                      └────────────────┘             └────────────────┘
```

### Future Implementation (Live Streaming)

```
┌───────────┐    ┌──────────┐    ┌───────────┐    ┌──────────────┐    ┌─────────────┐
│ 360°      │───▶│ Encoding │───▶│ Streaming │───▶│ AVP Headset  │───▶│ User View   │
│ Camera    │    │ Pipeline │    │ Service   │    │ Processing   │    │ Experience  │
└───────────┘    └──────────┘    └───────────┘    └──────────────┘    └─────────────┘
```

## Technical Specifications

### Video Processing

- **Resolution**: Standard 4K (3840×2160) 360° video
- **Frame Rate**: 30-60 FPS target
- **Encoding**: H.264/AVC or H.265/HEVC formats supported by AVFoundation
- **Field of View**: 360° spherical video

### Performance Considerations

- **Texture Updates**: Using CADisplayLink for synchronized frame updates
- **Memory Management**: Careful handling of video resources to prevent leaks
- **Rendering Pipeline**: Efficient texture generation and application

### Storage

- **App Bundle**: Packaged 360° videos (current implementation)
- **Future Expansion**: 
  - On-device storage for downloaded environments
  - Streaming infrastructure for remote content

## Core Implementation Components

### Key Classes and Components

1. **Immersive360View**: Main SwiftUI view for the immersive experience
   - Manages immersive space lifecycle
   - Handles user interface elements
   - Coordinates video playback

2. **VideoUpdater**: Helper class for video frame processing
   - Maintains CADisplayLink for frame synchronization
   - Extracts video frames from AVPlayerItemVideoOutput
   - Creates and applies textures to the sphere entity

3. **RealityKit Integration**:
   - ModelEntity: Inverted sphere for video projection
   - SimpleMaterial: Dynamic material updates for video frames
   - TextureResource: Conversion of video frames to RealityKit textures

## Framework Integration Points

- **visionOS**: Base operating system for Apple Vision Pro
- **SwiftUI**: User interface development
- **RealityKit**: 3D content management and rendering
- **AVFoundation**: Media playback and processing

## Current Implementation Details

The current implementation focuses on successfully loading and playing a 360° video from the app bundle on a spherical model entity in an immersive space:

1. **Video Loading**:
   ```swift
   guard let videoURL = Bundle.main.url(forResource: "Cable Car", withExtension: "mp4") else {
       debugMessage = "ERROR: Could not find Cable Car.mp4"
       return
   }
   ```

2. **Sphere Creation**:
   ```swift
   let sphere = ModelEntity(
       mesh: .generateSphere(radius: 100),
       materials: [SimpleMaterial(color: .blue, roughness: 0.0, isMetallic: false)]
   )
   
   // Make it visible from the inside
   sphere.scale = .init(x: -1, y: 1, z: 1)
   ```

3. **Video Processing**:
   ```swift
   // Create a separate task for video setup
   Task {
       do {
           try await applyVideoTexture(to: sphere, with: player)
           player.play()
       } catch {
           // Handle errors
       }
   }
   ```

4. **Frame Updates**:
   ```swift
   // Setup a timer to update frames
   let videoUpdater = VideoUpdater(entity: sphere, output: videoOutput)
   videoUpdater.startUpdating()
   ```

## Future Architecture Enhancements

1. **Real-time Streaming Integration**:
   - HLS or DASH adaptive streaming support
   - WebRTC for real-time communication
   - Network quality management and adaptation

2. **Multi-user Experience**:
   - Shared immersive spaces
   - User representation (avatars or visual indicators)
   - Synchronized playback between users

3. **Enhanced Content Management**:
   - Library of environments with metadata
   - Content download/caching system
   - User-generated content support

4. **Improved User Interface**:
   - Enhanced navigation and controls
   - Environment selection interface
   - User presence and status indicators

## Implementation Challenges

- **Performance Optimization**: Ensuring smooth frame rates while processing high-resolution video
- **Memory Management**: Preventing memory leaks with continuous texture updates
- **Video Projection Quality**: Addressing distortion and seam issues in 360° projection
- **User Comfort**: Preventing motion sickness and discomfort during extended use

## System Requirements

### Development Requirements

- Xcode 16.0 or later
- visionOS SDK
- macOS Sequoia or later
- Apple Developer Program membership

### Deployment Requirements

- Apple Vision Pro headset
- visionOS 2.0 or later
- Sufficient storage for bundled video assets

## Architecture Decisions and Rationale

1. **Bundle-based Content First**: Simplifies initial development by eliminating networking complexity and ensuring reliable playback before adding streaming features.
2. **RealityKit with Manual Texture Updates**: Provides direct control over the video rendering process while leveraging Apple's optimized rendering pipeline.
3. **Task-based Asynchronous Processing**: Utilizes Swift's modern concurrency model for better performance and code organization.
4. **Component-based Architecture**: Separates video processing logic (VideoUpdater) from view components for better maintainability.

← [Architecture](architecture.md) | [Modification](modification.md) →
