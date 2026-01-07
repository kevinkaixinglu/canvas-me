# CanvasMe: Gestural Interface & AR Engine 
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![Computer Vision](https://img.shields.io/badge/Computer_Vision-MediaPipe-blue?style=for-the-badge)

**A touchless drawing interface and augmented reality face-tracking application powered by client-side computer vision.**

![Gameplay Demo](./assets/canvas-me-demo.gif)

## About
CanvasMe is an exploration into **Human-Computer Interaction (HCI)** that removes the need for physical input devices. It allows users to draw on a digital canvas using only hand gestures and then project those creations onto their own face using real-time AR tracking.

The system uses **Handsfree.js** (wrapping MediaPipe) to map 3D hand and face landmarks to the 2D DOM, enabling a seamless workflow from creation to augmentation.

## Technical Architecture

### 1. Vector-Based Gesture Recognition
Instead of relying on pre-built gesture libraries, I implemented custom vector logic to detect "Intent."
* **Pointing Detection:** The system calculates the y-coordinate delta between finger tips and finger bases. A "Draw" state is triggered only when the **Index Finger** is extended and other fingers are curled (vectors negative).
* **Coordinate Mapping:** Maps the normalized `(0,0) - (1,1)` webcam coordinates to the viewport dimensions, mirroring the x-axis for an intuitive "mirror" effect.

### 2. Signal Processing & Smoothing
Raw computer vision data is often noisy, leading to jittery lines.
* **Linear Interpolation (Lerp):** I implemented a smoothing algorithm that interpolates between the current frame's landmark position and the previous frame's coordinates (`factor = 0.15`).
* **Result:** This creates fluid, organic brush strokes even when the tracking data fluctuates.

### 3. Augmented Reality (AR) Engine
The camera module features a custom "Sticker" system that anchors 2D images to 3D face meshes.
* **Collision Detection:** Uses bounding-box logic (`rect1.right < rect2.left`) to detect when a user drags a drawing over their face.
* **Landmark Anchoring:** Once overlap is detected, the image locks to **Face Landmark #33** (Eye). The system calculates the offset between the image center and the eye coordinate, maintaining relative position even as the user moves closer/further or tilts their head.

### 4. Client-Side Persistence
* **State Management:** Utilizes browser `localStorage` to serialize the HTML5 Canvas data as a Base64 string/Blob. This allows zero-latency transfer of high-resolution assets between the "Drawing" environment and the "AR" environment without server round-trips.
* **Composite Rendering:** The screenshot feature uses an off-screen canvas to merge the raw video feed with the DOM-based AR overlays, flipping the matrix context to ensure the saved image matches the mirrored user view.

## How to Use
1.  **Draw:** Open the app and raise your hand. Extend your index finger to begin painting.
2.  **Save:** Click "Save" to serialize your artwork to local storage.
3.  **Augment:** Switch to **Camera Mode**. Your drawing will appear as a movable sticker.
4.  **Track:** Drag the sticker onto your face. The engine will "lock" it to your facial mesh.
5.  **Capture:** Click the camera icon to download a composite snapshot.
