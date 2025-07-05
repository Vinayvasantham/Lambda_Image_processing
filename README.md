# 🖼️ Serverless Image Resizer using AWS Lambda & Terraform

This project automatically resizes images uploaded to an S3 bucket using a serverless architecture powered by **AWS Lambda**, **Amazon S3**, and **Terraform**.

Uploaded `.jpg` images are resized to `128x128` pixels and saved to a destination S3 bucket. All infrastructure is provisioned using Terraform.

---

## 📷 Features

- ✅ Upload an image to `uploads/` in the source S3 bucket
- ✅ Trigger Lambda on image upload via S3 event
- ✅ Resize image using Python & Pillow (PIL)
- ✅ Save resized image to a separate S3 bucket under `resized/`
- ✅ Infrastructure as Code (IaC) with Terraform

---

## 🏗️ Architecture

```
User Upload
   |
   v
[S3 Bucket - Uploads]
       |
       v  (Event Trigger)
[AWS Lambda Function]
       |
       v
[Resized Image → S3 Bucket]
```

---

## 🧪 Technologies Used

- **AWS Lambda**
- **Amazon S3**
- **IAM Roles & Permissions**
- **Python 3.8**
- **Pillow (PIL)**
- **Terraform**

---

## 📝 Lambda Function (Python)

```python
import boto3
import os
from PIL import Image
from io import BytesIO

s3 = boto3.client('s3')
dest_bucket = os.environ['DEST_BUCKET']

def lambda_handler(event, context):
    for record in event['Records']:
        src_bucket = record['s3']['bucket']['name']
        key = record['s3']['object']['key']

        response = s3.get_object(Bucket=src_bucket, Key=key)
        image = Image.open(BytesIO(response['Body'].read()))
        image = image.resize((128, 128))

        buffer = BytesIO()
        image.save(buffer, 'JPEG')
        buffer.seek(0)

        dest_key = f"resized/{key.split('/')[-1]}"
        s3.put_object(Bucket=dest_bucket, Key=dest_key, Body=buffer)
```

---

## 📦 Terraform Infrastructure

Key components in `main.tf`:
- Two S3 buckets (`upload_bucket`, `resized_bucket`)
- IAM Role with Lambda execution permissions
- Lambda function deployment with environment variable
- S3 → Lambda trigger with event filter (`uploads/` and `.jpg`)

---

## 🚀 Getting Started

### 1. Clone this repo
```bash
git clone https://github.com/yourusername/aws-lambda-image-resizer.git
cd aws-lambda-image-resizer
```

### 2. Package Lambda
```bash
pip install pillow -t .
zip -r lambda.zip . -x "*.terraform*" "*.git*" "*.zip"
```

### 3. Deploy with Terraform
```bash
terraform init
terraform apply
```

### 4. Upload an image
Upload a `.jpg` file to the `uploads/` folder in the upload bucket.

Resized image will appear in the destination bucket under `resized/`.

---

## 🛡 IAM & Security Notes

- Lambda uses least privilege IAM role
- Event filter restricts processing to `.jpg` in `uploads/` prefix
- `force_destroy = true` in Terraform is used for demo purposes — use with caution in production

---

## 📂 Folder Structure

```
.
├── lambda_function.py
├── lambda.zip
├── main.tf
├── variables.tf (optional)
├── outputs.tf (optional)
└── README.md
```

---

## 📬 Feedback & Contributions

Feel free to open issues or submit pull requests. Feedback is always welcome!

---

## 📄 License

MIT License
