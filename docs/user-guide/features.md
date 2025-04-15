# Presence Features

This document outlines the features available in the Presence virtual teleportation application for Apple Vision Pro.

## Table of Contents
1. [360° Video Playback](#360-video-playback)
2. [Immersive Experiences](#immersive-experiences)
3. [Playback Controls](#playback-controls)
4. [Interface Navigation](#interface-navigation)
5. [Video Management](#video-management)

## 360° Video Playback

### Equirectangular Video Projection

Presence supports the playback of 360° video content in equirectangular format, providing a fully immersive viewing experience.

- **Full Sphere Projection**: Videos are projected onto a spherical environment that surrounds the user in all directions
- **Inside-Out Viewing**: Users view the content from inside the sphere, creating the sensation of being teleported to the recorded location
- **High-Resolution Support**: The application supports 4K (3840×2160) 360° video content

### Local Video Library

The application includes a selection of pre-packaged 360° video environments:

- **Cable Car Experience**: Immerse yourself in a cable car journey with panoramic views
- **Additional Environments**: More environments can be added to the application bundle

## Immersive Experiences

### Full Immersive Mode

Presence utilizes visionOS's immersive spaces to provide a fully immersive teleportation experience:

- **Environmental Takeover**: When entering the immersive space, the 360° video environment replaces your surroundings
- **Head Tracking**: Look in any direction to explore different parts of the 360° environment
- **Spatial Positioning**: The environment maintains proper orientation as you move your head

### RealityKit Content Integration

Besides 360° video environments, the application also supports custom RealityKit content:

- **3D Model Integration**: Custom 3D models and environments can be loaded from a RealityKit content bundle
- **Mixed Content**: Combine real-world elements with virtual content for mixed reality experiences

## Playback Controls

### Video Transport Controls

The application provides a comprehensive set of video playback controls:

- **Play/Pause**: Toggle video playback with a single button press
- **Seek Forward/Backward**: Skip forward or backward by 10 seconds
- **Progress Bar**: Visual indication of current playback position
- **Duration Display**: Shows current time and total duration in minutes:seconds format

### User-Friendly Interface

The playback controls are designed for ease of use in an immersive environment:

- **Floating Panel**: Controls appear on a floating panel positioned for comfortable viewing
- **High Contrast**: Clear visual design ensures controls are visible against any video background
- **Large Touch Targets**: Buttons are sized appropriately for easy interaction in a spatial environment

### Playback Progress

Track your position in the video with intuitive progress indicators:

- **Interactive Scrubber**: Drag the playhead to any position in the video
- **Time Display**: Current position and total duration are clearly displayed
- **Visual Progress Bar**: See at a glance how much of the video has been played

## Interface Navigation

### Home Screen

The main interface provides a simple entry point to the experience:

- **Video Selection**: Choose from available video environments
- **Status Information**: See the current state of the application
- **Large, Clear Buttons**: Easy-to-use interface designed for spatial interaction

### Immersive Space Transitions

Move between the standard interface and immersive experiences:

- **Enter Immersive Mode**: Launch the 360° experience with a single button press
- **Exit Controls**: Easily return to the main interface at any time
- **Smooth Transitions**: Fluid movement between interface states

## Video Management

### Video Playback Engine

The application includes a robust video playback system:

- **Format Support**: Compatible with standard MP4 video files using H.264/HEVC encoding
- **Automatic File Handling**: Intelligent file detection with support for various naming conventions
- **Resource Management**: Efficient handling of video resources to maintain performance

### Debug Information

For users interested in technical details, the application provides:

- **Status Messages**: Visual feedback about video loading and playback state
- **Error Reporting**: Clear indications when issues occur with file loading or playback
- **Playback Status**: Real-time information about the current state of the video player

← [Getting Started](getting-started.md) | [Troubleshooting](troubleshooting.md) →
