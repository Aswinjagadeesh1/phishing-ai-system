# Phishing Detection and URL Analysis

**An educational web application for email classification, URL risk scoring, and explainable results.**

The application combines a JavaScript Naive Bayes email classifier with heuristic adjustments and a weighted URL-rule ensemble. An Express API serves analysis and authentication endpoints, with a browser dashboard and MongoDB-backed storage where available.

**Status:** educational prototype, not a validated production detection service. The original README credits **Nishanth G. E.**; that credit is retained. This repository is hosted in Aswin T. Jagadeesh's project collection.

## How analysis works

| Input | Current implementation |
| --- | --- |
| Email | Naive Bayes trained from embedded examples, with token processing and context-based score adjustments |
| URL | Five hand-written scoring functions combined with fixed weights |
| Explanation | Token contributions, URL-rule votes, and risk-factor descriptions |

The URL class is named `RandomForestURLClassifier` in the source, but its rules are not learned by training a random forest. Scores and confidence fields are application outputs, not established probabilities or measured accuracy.

## Features

- Email and URL analysis, including batch endpoints.
- Explanation panels and scan history.
- JWT authentication and password hashing.
- MongoDB/Mongoose persistence, with in-memory fallback behaviour.
- JSON/CSV export and API documentation.

## Run locally

Use a Node.js version compatible with the locked dependencies and npm. The legacy package manifest says Node 14+, but that lower bound does not establish compatibility with all current dependencies. MongoDB is optional for a temporary demonstration and required for persistent storage.

```bash
git clone https://github.com/Aswinjagadeesh1/phishing-ai-system.git
cd phishing-ai-system
npm install
npm start
```

In a second terminal, from the same project directory:

```bash
node src/backend/frontend-server.js
```

| Service | Address |
| --- | --- |
| Dashboard | http://localhost:8080 |
| API | http://localhost:8081 |
| Health | http://localhost:8081/api/health |
| API documentation | http://localhost:8081/api/docs |

The default local demo account is `admin` / `password123`. Configure `JWT_SECRET`, `DEMO_USERNAME`, and `DEMO_PASSWORD` as process environment variables before using anything beyond disposable demonstration data. `MONGODB_URI` defaults to `mongodb://localhost:27017/phishing-ai-system`; the backend port is controlled by `PORT`.

The server binds to all interfaces by default. Keep it in a trusted local environment: demonstration credentials and authentication configuration are not suitable for public deployment. Scan history can contain email content and should be treated accordingly.

## API overview

| Method | Route | Purpose |
| --- | --- | --- |
| POST | `/api/auth/signup` | Create an account |
| POST | `/api/auth/login` | Obtain an authentication token |
| POST | `/api/analyze-email` | Analyse `emailContent` |
| POST | `/api/analyze-url` | Analyse `url` |
| POST | `/api/analyze-emails-batch` | Batch email analysis |
| POST | `/api/analyze-urls-batch` | Batch URL analysis |
| GET | `/api/data/stats` | Stored analysis statistics |

Use the running API documentation for request details. For example, authenticated email analysis accepts `{"emailContent": "Example email body"}`.

## Repository guide

- [`src/backend/utils/aiEngine.js`](src/backend/utils/aiEngine.js): classification and scoring logic.
- [`src/backend/controllers/`](src/backend/controllers/): authentication and analysis handlers.
- [`src/backend/config/db.js`](src/backend/config/db.js): MongoDB configuration.
- [`src/frontend/`](src/frontend/): browser interface.
- [`PROJECT_DOCUMENTATION.md`](PROJECT_DOCUMENTATION.md): supplementary project notes that may describe earlier versions.

## Evaluation and limitations

No independent benchmark or held-out evaluation is asserted here. Embedded training examples and hand-written URL rules limit generalisation. A safe result does not establish that an email or website is trustworthy.

The `npm test` script is currently a placeholder that exits with an error; it is not an automated test suite. The next useful evaluation would use a separate labelled dataset, a confusion matrix, and analysis of false positives and false negatives.
