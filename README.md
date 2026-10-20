# AgriPredict AI --- AI-Powered Agricultural Prediction Platform

AgriPredict AI is a web application that provides AI-assisted crop-yield
estimates, crop recommendations, and plant disease image analysis. It
combines a React frontend with Supabase authentication, database
storage, and Edge Functions that call an external AI service.

> **Project status:** This version uses an external AI gateway for
> generating responses. It does **not** contain a locally trained
> machine-learning model or a dataset-based evaluation pipeline.
> Predictions are AI-generated guidance and have not been validated as
> agronomic measurements.

## Features

-   **Crop-yield estimation:** Accepts temperature, rainfall, humidity,
    soil type, crop type, and field area, then requests an estimated
    yield and alternative crop recommendations.
-   **Plant disease analysis:** Accepts a plant image and requests a
    possible disease identification, severity level, precautions, and
    additional plant sustainability information.
-   **Authentication:** Sign-up and sign-in using Supabase Auth.
-   **Prediction history:** Stores crop-yield and disease-analysis
    requests and results for signed-in users.
-   **User-specific data access:** PostgreSQL Row-Level Security (RLS)
    policies restrict profile and prediction-history operations by user.
-   **Responsive interface:** Built with React, TypeScript, Tailwind
    CSS, and shadcn/ui components.

## Technology Stack

  Layer               Technologies
  ------------------- -----------------------------------------------
  Frontend            React, TypeScript, Vite
  UI and styling      Tailwind CSS, shadcn/ui, Radix UI
  Backend functions   Supabase Edge Functions (Deno and TypeScript)
  Authentication      Supabase Auth
  Database            Supabase PostgreSQL
  AI integration      Lovable AI Gateway via HTTP requests
  Data exchange       JSON over HTTP

## How It Works

### 1. Crop-yield estimation

1.  The user enters field and crop conditions.
2.  The frontend calls the `predict-crop-yield` Supabase Edge Function.
3.  The function sends the supplied values to the external AI gateway.
4.  The returned text contains a yield estimate, growth outlook, and
    suitable alternative crop suggestions.
5.  The application can save the request and result in prediction
    history.

### 2. Plant disease analysis

1.  The user uploads a plant image.
2.  The frontend calls the `predict-disease` Supabase Edge Function.
3.  The function sends the image to the external AI gateway for disease
    analysis.
4.  A second AI request generates sustainability, plant-lifespan, and
    possible disease-cause information.
5.  The result can be saved to prediction history.

**Important limitation:** These are AI-service-generated responses, not
outputs from a model trained and evaluated within this repository.
Results may be inaccurate and should not replace advice from
agricultural experts.

## Project Structure

``` text
yield-and-heal-main/
├── public/                         # Static assets
├── src/
│   ├── components/                 # Shared and UI components
│   ├── integrations/supabase/      # Supabase client and types
│   ├── pages/
│   │   ├── Auth.tsx                # Sign-in and sign-up
│   │   ├── CropYield.tsx           # Crop-yield form and results
│   │   ├── DiseaseDetection.tsx    # Plant image analysis
│   │   ├── History.tsx             # Saved prediction history
│   │   └── Index.tsx               # Main page
│   ├── App.tsx                     # App routes
│   └── main.tsx                    # Frontend entry point
├── supabase/
│   ├── functions/
│   │   ├── predict-crop-yield/     # Crop-yield AI endpoint
│   │   └── predict-disease/        # Plant disease AI endpoint
│   └── migrations/                 # PostgreSQL schema and RLS policies
├── package.json
└── README.md
```

## Prerequisites

-   Node.js and npm
-   A Supabase project
-   A Lovable AI Gateway API key for the Edge Functions
-   Supabase CLI (for deploying database migrations and Edge Functions)

## Run the Frontend Locally

1.  Clone this repository:

    ``` bash
    git clone https://github.com/YOUR_USERNAME/AgriPredict-AI.git
    cd AgriPredict-AI
    ```

2.  Install dependencies:

    ``` bash
    npm install
    ```

3.  Create a `.env` file in the project root with your own Supabase
    project values:

    ``` env
    VITE_SUPABASE_URL=https://YOUR_PROJECT_ID.supabase.co
    VITE_SUPABASE_PUBLISHABLE_KEY=YOUR_SUPABASE_PUBLISHABLE_KEY
    ```

    Use the URL and publishable/anon key from your own Supabase project.
    Never put a service-role key or AI gateway secret in a frontend
    environment variable.

4.  Start the development server:

    ``` bash
    npm run dev
    ```

5.  Open the local URL printed by Vite in your terminal.

## Supabase Setup

1.  Create or select a Supabase project.

2.  Review the SQL files in `supabase/migrations/` and apply the
    migrations to create the `profiles` and `prediction_history` tables,
    triggers, indexes, and RLS policies.

3.  Configure the frontend environment variables shown above.

4.  Add the AI gateway key as a **server-side Supabase secret**, not in
    the frontend `.env` file:

    ``` bash
    supabase secrets set LOVABLE_API_KEY=YOUR_LOVABLE_API_KEY
    ```

5.  Link the Supabase CLI to your project and deploy the functions:

    ``` bash
    supabase login
    supabase link --project-ref YOUR_PROJECT_ID
    supabase functions deploy predict-crop-yield
    supabase functions deploy predict-disease
    ```

Make sure the Supabase project and deployed functions match the
environment variables used by the frontend. Review authentication and
function access settings before deploying publicly.

## Available Scripts

``` bash
npm run dev       # Start the local development server
npm run build     # Build the production frontend
npm run preview   # Preview the production build
npm run lint      # Run ESLint
```

## Security Notes

-   **Do not commit `.env` files, API keys, passwords, service-role
    keys, or other secrets.**
-   Keep `LOVABLE_API_KEY` in Supabase Edge Function secrets.
-   The frontend needs only the Supabase project URL and
    publishable/anon key.
-   Review the Edge Function authentication settings and add suitable
    access controls, rate limiting, and input-size limits before
    production use.
-   RLS policies help protect user-specific database records, but they
    do not automatically secure every Edge Function endpoint.

## Current Limitations

-   No locally trained crop-yield or plant-disease ML model is included.
-   No dataset-based accuracy, F1-score, MAE, or RMSE has been
    calculated for this implementation.
-   Crop-yield estimates and disease assessments depend on an external
    AI service and may not be scientifically reliable.
-   Live AI requests require a valid gateway secret and may be subject
    to provider availability, rate limits, and usage costs.

## Future Improvements

-   Evaluate predictions against verified, labelled agricultural
    datasets.
-   Train and validate dedicated crop-yield regression and plant-disease
    classification models.
-   Report suitable metrics such as MAE/RMSE for yield estimation and
    precision, recall, F1-score, and confusion matrices for disease
    classification.
-   Add request monitoring, rate limiting, stronger backend
    authorization, and automated tests.

## Disclaimer

AgriPredict AI is an educational project. Its generated estimates and
suggestions are informational only. Confirm important crop-management
and plant-health decisions with reliable agricultural data or a
qualified agricultural professional.

## Author

**Thrisha K**

