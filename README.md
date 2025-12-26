# JavaScript Advanced Image Cropper

<font color="Red">This project was generated entirely by Gemini 3 Pro.</font>

A high-precision, browser-based image cropping tool built with Vanilla JavaScript and Three.js. This application features a "viewport-centric" cropping workflow, physics-based constraint solvers, and high-resolution export capabilities.

## ✨ Key Features

* **Viewport-Centric Workflow**: The crop box remains centered (or snaps back to center), while the image moves behind it. This mimics the widely used image cropping format found in Lightroom, the Android Photo Editor and Microsoft Photos.
* **Physics-Based Constraints**: Uses an iterative mathematical solver to ensure the crop box never leaves the image boundaries, even when the image is rotated or flipped. It slides smoothly along diagonal edges rather than getting stuck.
* **Non-Destructive Transforms**:
* **Smooth Rotation**: Arbitrary rotation (-45° to +45°) with sub-pixel precision.
* **Discrete Transforms**: 90° rotation and Horizontal/Vertical flipping.


* **Auto-Zoom & Snap Animation**: The image automatically zooms to fill the crop box when rotated. When resizing the crop box, it animates smoothly back to the optimal viewport size using cubic easing.
* **Aspect Ratio Locking**: Supports Free, 3:2, 4:3, 1:1, and 16:9 ratios with automatic landscape/portrait switching.
* **High-Resolution Export**: Uses **Three.js (WebGL)** for the final rendering to ensure the exported image matches the CSS preview pixel-for-pixel, supporting full-resolution output.
* **Rich UI**: Dark mode interface, "Rule of Thirds" grid, and intuitive corner/side handles.

## 🛠 Technical Architecture

This project uses a hybrid approach to balance performance and export quality.

### 1. The Hybrid Rendering Strategy

* **Preview (DOM/CSS3)**: The live editor uses HTML DOM elements transformed via CSS3 (`transform: matrix3d`). This ensures distinct, crisp rendering of large images without the texture limit or anti-aliasing artifacts often associated with WebGL canvases during the editing phase.
* **Export (Three.js)**: When downloading, the application reconstructs the exact scene graph in a Three.js `OrthographicCamera` scene. This allows for hardware-accelerated rendering of the final image at its native resolution.

### 2. Transform Hierarchy

To handle complex combinations of panning, scaling, free rotation, and discrete rotation (90° steps) without coordinate singularities (Gimbal lock), the application uses a strict nested hierarchy:

1. **Smooth Rotation Wrapper (Outermost)**
* *Role*: Handles arbitrary rotation from the slider (Z-axis).
* *Contains*: Pan & Scale Container

2. **Pan & Scale Container**
* *Role*: Handles X/Y translation and scaling (Zoom).
* *Contains*: Base Transform Wrapper

3. **Base Transform Wrapper**
* *Role*: Handles discrete 90° rotation steps and flipping (Scale -1/1).
* *Contains*: Image Element

4. **Image Element (Innermost)**
* *Role*: The raw source image.

### 3. The Constraint Solver (Iterative Projection)

The core difficulty in a rotated cropper is preventing the crop box from exiting the image. Simple min/max clamping fails when the image is rotated.

We implemented a **Geometric Iterative Solver**:

1. **Define Inequalities**: The image boundaries are treated as linear inequalities in the local coordinate space.
2. **Iterative Projection**: When a user drags a handle, the proposed coordinate is checked against these boundaries. If a violation occurs, the point is mathematically projected (pushed back) perpendicular to the violated boundary edge.
3. **Convergence**: This process repeats multiple times per frame (e.g., 20 iterations) to find the mathematically perfect intersection where the crop box corner "slides" along the edge of the rotated image.

## 🚀 Getting Started

### Prerequisites

No build step is required. This is a standalone HTML application. It requires an internet connection to load the Three.js library via CDN.

### Installation

1. Clone the repository.
2. Open `index.html` in any modern web browser.

### Usage

1. **Import**: Click "Open Image" to select a file from your device.
2. **Adjust**:
* Drag the image to pan.
* Drag the corners/sides of the white box to crop.
* Use the bottom toolbar to set aspect ratios, rotate, or flip.


3. **Export**: Click the "Export" button to download the cropped `.png` file.

## 🧩 Dependencies

* [Three.js](https://threejs.org/) (r128) - Used solely for the image export rendering pipeline.

## 📄 License

This project is open source and available under the [MIT License](https://www.google.com/search?q=LICENSE).

---

### Acknowledgments

Developed as an experiment in advanced DOM mathematics and WebGL coordinate mapping. Special thanks to the Three.js community.
