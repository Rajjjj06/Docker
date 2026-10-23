# Express and Flask Docker Application

This project contains a Node.js/Express frontend and a Python/Flask backend. The frontend displays a form for a user's name, age, and city. When the form is submitted, Express sends the data as JSON to Flask, and Flask stores the submission in `backend/data.json`.

## Project Requirements Covered

- Express and Node.js frontend
- Form-based data submission
- Flask backend for processing and storing data
- Separate `frontend` and `backend` folders
- A Dockerfile for each service
- Docker Compose configuration with a shared network
- Git and Docker Hub publishing instructions

## Project Structure

```text
.
├── backend/
│   ├── app.py
│   ├── data.json
│   └── requirements.txt
├── frontend/
│   ├── index.js
│   ├── package.json
│   └── views/
│       ├── index.ejs
│       └── success.ejs
├── Dockerfile-backend
├── Dockerfile-frontend
├── docker-compose.yaml
├── .gitignore
└── README.md
```

## How It Works

1. The browser opens the Express frontend at `http://localhost:3000`.
2. The user submits the form.
3. Express receives the form data at `POST /submit`.
4. Express sends a JSON request to the Flask backend at `POST /submit`.
5. Flask validates the fields and appends the data to `backend/data.json`.
6. Express displays the success page.

The Flask backend also exposes `GET /data` to return all saved submissions.

## Run with Docker Compose

### Prerequisites

- Docker Desktop installed and running
- Docker Compose available through `docker compose`

From the project root, build and start both services:

```powershell
docker compose up --build
```

Open the frontend in a browser:

```text
http://localhost:3000
```

The backend is available at:

```text
http://localhost:5000
```

To view saved submissions directly:

```powershell
curl http://localhost:5000/data
```

Stop the services with:

```powershell
docker compose down
```

The services communicate through the `project_network` Docker network. Inside the Compose network, the frontend uses `http://backend-host:5000` as the backend URL.

## Build the Images Individually

The Compose file uses the project root as the build context because both Dockerfiles are in the root directory:

```powershell
docker build -f Dockerfile-backend -t flask-backend .
docker build -f Dockerfile-frontend -t express-frontend .
```

## Docker Hub Images

Both application images have been built and pushed to a private Docker Hub repository. Because the repository is private, access requires signing in with an account that has permission to view and pull the images.

The images are:

- `YOUR_DOCKERHUB_USERNAME/flask-backend:latest`
- `YOUR_DOCKERHUB_USERNAME/express-frontend:latest`

## Build and Upload Images to Docker Hub

Replace `YOUR_DOCKERHUB_USERNAME` with your Docker Hub username.

1. Log in:

   ```powershell
   docker login
   ```

2. Tag the images:

   ```powershell
   docker tag flask-backend YOUR_DOCKERHUB_USERNAME/flask-backend:latest
   docker tag express-frontend YOUR_DOCKERHUB_USERNAME/express-frontend:latest
   ```

3. Push both images to the private repository:

   ```powershell
   docker push YOUR_DOCKERHUB_USERNAME/flask-backend:latest
   docker push YOUR_DOCKERHUB_USERNAME/express-frontend:latest
   ```

The Docker Hub repositories must exist under the account and be configured as private before pushing.

To pull the private images on another machine:

```powershell
docker login
docker pull YOUR_DOCKERHUB_USERNAME/flask-backend:latest
docker pull YOUR_DOCKERHUB_USERNAME/express-frontend:latest
```

## Push the Code to GitHub

Replace `YOUR_GITHUB_USERNAME` and `YOUR_REPOSITORY_NAME` with the appropriate values.

```powershell
git init
git add .
git commit -m "Add Express Flask Docker application"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPOSITORY_NAME.git
git push -u origin main
```

Do not commit passwords, access tokens, `.env` files, virtual environments, dependency folders, or generated cache files. These are excluded by `.gitignore`.

## Run Without Docker

### Backend

From the project root:

```powershell
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
python app.py
```

### Frontend

In a second terminal:

```powershell
cd frontend
npm install
$env:BACKEND_URL="http://localhost:5000"
npm start
```

Then visit `http://localhost:3000`.

## API Endpoints

### `POST /submit`

Accepts JSON in this format:

```json
{
  "name": "Alex",
  "age": "21",
  "city": "London"
}
```

Returns HTTP `201` when the submission is stored. Missing fields return HTTP `400`.

### `GET /data`

Returns the saved submissions from `backend/data.json`. If no data file exists, the backend returns HTTP `404`.

## Notes

- `backend/data.json` is mounted into the backend container through the Compose volume, so submissions remain in the project directory while the containers run.
- The frontend uses Node.js's built-in `fetch`, available in the Node.js 20 image.
- For production use, add stronger validation, persistent database storage, authentication, and a production-grade server configuration.
