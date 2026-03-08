# 🌱 NPK & pH Based Fertilizer Recommendation System

This project is a Flask-based API that processes soil images to estimate **Nitrogen (N)**, **Phosphorus (P)**, **Potassium (K)**, and **pH levels**. It utilizes Machine Learning (TFLite & Scikit-learn) and Google's Gemini AI to identify **soil type**, verify if the uploaded image actually contains soil, and generate a customized **fertilizer recommendation** based on weather and crop data.

It is designed as a backend-friendly pipeline with integrated CORS support, ready to bridge with web, mobile, or backend applications.

---

## 🚀 Features

✔ **Soil Detection Layer:** Machine learning model verifies if the submitted image actually contains soil before processing.
✔ **Image Feature Extraction:** Extracts Local Binary Patterns (LBP), GLCM, and color channels.
✔ **Predicts NPK + pH:** Estimates soil parameters from image features.
✔ **Classifies Soil Type:** Uses TFLite to detect 8 different soil varieties.
✔ **AI Fertilizer Recommendation:** Integrates Google Gemini AI for smart, JSON-formatted fertilizer suggestions based on NPK, Organic Carbon, weather, and crop type.
✔ **CORS Enabled & Browser Ready:** Supports direct image uploads (`multipart/form-data`) alongside JSON URL passing.

---

## 📁 Project Structure

```text
├── app.py                       # Main Flask API / Inference script
├── requirements.txt             # Python dependencies
├── npk_ph_predictor_model.pkl   # Regression model for NPK + pH estimation
├── model_unquant.tflite         # Soil type classification (TFLite)
├── model_unquant2.tflite        # Soil vs Non-soil detector (TFLite)
├── labels.txt                   # Class labels for model_unquant
├── labels2.txt                  # Class labels for model_unquant2
└── README.md                    # Documentation
```

---

## 🛠 Installation & Local Setup

### 1. Clone the repository and navigate to the directory
```bash
git clone <your-repo-link>
cd fertilizer-recommendation
```

### 2. Create and Activate Virtual Environment
```bash
# On Mac/Linux
python -m venv venv
source venv/bin/activate

# On Windows
python -m venv venv
venv\Scripts\activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Run the Application
Start the Flask development server on `http://127.0.0.1:5000`:
```bash
python app.py
```

---

## 📌 API Specification

### 🔹 1. Root Endpoint (Health Check)
Check if the server is running.
- **Endpoint:** `/`
- **Method:** `GET`
- **Response:** `server is start`

---

### 🔹 2. Predict NPK + pH + Soil Type
Evaluates image(s) and predicts soil metrics. You can supply either an image URL or directly upload the image file.

- **Endpoint:** `/predict_npk`
- **Method:** `POST`

#### Method A: Using Image URLs (JSON Content-Type)
```json
{
  "image_urls": [
    "https://example.com/soil1.jpg"
  ]
}
```

#### Method B: Direct File Upload (Multipart Form-Data Content-Type)
Send the image file under the key `images`.

**Example using `curl`:**
```bash
curl -X POST http://127.0.0.1:5000/predict_npk \
  -F "images=@/path/to/your/soil.jpg"
```

**Response Example:**
```json
{
  "k_value": 31.62,
  "n_value": 78.43,
  "p_value": 15.11,
  "ph_value": 6.8,
  "soil_type": "Alluvial soil",
  "success": true
}
```

---

### 🔹 3. Generate Fertilizer Recommendation
Sends the soil metrics to Google Gemini AI to retrieve a tailored recommendation.

- **Endpoint:** `/recommend_fertilizer`
- **Method:** `POST`
- **Content-Type:** `application/json`

**Request Payload:**
```json
{
  "n_value": 78.43,
  "p_value": 15.11,
  "k_value": 31.62,
  "oc_value": 0.8,
  "crop_type": "Wheat",
  "soil_type": "Alluvial soil",
  "weather": "moderate humidity, 25C"
}
```

**Response Example:**
```json
{
  "organic": {
    "fertilizer_name": "Compost or Farmyard Manure (FYM)",
    "fertilizer_quantity": "1000 - 2000",
    "application_schedule": "Pre-planting",
    "application_method": "Broadcast and incorporate into soil",
    "data": "Improves soil structure, water retention, and microbial activity."
  },
  "inorganic": {
    "fertilizer_name": "Urea, SSP (Single Super Phosphate), MOP (Muriate of Potash)",
    "fertilizer_quantity": "Urea: 80-100, SSP: 150-200, MOP: 0",
    "application_schedule": "Urea: 1/2 basal, 1/2 top dressing at 30-45 DAS. SSP: Basal. MOP: Not needed (High K).",
    "application_method": "Urea: Broadcast/Drilling. SSP: Placement near seed.",
    "data": "N is low, requiring substantial application. P is moderate, applying a standard maintenance dose is recommended. K is high, further application is generally unnecessary."
  }
}
```

---
