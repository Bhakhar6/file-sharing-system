# File Sharing System

A secure and role-based file-sharing API built with FastAPI, MySQL, and SendGrid. This system enables Ops users to upload files, and Client users to list and download them—secured via JWT-based authentication and email verification.

## Features
- User Roles:
  - Ops: Authorized to upload files (.pptx, .docx, .xlsx).
  - Client:  Can view and download files only after email verification.
- Endpoints:
  
    | Endpoint                | Method | Description                                                  |
    | ----------------------- | ------ | ------------------------------------------------------------ |
    | `/signup`               | POST   | Register a new user (email verification for clients).        |
    | `/verify-email/{token}` | GET    | Verify user email via SendGrid.                              |
    | `/login`                | POST   | Authenticate and receive a JWT token.                        |
    | `/upload-file`          | POST   | Upload files (Ops only, supports `.pptx`, `.docx`, `.xlsx`). |
    | `/list-files`           | GET    | List all uploaded files (Client only).                       |
    | `/download-file/{id}`   | GET    | Generate a secure download link (Client only).               |
    | `/download/{token}`     | GET    | Download the file using a secure link.                       |
  

- Security:
  - JWT-based authentication for protected routes.
  - Email verification via SendGrid for Client users.
  - File type validation to allow only specific document formats.
- Storage: Files stored in the uploads/ directory.

## Tech Stack
- Backend: FastAPI
- Database: MySQL
- Email Service: SendGrid
- Storage: Local `uploads/` directory.
  
## Prerequisites
- Python: 3.13+
- MySQL: 8.0+
- SendGrid: Account for email verification
- Git: For cloning the repository
- Postman (optional): For testing API endpoints

## Installation
1. Clone the Repository:
   ```bash
   git clone https://github.com/Bhakhar6/file-sharing-system.git
   cd File-Sharing-System
   ```

2. Create and Activate Virtual Environment
   ```bash
   python -m venv venv
   source venv/bin/activate        # On Windows: venv\Scripts\activate
   ```
3. Install Dependencies
   ```bash
   pip install -r requirements.txt
   ```
4. Configure Environment Variables
   ```bash
   cp .env.example .env
   nano .env
   ```
   - Update .env file with:
     ```env
     SECRET_KEY=your-secure-secret-key
     DATABASE_URL=mysql+mysqlconnector://user:password@localhost:3306/file_sharing
     SENDGRID_API_KEY=SG.your-sendgrid-api-key
     UPLOAD_FOLDER=uploads
     ```
   - Use python -c "import secrets; print(secrets.token_hex(32))" to generate a secure secret key.
   - Replace DB credentials and SendGrid API key accordingly.

5. Set Up MySQL Database
   ```sql
   mysql -u root -p
   CREATE DATABASE file_sharing;
   EXIT;
   ```
   - Tables (users, files) are created automatically on startup

6. Run the Application
   ```bash
   uvicorn main:app --host 0.0.0.0 --port 8000
   ```
   - Access API docs at: http://localhost:8000/docs

---

## API Usage

### Register a User
#### POST `/signup`
  - Headers
    ```http
    Content-Type: application/json
    ```
  - Body
    ```json
    {
      "email": "client@example.com",
      "password": "client_password123"
    }
    ```
   - A verification email will be sent. Click the link to activate the account.

--- 

### Verify Email
#### GET `/verify-email/{token}`
- Open the tokenized link sent via email to complete verification.

---

### Log In
#### POST `/login`
- Headers:
  ```http
  Content-Type: application/x-www-form-urlencoded
  ```
- Body:
  ```ini
  username=client@example.com
  password=client_password123
  ```
- Response
  ```json
  {
  "access_token": "eyJ...",
  "token_type": "bearer"
  }
  ```
  - Save the access_token for authenticated requests.

---

### Upload a File (Ops Only)
#### POST `/upload-file`
- Headers:
  ```http
  Authorization: Bearer <ops_token>
  ```
- Body(form-data):
  
    | Key  | Type | Value     |
  | ---- | ---- | --------- |
  | file | File | test.docx |

  - Only .pptx, .docx, and .xlsx files are allowed.

---

### List Files (Client Only)
#### GET `/list-files`
- Headers:
  ```http
  Authorization: Bearer <client_token>
  ```
- Response:
  ```json
  [
    {
      "filename": "test.docx",
      "id": 1,
      "upload_time": "2025-07-03T00:06:00",
      "uploaded_by": 1
    }
  ]
  ```

  ---

### Download a File (Client Only)
Step 1: Generate Secure Download Link
- GET /download-file/{id}
- Headers
  ```http
  Authorization: Bearer <client_token>
  ```
- Response:
  ```json
  {
  "download_link": "http://localhost:8000/download/eyJ...",
  "message": "success"
  }
  ```
Step 2: Download the File
- GET /download/{token}
- Use the link in browser, Postman, or curl.





















