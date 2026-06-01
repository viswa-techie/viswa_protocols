# AR/VR/XR Standards — Comprehensive Overview

**Category:** 38 — AR/VR/XR (Extended Reality)  
**Document:** 00 — Standards Landscape Overview  
**Scope:** OpenXR, WebXR, display standards, safety (IEC 62471), privacy, accessibility  
**Key Standards:** OpenXR 1.0 (Khronos), WebXR Device API (W3C), IEC 62471, ISO 9241-39x  
**Audience:** XR engine developers, display engineers, UX designers, safety engineers  
**Prerequisites:** Computer graphics basics, display technology, human factors

---

## Chapter 1 — Historical Context

### 1.1 XR Standards Evolution

| Year | Milestone | Impact |
|------|-----------|--------|
| 1968 | "Sword of Damocles" (Ivan Sutherland) | First head-mounted display |
| 1992 | CAVE automatic virtual environment | Multi-wall immersive VR |
| 1994 | VRML (Virtual Reality Modeling Language) | First web 3D standard |
| 2007 | Google Street View | Consumer 360° imagery |
| 2012 | Oculus Rift Kickstarter | Modern VR revival |
| 2014 | Google Cardboard | Low-cost mobile VR |
| 2016 | Oculus Rift CV1 + HTC Vive launch | Consumer 6DoF VR |
| 2017 | ARKit (Apple) + ARCore (Google) | Mobile AR standards |
| 2019 | OpenXR 1.0 specification (Khronos Group) | Unified XR runtime standard |
| 2020 | WebXR Device API (W3C Recommendation) | Web-based XR access |
| 2023 | Apple Vision Pro announcement | Spatial computing mainstream |
| 2023 | Meta Quest 3 (mixed reality) | Consumer MR adoption |
| 2024 | OpenXR 1.1 with hand tracking, body tracking | Extended interaction standards |

### 1.2 Standards Landscape

```mermaid
graph TB
    subgraph "Runtime & API"
        OPENXR[OpenXR (Khronos)<br/>Native XR runtime API]
        WEBXR[WebXR (W3C)<br/>Web-based XR API]
        ARKIT[ARKit (Apple)<br/>iOS/visionOS AR]
        ARCORE[ARCore (Google)<br/>Android AR]
    end
    
    subgraph "Rendering & Graphics"
        VULKAN[Vulkan (Khronos)<br/>Low-level graphics]
        GLTF[glTF 2.0 (Khronos)<br/>3D asset format]
        USD[OpenUSD (Pixar/Alliance)<br/>Scene description]
    end
    
    subgraph "Display & Optics"
        IEC62471[IEC 62471<br/>Photobiological Safety]
        ICDM[ICDM (SID)<br/>Display Measurements]
        ISO9241[ISO 9241-39x<br/>Ergonomics of HMDs]
    end
    
    subgraph "Safety & Health"
        IEC63315[IEC 63315 (draft)<br/>XR Safety]
        ISO16505[ISO 16505<br/>Camera Monitor Systems]
    end
    
    subgraph "Content & Interaction"
        MPEG[MPEG-I<br/>Immersive media]
        IEEE2048[IEEE 2048<br/>XR Ethics]
    end
    
    OPENXR --> VULKAN
    OPENXR --> GLTF
    WEBXR --> GLTF
    ARKIT --> USD
```

---

## Chapter 2 — OpenXR (Khronos Group)

### 2.1 Architecture

```mermaid
graph TB
    subgraph "Application Layer"
        APP1[Game Engine<br/>(Unity, Unreal)]
        APP2[Custom XR App]
    end
    
    subgraph "OpenXR API Layer"
        LOADER[OpenXR Loader<br/>─ API dispatch<br/>─ Layer management]
        LAYER1[API Layers<br/>─ Validation<br/>─ Debug<br/>─ Performance overlay]
    end
    
    subgraph "Runtime Layer"
        RT1[Meta OpenXR Runtime]
        RT2[SteamVR OpenXR Runtime]
        RT3[Windows Mixed Reality]
        RT4[Monado (Open Source)]
    end
    
    subgraph "Hardware"
        HW1[Quest 3/Pro]
        HW2[Valve Index]
        HW3[HP Reverb]
        HW4[Linux HMDs]
    end
    
    APP1 --> LOADER
    APP2 --> LOADER
    LOADER --> LAYER1
    LAYER1 --> RT1
    LAYER1 --> RT2
    LAYER1 --> RT3
    LAYER1 --> RT4
    RT1 --> HW1
    RT2 --> HW2
    RT3 --> HW3
    RT4 --> HW4
```

### 2.2 OpenXR Core Concepts

| Concept | Description | Function |
|---------|-------------|----------|
| Instance | Application connection to runtime | `xrCreateInstance()` |
| System | Physical XR device | `xrGetSystem()` |
| Session | Rendering session lifecycle | `xrCreateSession()` → Begin → End |
| Spaces | Coordinate systems | LOCAL, STAGE, VIEW reference spaces |
| Actions | Input abstraction (controller-agnostic) | `xrCreateActionSet()`, action bindings |
| Swapchain | Image buffer for rendering | `xrCreateSwapchain()` |
| Views | Per-eye rendering configuration | Stereo pair (left/right) |
| Frame | Render loop timing | `xrWaitFrame()` → `xrBeginFrame()` → `xrEndFrame()` |

### 2.3 OpenXR Extensions (Key Categories)

| Category | Extension | Purpose |
|----------|----------|---------|
| Hand Tracking | XR_EXT_hand_tracking | Skeletal hand tracking (26 joints) |
| Eye Tracking | XR_EXT_eye_gaze_interaction | Gaze direction input |
| Passthrough | XR_FB_passthrough | Camera passthrough for MR |
| Spatial Anchors | XR_MSFT_spatial_anchor | Persistent world-locked content |
| Scene Understanding | XR_MSFT_scene_understanding | Room mesh, planes, objects |
| Body Tracking | XR_FB_body_tracking | Full body pose estimation |
| Facial Tracking | XR_FB_face_tracking2 | Facial expression capture |
| Foveated Rendering | XR_FB_foveation | Reduce peripheral resolution |
| Composition Layers | XR_KHR_composition_layer_* | UI panels, video layers |

---

## Chapter 3 — WebXR Device API (W3C)

### 3.1 WebXR Session Types

| Session Mode | Description | Hardware |
|-------------|-------------|---------|
| `immersive-vr` | Full VR (headset replaces environment) | VR headsets |
| `immersive-ar` | AR overlay on real world | AR glasses, phone AR |
| `inline` | Non-immersive 3D on web page | Desktop/mobile browser |

### 3.2 WebXR Feature Descriptors

| Feature | Descriptor | Description |
|---------|-----------|-------------|
| Local floor | `local-floor` | Origin at floor level |
| Bounded reference | `bounded-floor` | Playspace boundary |
| Hand tracking | `hand-tracking` | Skeletal hand input |
| Hit test | `hit-test` | Ray-surface intersection (AR) |
| Anchors | `anchors` | Persistent spatial anchors |
| Plane detection | `plane-detection` | Surface detection (AR) |
| Mesh detection | `mesh-detection` | Room mesh (AR) |
| Depth sensing | `depth-sensing` | Depth buffer access |
| Light estimation | `light-estimation` | Environmental lighting |
| DOM overlay | `dom-overlay` | HTML UI in AR session |
| Layers | `layers` | Multi-layer composition |

### 3.3 WebXR vs. OpenXR

| Feature | WebXR | OpenXR |
|---------|-------|--------|
| Platform | Web browsers | Native applications |
| Language | JavaScript/TypeScript | C/C++ (native) |
| Performance | Lower (JS overhead, compositor) | Maximum (direct GPU access) |
| Distribution | URL-based (no install) | App store / sideload |
| Hardware access | Limited by browser security | Full device access |
| Foveated rendering | Limited browser support | Full extension support |
| Hand tracking | Supported | Supported (more detailed) |

---

## Chapter 4 — Display Standards & Optics

### 4.1 Display Performance Metrics (ICDM / SID)

| Metric | Definition | VR Target (2024) | AR Target |
|--------|-----------|-----------------|-----------|
| PPD (Pixels Per Degree) | Angular pixel density | > 25 PPD | > 40 PPD |
| Refresh rate | Display update frequency | 90-120 Hz | 60-120 Hz |
| Persistence | Time pixel is illuminated per frame | < 2ms (low persistence) | < 1ms |
| Field of View (FoV) | Angular extent of visible display | > 100° horizontal | > 50° (waveguide) |
| Motion-to-photon latency | Input → displayed change | < 20ms | < 20ms |
| Contrast ratio | Max/min luminance | > 10,000:1 (OLED) | N/A (see-through) |
| Eye box | Volume where image is visible | > 10mm | > 8mm |
| Vergence-accommodation conflict | Mismatch between eye focus mechanisms | Mitigated (varifocal) | Less severe (additive) |

### 4.2 Display Technologies for XR

| Technology | Type | Advantages | Challenges |
|-----------|------|-----------|-----------|
| OLED (micro) | VR display panel | Deep blacks, fast pixel response | Limited brightness, burn-in |
| LCD (fast-switch) | VR display panel | Higher brightness, no burn-in | Lower contrast, slower response |
| MicroLED | AR/VR (emerging) | Extreme brightness, long life | Manufacturing yield, cost |
| LCoS (Liquid Crystal on Silicon) | AR waveguide source | High resolution, compact | Limited brightness |
| LBS (Laser Beam Scanning) | AR retinal projection | Very compact, true focus depth | Speckle, safety, low FoV |
| Holographic waveguide | AR optics (combiners) | Thin, transparent | Rainbow effects, FoV limited |
| Birdbath optics | AR (pass-through) | Wide FoV, good image quality | Heavy, thick |
| Pancake lens | VR optics | Thin form factor | Light loss (75%), ghosting |

---

## Chapter 5 — Safety & Photobiological Standards

### 5.1 IEC 62471 — Photobiological Safety of Lamps

| Risk Group | Classification | Near-eye XR Consideration |
|-----------|---------------|--------------------------|
| Exempt (RG0) | No photobiological hazard | Safe for extended use |
| Low Risk (RG1) | No hazard from aversion response | Acceptable for XR (<1000s exposure) |
| Moderate Risk (RG2) | Aversion response protects | Requires exposure time limits |
| High Risk (RG3) | Hazardous even with aversion | Not acceptable for consumer XR |

### 5.2 XR-Specific Safety Considerations

| Hazard | Standard/Guideline | Mitigation |
|--------|-------------------|-----------|
| Blue light exposure | IEC 62471, IEC TR 62778 | Blue light filtering, exposure limits |
| Epileptic seizure risk | IEC 62368-1 | Flash rate limits (< 3 Hz for full-field) |
| Cybersickness (motion sickness) | ISO 9241-392 | Framerate ≥ 90 Hz, reduce vection |
| Physical collision | IEC 63315 (draft) | Guardian/boundary systems |
| Eye strain / fatigue | ISO 9241-394 | Session time limits, break reminders |
| Interpupillary distance (IPD) | ISO 9241-393 | Adjustable IPD (58-72mm range) |
| Thermal discomfort | IEC 62368-1 | Surface temp < 48°C (skin contact) |

### 5.3 ISO 9241-39x Series (Ergonomics of HMDs)

| Standard | Title | Content |
|----------|-------|---------|
| ISO 9241-391 | Requirements and guidelines for HMDs in general | Overall ergonomics framework |
| ISO 9241-392 | Guidance on methods for assessing VR ergonomics | Test methods for cybersickness |
| ISO 9241-393 | Requirements for HMD optical properties | Distortion, accommodation |
| ISO 9241-394 | Guidelines for comfortable use | Usage duration, break scheduling |
| ISO 9241-395 (draft) | AR/MR specific requirements | See-through display ergonomics |

---

## Chapter 6 — 3D Content Standards

### 6.1 glTF 2.0 (Khronos)

| Feature | Description |
|---------|-------------|
| Format | JSON + binary buffers (.gltf + .bin or single .glb) |
| Geometry | Triangulated meshes, morph targets |
| Materials | PBR metallic-roughness model |
| Animation | Skeletal (skin/joints), morph target, property |
| Extensions | KHR_materials_unlit, KHR_draco_mesh_compression, etc. |
| XR relevance | Default 3D interchange format for WebXR and OpenXR |

### 6.2 OpenUSD (Universal Scene Description)

| Feature | Description |
|---------|-------------|
| Organization | Alliance for OpenUSD (Pixar, Apple, Autodesk, NVIDIA, Adobe) |
| Format | .usd (text), .usdc (binary/crate), .usdz (zipped AR package) |
| Composition | Layering, referencing, variant sets, payload deferral |
| Physics | PhysicsSchemas for rigid/soft body, collisions |
| Materials | MaterialX + UsdShade (PBR) |
| XR relevance | Apple Vision Pro native format (.usdz); collaborative 3D |

### 6.3 MPEG-I (Immersive Media)

| Part | Title | Application |
|------|-------|-------------|
| MPEG-I Part 2 | Omnidirectional Media Format (OMAF) | 360° video delivery |
| MPEG-I Part 5 | Video-based Point Cloud Compression (V-PCC) | Volumetric video |
| MPEG-I Part 9 | Geometry-based Point Cloud Compression (G-PCC) | LiDAR-captured 3D |
| MPEG-I Part 12 | MPEG Immersive Video (MIV) | Multi-view 6DoF video |
| MPEG-I Part 14 | Scene Description for MPEG Media | glTF + media integration |

---

## Chapter 7 — Privacy & Ethics

### 7.1 XR-Specific Privacy Concerns

| Data Type | Sensitivity | Privacy Risk | Regulation |
|-----------|------------|-------------|-----------|
| Eye tracking data | Very high | Cognitive state inference, attention | GDPR Art. 9 (biometric) |
| Room mapping (mesh) | High | Interior surveillance, layout | GDPR, CCPA |
| Hand/body tracking | High | Gesture patterns, identity | Biometric data laws |
| Facial expressions | Very high | Emotion recognition | EU AI Act (prohibited uses) |
| Spatial anchors | Medium | Location history, place identification | Location privacy laws |
| Pupil dilation | Very high | Emotional/cognitive state | GDPR Art. 9 |
| Gait pattern | High | Biometric identification | Biometric laws |

### 7.2 IEEE 2048 — XR Ethics

| Principle | Description |
|-----------|-------------|
| Safety | Physical and psychological safety of users |
| Privacy | Data minimization, consent, transparency |
| Inclusivity | Accessible to diverse abilities and cultures |
| Accountability | Clear responsibility for XR content/systems |
| Transparency | Disclosure of virtual vs. real elements |
| Interoperability | Avoid vendor lock-in of user data/identity |

---

## Chapter 8 — Interview Questions

### Tier 1: Entry-Level
1. What is OpenXR and how does it differ from device-specific SDKs?
2. Explain the difference between 3DoF and 6DoF tracking.
3. What is motion-to-photon latency and why must it be < 20ms?
4. What are the WebXR session modes (immersive-vr, immersive-ar, inline)?

### Tier 2: Mid-Level
1. Walk through the OpenXR frame loop (xrWaitFrame → xrBeginFrame → xrEndFrame).
2. How do you implement foveated rendering using OpenXR extensions?
3. Compare glTF 2.0 vs. OpenUSD for XR content delivery.
4. How does IEC 62471 apply to VR headset blue light exposure assessment?

### Tier 3: Senior/Lead
1. Design an OpenXR application architecture supporting hand tracking + eye tracking + passthrough MR.
2. How do you architect a WebXR platform for enterprise training with 50 concurrent users?
3. Explain vergence-accommodation conflict mitigation strategies and their display technology requirements.
4. How do you handle GDPR compliance for an XR system collecting eye tracking + room mapping data?

### Tier 4: Principal
1. Design a cross-platform XR standard unifying OpenXR + WebXR + proprietary runtimes (visionOS).
2. How should XR standards evolve for neural interfaces and brain-computer interaction?
3. Propose an XR privacy framework addressing biometric data from eye/face/body tracking.
4. How do you architect a "metaverse" interoperability standard for identity, assets, and social presence?

---

*Document Version: 1.0 | Last Updated: May 2026 | Author: Technology Standards Team*
