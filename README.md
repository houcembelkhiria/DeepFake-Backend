# 🔍 DeepFake Detection API

<div align="center">

![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=for-the-badge&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.115.6-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.18.0-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?style=for-the-badge&logo=docker&logoColor=white)

**A powerful REST API for detecting manipulated images using Error Level Analysis (ELA) and Deep Learning**

[Features](#-features) • [Quick Start](#-quick-start) • [API Endpoints](#-api-endpoints) • [Docker](#-docker-deployment) • [Tech Stack](#-tech-stack)

</div>

---

## ✨ Features

- 🎯 **Deep Learning Based Detection** - Uses a trained TensorFlow/Keras CNN model
- 🔬 **Error Level Analysis (ELA)** - Applies ELA preprocessing to detect image manipulations
- 🚀 **Fast Inference** - Model loaded once at startup for optimal performance
- 📤 **Multiple Upload Methods** - Support for direct file upload and path-based prediction
- 🐳 **Docker Ready** - Fully containerized for easy deployment
- 📚 **Auto-Generated Docs** - Swagger UI and ReDoc documentation included

---

## 🛠 Tech Stack

| Technology | Version | Purpose |
|------------|---------|---------|
| **Python** | 3.11 | Core programming language |
| **FastAPI** | 0.115.6 | Modern, fast web framework |
| **TensorFlow** | 2.18.0 | Deep learning model inference |
| **Pillow** | 11.0.0 | Image processing & ELA |
| **Uvicorn** | 0.34.0 | ASGI server |
| **NumPy** | 2.0.2 | Numerical computations |

---

## 🚀 Quick Start

### Prerequisites

- Python 3.11+
- Trained model file (`.h5` format)

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd backend
   ```

2. **Create a virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure environment variables** (optional)
   ```bash
   export MODEL_PATH=/path/to/your/model.h5
   export FILES_DIR=/path/to/images/directory
   ```

5. **Run the server**
   ```bash
   uvicorn app:app --host 0.0.0.0 --port 8000 --reload
   ```

6. **Access the API documentation**
   - Swagger UI: [http://localhost:8000/docs](http://localhost:8000/docs)
   - ReDoc: [http://localhost:8000/redoc](http://localhost:8000/redoc)

---

## 📡 API Endpoints

### `POST /upload`

Upload an image directly for DeepFake detection.

**Request:**
```bash
curl -X POST "http://localhost:8000/upload" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@image.jpg"
```

**Response:**
```json
{
  "file": "image.jpg",
  "prediction": "Real",
  "confidence": 95.67,
  "probabilities": {
    "real": 95.67,
    "fake": 4.33
  }
}
```

### `POST /predict/{folder}/{filename}`

Predict on an image already stored on the server.

**Request:**
```bash
curl -X POST "http://localhost:8000/predict/uploads/test_image.jpg"
```

**Response:**
```json
{
  "file": "test_image.jpg",
  "prediction": "Fake",
  "confidence": 87.23,
  "probabilities": {
    "real": 12.77,
    "fake": 87.23
  }
}
```

---

## 🔬 How It Works

### Error Level Analysis (ELA)

The API uses **Error Level Analysis** as a preprocessing step before feeding images to the neural network:

1. **Original Image** - Load the uploaded image
2. **Compression** - Re-save the image at a lower JPEG quality (95%)
3. **Difference Calculation** - Compute pixel-wise difference between original and compressed
4. **Brightness Enhancement** - Scale the difference to enhance visibility
5. **Model Prediction** - Feed the 128x128 ELA image to the CNN

> 💡 **Why ELA?** Manipulated regions of an image often have different compression artifacts compared to the original, making them detectable through ELA.

---

## 🐳 Docker Deployment

### Using Docker Compose (Recommended)

```bash
# From the project root directory
docker-compose up --build
```

The API will be available at `http://localhost:8002`

### Using Docker Standalone

1. **Build the image**
   ```bash
   docker build -t deepfake-api .
   ```

2. **Run the container**
   ```bash
   docker run -d \
     -p 8000:8000 \
     -v /path/to/model:/app/models \
     -e MODEL_PATH=/app/models/best_model.h5 \
     --name deepfake-api \
     deepfake-api
   ```

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `MODEL_PATH` | Path to the trained `.h5` model | Local path |
| `FILES_DIR` | Directory for stored images | `./images` |
| `TF_ENABLE_ONEDNN_OPTS` | Disable oneDNN optimizations | `0` |
| `TF_CPP_MIN_LOG_LEVEL` | TensorFlow logging level | `2` |

---

## 📁 Project Structure

```
backend/
├── app.py              # Main FastAPI application
├── requirements.txt    # Python dependencies
├── Dockerfile          # Docker configuration
├── .dockerignore       # Docker ignore rules
├── images/             # Directory for uploaded images
└── README.md           # This file
```

---

## 🔒 CORS Configuration

The API is configured to allow all origins for development purposes:

```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

> ⚠️ **Production Note:** For production deployments, restrict `allow_origins` to your frontend domain(s).

---

## 📊 Response Format

All prediction endpoints return a JSON object with:

| Field | Type | Description |
|-------|------|-------------|
| `file` | string | Name of the analyzed file |
| `prediction` | string | Either "Real" or "Fake" |
| `confidence` | float | Confidence percentage (0-100) |
| `probabilities.real` | float | Probability of being real (%) |
| `probabilities.fake` | float | Probability of being fake (%) |

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

---

## 📝 License

This project is part of an academic deep learning course at TEKUP.

---

<div align="center">

**Made with ❤️ using FastAPI and TensorFlow**

</div>
