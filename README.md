# README: Deepfake Detection Inference Script

## Overview
This script is designed to process test images for deepfake detection using the Roboflow Inference API. It reads image files from a specified directory, sends them for inference, and stores the predictions in a JSON file.

## Requirements
- Python 3.x
- `inference-sdk` (Roboflow's inference library)
- Required Python libraries:
  - `base64`
  - `os`
  - `json`

## Setup Instructions
### 1. Install Dependencies
Ensure that you have the required dependencies installed. If `inference-sdk` is not installed, you can install it using:
```bash
pip install inference-sdk
```

### 2. Update API Key
Replace the placeholder API key with your actual Roboflow API key:
```python
CLIENT = InferenceHTTPClient(
    api_url="https://detect.roboflow.com",
    api_key="K1aE1fYjCXZQSaMsW6ak"  # Replace with your actual API key
)
```

### 3. Set Directory Paths
Ensure the test images are stored in the specified directory:
```python
test_dir = "/content/drive/MyDrive/dataset/test"
output_json_path = "/content/drive/MyDrive/dataset/teamname_predictions.json"
```
Modify `test_dir` and `output_json_path` as needed.

## Code Explanation
### **1. Initialize Roboflow Inference Client**
```python
CLIENT = InferenceHTTPClient(
    api_url="https://detect.roboflow.com",
    api_key="K1aE1fYjCXZQSaMsW6ak"
)
```
This sets up the connection to the Roboflow API using the provided API key.

### **2. Read Image Files from the Test Directory**
```python
image_files = [f for f in os.listdir(test_dir) if f.endswith(('.png', '.jpg', '.jpeg'))]
image_files.sort()
```
This retrieves all image files in the test directory and sorts them to ensure consistent processing order.

### **3. Process Each Image**
For each image in the directory:
- Open and encode the image in Base64.
- Send the encoded image to Roboflow’s inference API.
- Extract the top prediction and assign `fake` or `real` labels based on the output.

```python
with open(image_path, "rb") as img_file:
    encoded_string = base64.b64encode(img_file.read()).decode("utf-8")
result = CLIENT.infer(encoded_string, model_id="deepfake-ebv1o/2")
top_prediction = result.get("top", "unknown")
final_prediction = "fake" if top_prediction == "fake_cifake_images" else "real"
```
This ensures the model correctly identifies deepfake images and assigns appropriate labels.

### **4. Store Predictions in JSON File**
```python
formatted_result = {
    "index": index,
    "prediction": final_prediction
}
results_list.append(formatted_result)
```
Each prediction is stored in a structured format with an image index and its assigned deepfake label.

### **5. Save Predictions to File**
```python
with open(output_json_path, "w") as json_file:
    json.dump(results_list, json_file, indent=2)
```
This writes the collected predictions to a JSON file that follows the required submission format.

## Output Format
The predictions are saved in `teamname_predictions.json` as:
```json
[
  { "index": 1, "prediction": "fake" },
  { "index": 2, "prediction": "real" }
]
```

## Error Handling
If an image fails to process, the script will display an error message:
```python
print(f"Error processing {image_path}: {str(e)}")
```
This ensures that errors do not stop the script from processing the remaining images.

## Running the Script
Execute the script with:
```bash
python inference_script.py
```
Ensure that all required dependencies are installed and configured before running the script.

## Additional Notes
- Ensure that the API key is valid and has access to the Roboflow model.
- The test directory must contain valid images for inference.
- Predictions will be stored in JSON format as required by the competition guidelines.
