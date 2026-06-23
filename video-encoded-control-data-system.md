# Video-Encoded Real-Time Control Data System

**Author:** Cameron Donlon

**Publication Date:** June 23, 2026

## Intent of This Publication

This document is published as a defensive publication to establish prior art and place the described methods, systems, and architectures into the public domain. The intent is to prevent any individual, organization, or entity from obtaining patent rights over the concepts described herein. All methods described are hereby dedicated to the public.

## Abstract

This publication describes a system and method for encoding multi-channel real-time control data — including but not limited to servo motor positions, DMX lighting values, MIDI commands, stepper motor instructions, relay states, pneumatic triggers, and other motion or automation data — directly onto standard video frames. The encoded video is played back through any standard video playback device and transmitted via standard video output interfaces including but not limited to HDMI, DVI, DisplayPort, VGA, SDI, and composite video. A receiving device captures the video signal, decodes the embedded control data from the pixel information within the video frame, and dispatches the decoded commands in real time to connected hardware via any suitable protocol including but not limited to UDP, Art-Net, sACN, DMX512, MIDI, serial, SPI, or I2C.

The core novelty of this system is the use of video frames — specifically the color, brightness, luminance, and chrominance values of defined pixel regions within each frame — as a carrier medium for arbitrary real-time control data. This method allows standard, commercially available video playback hardware to serve as a show control distribution system without modification, and allows the control network receiving decoded commands to remain physically isolated from any external network by virtue of the video signal being the sole data path between the playback system and the control hardware.

## Plain Language Summary

In simple terms: this system hides control instructions for motors, lights, and other devices inside a standard video file. Each frame of the video contains not just a visual image, but also hidden data encoded in the color and brightness values of specific regions of pixels within that frame. A small decoding device receives the video signal, reads those pixel values, and translates them back into real-time commands that are sent to motors, servo controllers, lighting fixtures, and other devices.

Because the data travels as a standard video signal — the same kind of signal used to connect a computer to a monitor or a Blu-ray player to a television — any reliable video playback device can be used as the control data source. This includes media players, computers, digital signage players, and other devices. The audio track of the video can play normally alongside the encoded control data, keeping sound and motion in perfect synchronization.

The control hardware receiving the decoded commands can be connected to a completely separate, isolated network with no connection to the internet or any external system. The video cable becomes the only bridge between the playback world and the control world, creating a physically air-gapped control network by design.

---

## 1. Field of the Invention

This publication relates to the fields of show control, animatronics, theatrical automation, lighting control, industrial automation, themed entertainment, interactive installations, museum exhibits, live event production, and any application requiring the synchronized playback of multi-channel real-time control data alongside audio and visual media. The methods described are applicable to any system in which control commands must be distributed to multiple endpoints in synchronization with a media timeline.

## 2. Background

Existing show control systems typically distribute control data over dedicated IP networks, serial buses, or proprietary hardware. These approaches require separate infrastructure for control data distribution, introduce potential network security vulnerabilities, and require synchronization mechanisms to keep control data aligned with audio and visual playback. Many such systems are platform-specific, requiring proprietary software or hardware to author and play back show content.

A need exists for a method of distributing real-time control data that leverages widely available, reliable, and inexpensive video playback infrastructure; that inherently synchronizes control data with audio and visual content without requiring external synchronization mechanisms; and that physically isolates control hardware from external networks without requiring additional security measures.

## 3. Summary of the Disclosure

This disclosure describes a system comprising:

* A method of encoding real-time control data into the pixel values of defined regions within standard video frames, such that the encoded data is carried by the video signal transparently alongside any visual content occupying other regions of the frame, or across the entire frame if no visual content is required.
* A method of playing back such encoded video using any standard video playback device and transmitting the video signal via any standard video output interface.
* A method of capturing the transmitted video signal using any suitable capture device and decoding the control data from the pixel values of the defined regions in real time.
* A method of dispatching the decoded control data to connected hardware via any suitable protocol.
* A method of synchronizing the decoded control data with the audio track of the video using fixed or calibrated timing offsets baked into the video file at encode time.
* A method of verifying frame validity using reserved pixel regions containing known marker values, preventing accidental activation of connected hardware from arbitrary video content.
* A method of physically isolating the control hardware network from any external network by using the video signal as the sole data path between the playback system and the control hardware.
* Extension of the above methods to other carrier media including audio signals, network video streams, optical media, and other transmission mediums.
* Applications include, but are not limited to, the control of animatronics for theme parks, automated logistics and warehouse systems, and integrated motion control for passenger ride systems.

## 4. Detailed Description

### 4.1 Quadrant-Based Pixel Data Encoding

#### 4.1.1 Definition of Quadrants

The fundamental data unit of this system is a quadrant — a defined rectangular region of pixels within a video frame. A quadrant is defined by its position within the frame (expressed as pixel coordinates of its top-left corner), its width in pixels, and its height in pixels. Quadrants may be of any size, though uniform sizes such as 16x16 pixels, 8x8 pixels, or 32x32 pixels are preferred for efficient processing.

A quadrant encodes data through the color, brightness, luminance, and chrominance values of the pixels it contains. Each pixel in a quadrant contributes one or more bytes of data depending on the color depth and encoding scheme in use. In an 8-bit per channel RGB color space, each pixel encodes three bytes: one each for the red, green, and blue channel values. In a grayscale encoding scheme, each pixel encodes a single byte representing luminance. In a YCbCr color space, the luma (Y) channel may be used independently of the chroma channels to encode data with maximum compatibility across video processing pipelines.

#### 4.1.2 Quadrant Grid Layout

The full frame is divided into a grid of quadrants. For a 1920x1080 pixel frame using 16x16 pixel quadrants, the grid is 120 quadrants wide by 67 quadrants tall, yielding 8,040 quadrants per frame. For a 3840x2160 pixel frame using the same quadrant size, the grid is 240 quadrants wide by 135 quadrants tall, yielding 32,400 quadrants per frame. The total data capacity per frame in bytes equals the number of quadrants multiplied by the number of pixels per quadrant multiplied by the number of bytes per pixel under the encoding scheme in use.

Quadrant grids may cover the entire frame or may occupy only a portion of the frame, allowing visual content to occupy the remaining frame area. Partial-frame encoding allows a single video file to simultaneously carry visible imagery and encoded control data without interference between the two.

#### 4.1.3 Data Encoding in Pixel Values

Control data values are mapped directly to pixel channel values within each quadrant. In the simplest encoding scheme, a single byte of control data is encoded as the luminance or red channel value of a single pixel, with values ranging from 0 to 255 representing the full range of the control parameter. Multi-byte values may be encoded across multiple pixels or across multiple color channels of a single pixel.

Encoding may use any or all of the following pixel properties as data carriers:

* Red channel value (8-bit, 0-255)
* Green channel value (8-bit, 0-255)
* Blue channel value (8-bit, 0-255)
* Luminance value (Y channel in YCbCr, 8-bit or 10-bit)
* Chrominance values (Cb and Cr channels in YCbCr)
* Alpha channel value where supported
* Combined multi-channel values for extended precision

The encoding scheme may use lossless video codecs to preserve exact pixel values, or may apply error correction techniques to recover data from frames compressed with lossy codecs. The preferred encoding uses lossless or near-lossless video compression to ensure data integrity through the playback and transmission pipeline.

#### 4.1.4 Frame Capacity Examples

The following table illustrates approximate raw data capacity at common video resolutions using pixel quadrants and one byte per quadrant or quadrants including multiple pixel x pixel densities:

| Resolution | Quadrants/Frame | Bytes/Frame | Bytes/Sec @30fps |
| --- | --- | --- | --- |
| 720p (1280x720) | 3,600 | 3,600 | 108,000 |
| 1080p (1920x1080) | 8,040 | 8,040 | 241,200 |
| 4K (3840x2160) | 32,400 | 32,400 | 972,000 |

Alternative data formats may include various resolutions, ranging from 480p to 8k.

### 4.2 Supported Control Data Types

The system is not limited to any specific type of control data. Any data that can be represented as a sequence of bytes may be encoded into the video frame quadrant regions. Supported data types include but are not limited to:

* Servo motor position values (e.g., pulse width values for RC-style servo controllers, Dynamixel protocol position commands, Pololu Maestro targets, SSC-32 pulse commands)
* DMX512 channel values for lighting fixtures, dimmers, fog machines, hazers, strobe lights, LED controllers, moving head fixtures, and any other DMX-addressable device
* Art-Net and sACN universe data (one or more complete DMX universes per frame)
* MIDI note, velocity, control change, program change, and SysEx messages
* Stepper motor step and direction commands
* DC motor speed and direction values
* Pneumatic valve open/close states
* Relay and switch states
* PWM values for any PWM-controlled device
* Trigger and event flags for show control cue systems
* Projection system cue commands
* Audio routing and mixing commands
* Any arbitrary binary data stream requiring frame-synchronized delivery

### 4.3 Video Playback and Transmission

The encoded video file is played back using any standard video playback device. Suitable playback devices include but are not limited to: digital signage media players, computers, single-board computers, Blu-ray players, DVD players, streaming media devices, video servers, and any other device capable of playing back a standard video file format.

The video signal is transmitted from the playback device to the decoding device via any standard video output interface, including but not limited to:

* HDMI (High-Definition Multimedia Interface) in any version
* DVI (Digital Visual Interface)
* DisplayPort in any version
* VGA (Video Graphics Array)
* SDI (Serial Digital Interface) in any variant including SD-SDI, HD-SDI, and 3G-SDI
* Composite video
* Component video
* S-Video
* Network video streams including NDI, RTP, RTSP, and similar protocols
* Optical media including fiber-optic HDMI and SDI

The use of lossless or near-lossless transmission paths is preferred to preserve exact pixel values. Where lossy transmission is unavoidable, error correction techniques may be applied at the encoding stage.

### 4.4 Capture and Decode Hardware

The receiving end of the system captures the video signal and decodes the embedded control data. Suitable capture and decode hardware includes but are not limited to:

* HDMI-to-MIPI CSI-2 bridge chips (such as Toshiba TC358743 or equivalent) connected to single-board computers via CSI-2 interface
* USB video capture cards connected to any computer or single-board computer
* PCIe video capture cards installed in desktop computers or servers
* Field-Programmable Gate Arrays (FPGAs) with HDMI or other video input capability, performing decode entirely in hardware for deterministic, low-latency operation
* Application-Specific Integrated Circuits (ASICs) purpose-designed for this decode function
* Microcontrollers with sufficient processing capability and appropriate video input hardware
* Any combination of the above

Decoding can also be done without a capture card done locally on device with or without GUI.

The decode process consists of capturing each incoming video frame, extracting the pixel values from the defined quadrant regions, interpreting those pixel values as control data bytes according to the encoding scheme, and dispatching the resulting control commands to connected hardware. This process repeats for every frame of video, achieving a command update rate equal to the video frame rate.

### 4.5 Command Dispatch Protocols

Decoded control data is dispatched from the decode hardware to connected control devices via any suitable protocol. Supported dispatch protocols include but are not limited to:

* UDP (User Datagram Protocol) unicast or multicast packets over Ethernet or Wi-Fi
* Art-Net protocol (DMX over UDP/IP)
* sACN (Streaming ACN, ANSI E1.31) protocol
* DMX512 over RS-485
* MIDI over serial, USB, or network
* Serial protocols including RS-232, RS-485, and TTL serial
* SPI (Serial Peripheral Interface)
* I2C (Inter-Integrated Circuit)
* CAN bus
* Proprietary serial protocols used by specific servo or motor controllers
* Any other wired or wireless data transmission protocol

### 4.6 Frame Validity and Safety Marker System

To prevent accidental activation of connected hardware from arbitrary video content not containing encoded control data, the system employs a frame validity marker scheme. One or more reserved quadrant regions within each frame are designated as safety markers. These regions contain known, specific pixel values on every valid encoded frame. The decode hardware checks these marker regions before processing any control data from the frame.

A frame is considered valid only when all designated marker regions contain their expected values. Frames that do not contain valid markers are ignored, and connected hardware maintains its last commanded state. This mechanism prevents random video content, test patterns, or other non-encoded video from being misinterpreted as control data.

Enhanced safety may be achieved by requiring a sequence of consecutive valid frames before enabling control data dispatch, and by embedding an incrementing counter value within the marker region that the decode hardware verifies is advancing correctly. A counter that stops advancing indicates a stalled, frozen, or invalid video source, causing the decode hardware to cease dispatching commands and hold connected hardware in a safe state.

The safety marker system, combined with the physical network isolation described below, creates a multi-layer safety architecture appropriate for use in environments where unintended hardware activation could cause injury or damage.

### 4.7 Timing, Synchronization, and Audio Alignment

Because the encoded control data is carried within the video frames of a standard video file, and because standard video playback devices play back audio and video tracks from the same file in synchronization, the control data is inherently synchronized with the audio track of the video file without requiring any external synchronization mechanism.

To account for the fixed, measurable latency introduced by the video capture and decode pipeline between the playback device and the control hardware, the system provides the following synchronization methods:

#### 4.7.1 Audio Track Offset

The audio track of the video file may be offset by a fixed duration relative to the video track at encode time, using standard video encoding tools. This offset is calculated to compensate for the known pipeline latency, so that by the time the control data decoded from a given video frame reaches the connected hardware, the audio playback from the same video file has advanced by the same duration. The result is that control data and audio arrive at their respective destinations simultaneously despite the pipeline latency. The offset value may be a fixed default (such as one video frame duration, approximately 33 milliseconds at 30 frames per second) or may be determined by a calibration procedure.

#### 4.7.2 Intentional Decode Buffer

The decode hardware may introduce an intentional, fixed-duration buffer between frame capture and command dispatch. By holding each decoded frame for a known duration before dispatching its commands, the total pipeline latency is made deterministic and consistent across different hardware configurations. A corresponding audio offset is then baked into the video file at encode time to compensate for this known total latency.

#### 4.7.3 Calibration Procedure

A calibration video containing known synchronization pulses — specific pixel patterns in the marker regions coinciding with known audio events — may be used to measure the actual pipeline latency of a specific hardware installation. The measured latency value is stored in a configuration file on the decode hardware and applied as the audio offset when encoding future show files for that installation.

#### 4.7.4 Linear Timecode and Timecode Tracks

Linear Timecode (LTC) or other timecode formats may be encoded on one or more audio channels of the video file. The decode hardware or other devices in the control network may use this timecode to maintain frame-accurate synchronization across multiple playback systems, to trigger external events at specific timecode positions, or to allow recording of live control data against a timecode reference for later encoding into a video file. The timecode track may also be used by external show control systems to chase the video playback position.

### 4.8 Multi-Player Synchronization and Scalability

Multiple video playback devices may be synchronized to play back encoded video files simultaneously, with each device feeding a separate decode unit and its associated control hardware. Standard multi-player synchronization mechanisms supported by commercial media players — including network sync protocols, GPIO sync signals, and timecode chase — allow all players to maintain frame-accurate synchronization. This architecture allows the system to scale to arbitrarily large numbers of control channels by adding additional playback and decode units, each operating independently while remaining synchronized through the playback layer.

### 4.9 Physical Network Isolation

A significant architectural property of this system is that the video signal constitutes the sole data path between the video playback system and the control hardware network. The video playback device and the decode hardware are connected only by the video signal cable. No IP network connection, serial connection, or other data connection is required between the playback system and the control hardware.

This means the control hardware network — comprising the decode unit, any connected control nodes, servo controllers, lighting fixtures, and other devices — may be physically isolated from any external network, including the internet, without any impact on the operation of the system. The playback device may be connected to external networks for content management, remote monitoring, or other purposes without exposing the control hardware network to those connections.

This physical isolation is an inherent architectural property of the system, not a configuration option. The video cable acts as a unidirectional data path, carrying data from the playback system to the decode hardware with no return path. This property makes the control hardware network resistant to remote interference, unauthorized access, and network-based attacks by design, without requiring firewall configuration, network segmentation, or other security measures.

### 4.10 Authoring Software

Show control authoring software may be used to create video files containing encoded control data. Such software provides a timeline-based interface allowing a user to program control values for any number of channels across the duration of a show, preview the resulting motion and control output, and export the programmed data as an encoded video file. The authoring software may support import of control data from external sources including DMX recording captures, MIDI files, motion capture data, and exports from other show control or lighting control software. The software may also support encoding of multiple data types within a single exported video file, allowing servo data, DMX data, MIDI data, and other control types to be combined in a single distribution file.

### 4.11 Extension to Other Carrier Media

While the primary carrier medium described in this disclosure is a standard digital video signal, the core method of encoding control data into the values of defined regions of a structured data frame is extensible to other carrier media, including:

* Audio signals, in which control data is encoded into specific frequency bands, amplitude envelopes, or phase relationships within an audio signal
* Network video streams, in which encoded video frames are transmitted over IP networks rather than physical video interfaces
* Optical disc media, in which encoded video is stored on Blu-ray, DVD, or other optical formats for physical distribution
* Still image sequences, in which control data is encoded into a series of still image files played back at a defined frame rate
* Any other medium capable of carrying a sequence of structured data frames at a defined rate

## 5. Claims of Priority and Prior Art

The following claims are made for the purpose of establishing prior art and preventing the patenting of the described methods by any party:

1. The method of encoding real-time control data into pixel values of defined regions within standard video frames.
2. The method of transmitting such encoded video via standard video output interfaces and capturing it with any suitable capture device for real-time decode.
3. The use of quadrant or block regions of pixels, defined by position and size within a video frame, as data-carrying units for control channel values.
4. The use of pixel color values, luminance values, chrominance values, brightness values, or grayscale values as the encoding medium for control data bytes.
5. The method of dispatching decoded control data to servo controllers, DMX lighting systems, Art-Net devices, MIDI devices, motor controllers, or any other hardware via any network or serial protocol.
6. The use of reserved pixel regions within encoded frames as frame validity markers to prevent accidental hardware activation from non-encoded video content.
7. The use of an incrementing counter value in marker regions for frame integrity verification and stall detection.
8. The method of compensating for decode pipeline latency by applying a fixed audio track offset within the video file at encode time.
9. The method of introducing an intentional fixed-duration buffer in the decode pipeline to make total pipeline latency deterministic, combined with a corresponding audio offset baked into the video file.
10. The use of a calibration video and measurement procedure to determine pipeline latency for a specific hardware installation.
11. The encoding of Linear Timecode or other timecode formats on audio channels of the encoded video file for synchronization purposes.
12. The architecture in which the video signal constitutes the sole data path between the playback system and the control hardware network, creating physical network isolation as an inherent property.
13. The scaling of the system through multiple synchronized video playback devices each feeding independent decode units.
14. The application of the above methods to carrier media other than physical video interfaces, including network streams, optical media, and audio signals.
15. Authoring software that programs multi-channel control data on a timeline and exports it as an encoded video file.

## 6. Dedication to the Public Domain

The author, Cameron Donlon, hereby dedicates all methods, systems, architectures, and concepts described in this publication to the public domain. This dedication is made for the purpose of establishing prior art and ensuring that the described methods remain freely available for use by any individual, organization, or entity without restriction. No patent rights are claimed or reserved by the author with respect to the subject matter of this publication.

---

— End of Defensive Publication —

Cameron Donlon | June 23, 2026
