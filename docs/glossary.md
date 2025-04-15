
# Glossary

← [Resources](docs/resources.md)

## Technical Terms

### AVPlayerItemVideoOutput
A Core Video object that provides access to video frames as they are being rendered by an AVPlayerItem. This component serves as the bridge between AVFoundation's video playback infrastructure and custom rendering pipelines, allowing for real-time extraction of video frame data from 360° video content for processing in the Apple Vision Pro environment.

### PixelBuffer Extraction
The process of obtaining raw pixel data from video frames in the form of CVPixelBuffer objects. This critical step involves pulling uncompressed frame data from the AVPlayerItemVideoOutput, maintaining the correct timing and synchronization required for smooth virtual teleportation experiences. PixelBuffers are the container format that holds the actual image data needed for further processing.

### CIImage/CGImage Conversion
The transformation process between different Apple image formats:
- **CIImage**: A Core Image representation that supports real-time image processing and filtering operations.
- **CGImage**: A Core Graphics representation that provides compatibility with lower-level rendering systems.
This conversion step is essential for preparing video frame data for integration with RealityKit's texture system.

### TextureResource Creation
The generation of RealityKit-compatible texture resources from processed image data. TextureResources serve as the graphical assets that can be applied to 3D objects within the virtual environment, enabling the mapping of 360° video content onto immersive surroundings that create the teleportation effect.

### RealityKit ModelEntity
A fundamental 3D object within Apple's RealityKit framework that represents a renderable component in a virtual scene. In the context of virtual teleportation, ModelEntities provide the structural elements onto which 360° video textures are applied, creating the immersive environment that surrounds the user during the teleportation experience.

### CADisplayLink Trigger
A timer object synchronized with the display refresh rate of the device. CADisplayLink ensures that video frame updates and rendering operations are precisely timed with the screen's refresh cycle, creating smooth motion and preventing visual artifacts. This component serves as the central timing mechanism for the virtual teleportation rendering pipeline.

### Encoding Pipeline
The end-to-end process of compressing and preparing video data for transmission or storage. In the context of virtual teleportation, the encoding pipeline refers to the series of operations that convert raw camera input from one location into a format suitable for transmission to and rendering on a remote user's Apple Vision Pro device. This pipeline involves various compression algorithms, metadata management, and optimization techniques to balance quality, bandwidth requirements, and latency.
