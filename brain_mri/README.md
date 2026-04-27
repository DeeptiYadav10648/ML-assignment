# 🧠 Brain MRI Tumor Detection Web App

A complete Flask-based web application for detecting brain tumors in MRI images using a pre-trained CNN model.

## 📋 Project Overview

This project is designed for **B.Tech 2nd Year (Semester 4) students** to understand:
- ✅ How to build Flask web applications
- ✅ How to integrate machine learning models with web apps
- ✅ How to handle file uploads and image processing
- ✅ How to deploy applications to the cloud (AWS EC2)

### What This App Does:
1. Users upload a brain MRI image through a web interface
2. The image is preprocessed (resized, normalized)
3. A pre-trained CNN model analyzes the image
4. The app displays the prediction (Tumor Detected/Not Detected) with confidence score

---

## 📁 Project Structure

```
brain_mri/
├── app.py                      # Main Flask application
├── requirements.txt            # Python dependencies
├── model/
│   └── brain_tumor_model1.h5   # Pre-trained CNN model
├── static/
│   └── uploads/                # Folder to store uploaded images
├── templates/
│   ├── index.html              # Upload page
│   └── result.html             # Results page
└── README.md                   # This file
```

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|-----------|
| **Backend** | Flask 2.3.2 |
| **ML Model** | TensorFlow/Keras 2.13.0 |
| **Image Processing** | Pillow (PIL) 10.0.0 |
| **Frontend** | HTML5 + CSS3 |
| **Web Server** | Gunicorn 21.2.0 |
| **Deployment** | AWS EC2 |

---

## ⚡ Quick Start (Local Development)

### Prerequisites:
- Python 3.9 or higher
- pip (Python package manager)
- Git (optional)

### Step 1: Clone or Download Project
```bash
cd path/to/brain_mri
```

### Step 2: Create Virtual Environment (Recommended)
```bash
# On Windows
python -m venv venv
venv\Scripts\activate

# On Mac/Linux
python3 -m venv venv
source venv/bin/activate
```

### Step 3: Install Dependencies
```bash
pip install -r requirements.txt
```

### Step 4: Verify Model File
Ensure `model/brain_tumor_model1.h5` exists in the project directory.

### Step 5: Run the Application
```bash
python app.py
```

### Step 6: Access the App
Open your browser and go to:
```
http://localhost:5000
```

---

## 📚 Code Explanation

### 1. **Flask Setup** (app.py lines 1-50)
```python
app = Flask(__name__)
app.config['MAX_CONTENT_LENGTH'] = 16 * 1024 * 1024  # Max 16MB
```
- Creates Flask application instance
- Sets maximum file upload size to 16MB

### 2. **Model Loading** (app.py lines 52-61)
```python
model = load_model('model/brain_tumor_model1.h5')
```
- Loads the pre-trained CNN model
- Model stays in memory for fast predictions

### 3. **Image Preprocessing** (app.py lines 67-107)
```python
def preprocess_image(image_path, img_size=224):
```
- **Resize**: Scales image to 224x224 pixels (model input size)
- **Normalize**: Converts pixel values from [0-255] to [0-1]
- **Expand dimensions**: Adds batch dimension required by model

### 4. **Prediction Logic** (app.py lines 110-166)
```python
def predict_tumor(image_path):
```
- Preprocesses image
- Feeds to CNN model
- Returns prediction + confidence score
- Handles errors gracefully

### 5. **Flask Routes** (app.py lines 169-250)

| Route | Method | Purpose |
|-------|--------|---------|
| `/` | GET | Display upload form |
| `/predict` | POST | Process upload & predict |
| `/upload-new` | GET | Return to home page |

### 6. **Frontend** (HTML/CSS)
- **index.html**: Clean upload interface with gradient background
- **result.html**: Shows prediction with confidence bar and uploaded image

---

## 🔍 How the Model Works

### Input:
- Brain MRI image (JPG, PNG, GIF)
- Size: Any (resized to 224x224)

### Processing:
1. Image loaded and converted to RGB
2. Resized to 224x224 pixels
3. Pixel values normalized to [0, 1]
4. Passed through CNN layers

### Output:
- **Confidence Score** (0-100%)
- **Prediction**:
  - If score > 50%: "Tumor Detected ⚠️"
  - If score < 50%: "No Tumor Detected ✓"

---

## 📖 Understanding Key Functions

### `preprocess_image(image_path, img_size=224)`
**What it does**: Prepares image for the model
- Loads image file
- Converts to RGB if needed
- Resizes to 224x224
- Normalizes pixel values
- Adds batch dimension

**Why?**: CNN models expect specific input format

### `predict_tumor(image_path)`
**What it does**: Makes prediction using the model
- Calls `preprocess_image()`
- Runs model.predict()
- Interprets output as percentage
- Returns result dictionary

**Output example**:
```python
{
    'success': True,
    'prediction': 'Tumor Detected ⚠️',
    'confidence': 87.34,
    'message': 'Prediction successful'
}
```

### `@app.route('/predict', methods=['POST'])`
**What it does**: Handles image uploads
- Validates file existence & extension
- Saves file securely
- Calls `predict_tumor()`
- Returns result page

---

## 🚀 Deployment to AWS EC2

See [AWS_DEPLOYMENT.md](AWS_DEPLOYMENT.md) for complete step-by-step instructions.

**Quick Summary**:
1. Launch EC2 instance (Ubuntu 20.04)
2. Connect via SSH
3. Install Python & dependencies
4. Upload project files
5. Run with Gunicorn
6. Configure security groups
7. Access via public IP

---

## ⚠️ Important Notes

### Model Training
- This project uses a **pre-trained model**
- Model file: `brain_tumor_model1.h5`
- We do NOT retrain the model
- The model should already be trained on MRI images

### Medical Disclaimer
⚠️ **IMPORTANT**: This application is for **educational purposes only**.
- Results should NOT be used for actual medical diagnosis
- Always consult qualified medical professionals
- This is a demonstration of ML + Web integration

### File Upload Limits
- Maximum file size: **16MB**
- Allowed formats: **JPG, PNG, GIF**
- Images are stored in `static/uploads/`

---

## 🐛 Troubleshooting

### Problem: "Model not found"
```
✗ Error loading model: Model not found
```
**Solution**: Ensure `model/brain_tumor_model1.h5` exists in project directory.

### Problem: "Permission denied" when uploading
**Solution**: Ensure `static/uploads/` folder has write permissions:
```bash
chmod 755 static/uploads/  # On Mac/Linux
```

### Problem: "Port 5000 already in use"
**Solution**: Either:
- Stop other application using port 5000
- Or run on different port: `python app.py --port 5001`

### Problem: Large images cause timeout
**Solution**: Reduce image size or increase timeout in gunicorn:
```bash
gunicorn -w 4 -b 0.0.0.0:5000 --timeout 120 app:app
```

---

## 📊 File Size Reference

| File | Size | Purpose |
|------|------|---------|
| `app.py` | ~12 KB | Flask application |
| `index.html` | ~6 KB | Upload page |
| `result.html` | ~8 KB | Results page |
| `brain_tumor_model1.h5` | ~100-500 MB | CNN model |

---

## 💡 Learning Outcomes

After completing this project, you will understand:
1. ✅ Flask application structure and routing
2. ✅ File upload handling in Flask
3. ✅ Image processing with PIL and NumPy
4. ✅ Integrating ML models with web apps
5. ✅ HTML/CSS for web interfaces
6. ✅ Deploying Python apps to cloud servers

---

## 🎯 Presentation Tips for Your Semester

### What to Show:
1. **Live Demo**: Upload a test image and show the prediction
2. **Code walkthrough**: Explain preprocessing and prediction steps
3. **Architecture diagram**: Show frontend → backend → model flow
4. **Results**: Show different predictions with confidence scores

### Key Points to Mention:
- "This project integrates machine learning with web development"
- "The model was pre-trained on thousands of MRI images"
- "Flask provides the web interface, Keras runs the AI model"
- "AWS EC2 allows us to deploy to the cloud"

### Possible Questions & Answers:
**Q: How accurate is this model?**
A: This depends on the model. Typically CNN models for tumor detection achieve 85-95% accuracy on test data.

**Q: Why preprocess the image?**
A: The model was trained on 224x224 images with normalized values. We must match that format.

**Q: Can this replace a doctor?**
A: No! This is a proof-of-concept. Real diagnosis requires medical professionals.

---

## 📞 Need Help?

### Common Issues:
- **Python not installed**: Download from python.org
- **TensorFlow slow**: First run takes time to compile
- **GPU support**: Install `tensorflow-gpu` for faster predictions
- **Model accuracy**: Accuracy depends on model training quality

---

## 🎓 Educational Value

This project covers:
- **Python Web Development**: Flask framework
- **Machine Learning Integration**: Loading and using trained models
- **Image Processing**: PIL for image manipulation
- **Cloud Deployment**: AWS EC2 instance management
- **Full Stack Development**: Frontend + Backend + ML

Perfect for a semester project! 🚀

---

## 📝 License

Educational project for learning purposes.

---

## 👨‍💻 Author Notes

This template is designed for **maximum clarity** and **educational value**. 
Every function has detailed comments explaining the "what" and "why".

Good luck with your presentation! 🎉
