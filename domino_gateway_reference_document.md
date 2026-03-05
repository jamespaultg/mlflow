
# Domino Gateway & Model Endpoint Implementation Guide

## 1. Understanding the Client Requirement

The client wants a **single generic endpoint (Gateway)** that performs:

1. Schema validation of incoming request
2. Routing to the appropriate model endpoint
3. Calling the Domino model endpoint
4. Adding metadata:
   - correlation_id
   - request timestamp
   - response timestamp
   - latency
5. Returning standardized response

Architecture:

Client
   |
   v
Gateway (Domino Published App – FastAPI)
   |
   v
Domino Model Endpoint(s)
   - irish_home_price_reg
   - irish_home_price_clf


## 2. Responsibilities of Each Component

### Gateway (Domino Published App)

Responsibilities:

- Input schema validation
- Correlation ID creation
- Request logging
- Routing to the correct model endpoint
- Calling model endpoint
- Handling authentication
- Returning standardized response

Gateway should **NOT perform model inference**.

### Model Endpoint (Domino)

Responsibilities:

- Load MLflow model
- Run preprocessing pipeline
- Perform prediction
- Return result


## 3. Routing Multiple Models

Gateway uses a routing configuration.

Example environment variable:

MODEL_ROUTES_JSON

Example:

{
  "irish_home_price_reg": {
    "url": "https://domino/.../irish-regression-endpoint",
    "token": "MODEL_ACCESS_TOKEN"
  },
  "irish_home_price_clf": {
    "url": "https://domino/.../irish-classification-endpoint",
    "token": "MODEL_ACCESS_TOKEN"
  }
}

The gateway reads this variable and routes requests accordingly.


## 4. Example Gateway Request

POST /predict

{
  "model_key": "irish_home_price_reg",
  "inputs": [
    {
      "sale_year": 2021,
      "sale_month": 1,
      "address": "APT 15 SANDON COVE",
      "county": "Dublin",
      "eircode": "D03RY60"
    }
  ]
}


## 5. Example Gateway Response

{
  "correlation_id": "c1f8c0d4-1234-4567-8901-abcdef123456",
  "model_key": "irish_home_price_reg",
  "latency_ms": 120,
  "result": {
      "prediction": [345000.23]
  }
}


## 6. Authentication

If Domino Model Endpoint security is enabled, requests must include:

Authorization: Bearer <MODEL_ACCESS_TOKEN>

Token is generated in Domino Model Endpoint settings.

Common causes of 404 / Unauthorized:

- Endpoint requires authentication
- Missing Authorization header
- Incorrect endpoint URL
- Token not valid


## 7. Deploying Gateway as Domino App

Steps:

1. Upload gateway code into Domino project
2. Go to:

Deployments → Apps → Publish App

3. Launch command:

uvicorn app.main:app --host 0.0.0.0 --port 8888

4. Add environment variable:

MODEL_ROUTES_JSON


## 8. Official Domino Documentation References

### Publish Domino Apps
https://docs.dominodatalab.com/en/cloud/user_guide/cd0095/publish-and-share-an-app/

### Deploy Domino Model Endpoints
https://docs.dominodatalab.com/en/latest/user_guide/8dbc91/deploy-domino-endpoints/

### Endpoint Authorization Modes
https://docs.dominodatalab.com/en/latest/user_guide/e038f2/select-domino-endpoint-authorization-mode/

### Endpoint Security
https://docs.dominodatalab.com/en/latest/user_guide/88a0ef/domino-endpoint-security/


## 9. Domino Support Articles

FastAPI Published App Example:
https://support.domino.ai/support/s/article/Simple-FastAPI-example-as-a-Published-App

FastAPI Multi Endpoint Example:
https://support.domino.ai/support/s/article/FastAPI-App-with-Multiple-Endpoints


## 10. Helpful YouTube Videos

Model Management & Deployment in Domino:
https://www.youtube.com/watch?v=XnfAe_0OKxY

Domino MLOps Platform Overview:
https://www.youtube.com/watch?v=xUmLEx18Vk8

Domino Application Development Deep Dive:
https://www.youtube.com/watch?v=_iwwau5h3xI


## 11. Summary

Client calls:

POST /predict

Gateway performs:

1. Validate request
2. Generate correlation ID
3. Route to correct model endpoint
4. Call Domino model endpoint
5. Return standardized response

Architecture:

Client
  |
  v
Gateway (Domino App)
  |
  +---- irish_home_price_reg
  |
  +---- irish_home_price_clf
