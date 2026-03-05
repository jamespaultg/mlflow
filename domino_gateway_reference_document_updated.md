
# Domino Gateway & Model Endpoint Implementation Guide (Updated)

## Architecture Overview

Client
   |
   v
Gateway (Domino Published App – FastAPI)
   |
   v
Domino Model Endpoint(s)
   - irish_home_price_reg
   - irish_home_price_clf

## Gateway Responsibilities (Instance 1)

- Validate raw Irish property input schema
- Perform feature engineering (Feature Creation Component)
- Generate and propagate correlation IDs
- Route to correct Domino model endpoint
- Attach authentication (Bearer token if required)
- Return standardized response with latency metadata

## Model Endpoint Responsibilities (Instance 2)

- Load model from Domino Model Registry
- Accept engineered features
- Perform inference only
- Return prediction result

## Example Request

POST /predict

{
  "model_key": "irish_home_price_reg",
  "inputs": [
    {
      "county": "Dublin",
      "town": "Dublin 3",
      "property_type": "Apartment",
      "bedrooms": 2,
      "bathrooms": 1,
      "floor_area_sqm": 75.0,
      "year_built": 2005,
      "sale_date": "2021-01-15",
      "latitude": 53.3600,
      "longitude": -6.2300
    }
  ]
}

## Example Response

{
  "correlation_id": "uuid-generated",
  "model_key": "irish_home_price_reg",
  "latency_ms": 120,
  "result": {
      "prediction": [345000.23]
  }
}

## Key Design Decision

Feature engineering is implemented in Instance 1 to align with assessment requirements. 
Model endpoints focus strictly on inference using registry-managed artifacts.
