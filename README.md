![PyTorch](https://img.shields.io/badge/PyTorch-Deep_Learning-EE4C2C)
![FastAPI](https://img.shields.io/badge/FastAPI-Backend-009688)
![Google Colab](https://img.shields.io/badge/Google_Colab-T4_GPU-F9AB00)
![Status](https://img.shields.io/badge/Status-Active-success)

# 🎨 NeuralArt Studio: Neural Style Transfer  
**High-Performance AI Web App for Artistic Image Generation**

[![Live Demo](https://img.shields.io/badge/Frontend-Live%20on%20GitHub%20Pages-blue)](https://syedimad-ds.github.io/Style-Transfer/)  
*(Note: The live frontend requires the Colab backend to be running actively.)*

NeuralArt Studio is a full-stack AI web application that transfers the visual style of any artwork onto any photo in seconds. Powered by an optimized **Gatys Neural Style Transfer Model** and a pre-trained **VGG19** network, it runs efficiently on a **Google Colab Free T4 GPU**.

---

## 🛠️ Tech Stack
- **AI Model:** PyTorch (VGG19 Feature Extraction & LBFGS Optimization)
- **Backend:** FastAPI (REST API Server)
- **Frontend Dashboard:** Pure HTML/CSS/Vanilla JS (Dark Theme UI, Responsive)
- **Deployment & Tunneling:** Google Colab + Ngrok
- **Data Processing:** Pillow, Torchvision

---

## 📂 Repository Structure

```text
Style-Transfer/
│
├── index.html                   # Frontend UI (Dark Theme, Image Dropzones)
├── NeuralArt_API_ipynb.ipynb    # Google Colab Backend (FastAPI + PyTorch)
└── README.md                    # Project documentation
```
---

## 🌩️ Architecture (Decoupled Deployment)

This project features a decoupled Serverless-style architecture to provide free GPU compute:

* **The Client (Frontend):** Hosted entirely for free on GitHub Pages. It handles the UI, image to Base64 conversion, and API communication.
* **The Brain (Backend):** Hosted on Google Colab. It leverages the free T4 GPU to run the heavy PyTorch optimization loop.
* **The Bridge (Ngrok):** Creates a secure, temporary public tunnel exposing the Colab localhost to the Frontend.

---

## 🧠 Technical Approach & Trade-offs

### The Current Approach
This project intentionally uses the **Gatys Neural Style Transfer Optimization Model** rather than faster feed-forward networks (like AdaIN or CycleGAN). 

### Why this approach?
During development, we tested AdaIN models for real-time inference. However, they consistently produced "muddy" or washed-out results, destroying fine facial features (like eyes and noses in pet photos) and over-applying the style colors. 

To achieve "museum-quality" results, we shifted to the Gatys method. Instead of a single forward pass, it starts with the content image and uses LBFGS optimization to iteratively update pixels, minimizing both content loss and style loss simultaneously. 

**The Optimization for Free Tier:** Standard Gatys takes minutes to run. To make this viable for a web application running on a free Google Colab T4 GPU, the algorithm was heavily optimized:
* Reduced image resolution to `256x256`.
* Capped optimization steps to `80`.
* Increased learning rate and style weights.
* **Result:** High-fidelity, sharp painterly effects delivered in just **10-15 seconds**.

---

## ⚠️ Current Limitations (Shortcomings)

As a trade-off for utilizing a 100% free cloud-GPU architecture, the system has a few inherent limitations:
1. **Ephemeral API Endpoints:** Because the backend relies on Google Colab and Ngrok, the API URL changes every time the Colab notebook is restarted. The user must manually paste the new URL into the frontend.
2. **Resolution Cap:** The output is capped at 256px. Processing higher resolutions (e.g., 512px or 1080px) on the free tier takes longer than standard browser timeout limits (usually 1-2 minutes), which would cause frontend HTTP requests to fail.
3. **Session Lifespan:** Google Colab terminates idle sessions. If the backend is left inactive, the environment will reset, requiring a manual restart of the notebook cells.

---

## 🚀 How to Run the Project

Since this project requires a GPU for processing, the backend is hosted on Google Colab, while the frontend is accessed via your browser.

### Step 1: Start the AI Backend (Google Colab)
1. Open `NeuralArt_API_ipynb.ipynb` in **Google Colab**.
2. Go to **Runtime → Change runtime type** and ensure **T4 GPU** is selected.
3. In Colab's left sidebar, click the **🔑 Secrets (Key icon)**.
4. Add a new secret named `NGROK_TOKEN` and paste your free token from the ngrok dashboard. (Ensure "Notebook access" is toggled ON).
5. Click **Runtime → Run All**.
6. Wait for the final cell to execute and copy your generated public URL, which will look like this:

```text
🚀 PREMIUM SERVER URL: [https://xxxx-xx-xx-xxx.ngrok-free.app](https://xxxx-xx-xx-xxx.ngrok-free.app)
```

### Step 2: Connect the Frontend
1. Open the Live Frontend link (or run index.html locally).
2. Paste the ngrok URL from Step 1 into the Configuration Bar at the top.
3. Drop your photo into the Content Image zone.
4. Drop an artwork into the Style Image zone.
5. Click ⚡ Generate Stylized Image.
6. Wait approximately for a minute for the GPU optimization, use the Before/After slider to compare, and download your masterpiece!

---

## 💡 Tips for Best Results
- Image Choice: For the best results, use high-contrast style images (e.g., Van Gogh's Starry Night, Monet, or heavy textures).
- Resolution: The output resolution is currently optimized to 256px to ensure lightning-fast processing (~10s) and prevent Colab Free Tier timeouts.
- Session Limits: Keep the Colab tab open while generating images. If the Colab session disconnects, you will need to restart the backend to generate a new Ngrok URL.
