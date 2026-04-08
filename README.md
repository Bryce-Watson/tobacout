# Tobacout

Tobacout is a Next.js 16 app that turns a smoker's input data into a visual 30-year risk timeline. The app combines state-level CDC PLACES data with Gemini-powered photo generation to show the health, financial, and environmental impact of continued smoking.

## What it does

- Accepts a photo upload or a built-in sample image.
- Collects age, years smoked, cigarettes per day, price per pack, and U.S. state.
- Calls a server route that pulls CDC PLACES data for the selected state and projects smoking-related risk at `0`, `10`, `20`, and `30` years.
- Calls a Gemini image generation route to create future smoking-progressed face images for `+10`, `+20`, and `+30` years.
- Presents a timeline with health-risk estimates, money spent, cigarette waste, water use, CO2 impact, and estimated life lost.

## Demo

[![Tobacout Demo](https://img.youtube.com/vi/yBvzbDMu9iE/0.jpg)](https://youtu.be/yBvzbDMu9iE)

## Stack

- Next.js `16.2.2` with the App Router
- React `19`
- TypeScript
- Tailwind CSS `4`
- Google Gemini APIs for image generation
- CDC PLACES public data API for state-level health inputs

## Project structure

- [`app/page.tsx`]: main multi-step UI
- [`app/api/auth/statRouter/route.ts`]: POST endpoint for smoking risk analysis
- [`app/api/auth/smokingRisk.ts`]: parsing, CDC fetches, and risk timeline calculations
- [`app/api/auth/photoGen/route.ts`]: POST endpoint for Gemini image generation
- [`app/api/auth/smokingRiskData.json`]: local smoking bucket dataset

## Local development

### Prerequisites

- Node.js 20+
- npm
- A Gemini API key with access to the image generation model used by the app

### Install

```bash
npm ci
```

### Environment variables

Create a local env file such as `.env.local` and define:

```bash
GEMINI_API_KEY=your_api_key_here
```

`GEMINI_API_KEY` is required by [`app/api/auth/photoGen/route.ts`]. Without it, the photo generation route returns an error.

### Run

```bash
npm run dev
```

Open `http://localhost:3000`.

### Production build

```bash
npm run build
npm run start
```

The project currently uses the standard Next.js Node server build flow.

## API overview

### `POST /api/auth/statRouter`

Accepts JSON:

```json
{
  "age": "35",
  "yearsSmoked": "10",
  "cigarettesPerDay": "12",
  "state": "AZ"
}
```

Returns a success payload containing:

- selected smoking bucket
- normalized input values
- timeline entries for `0`, `10`, `20`, and `30` years
- projected heart disease, stroke, and lung disease percentages

### `POST /api/auth/photoGen`

Accepts `multipart/form-data` with:

- `noOfCigs`
- `pastYears`
- `futureYears`
- `userPhoto`

Returns generated future face image data as base64 strings.

## Notes and limitations

- The risk timeline depends on live CDC PLACES API responses for the selected U.S. state.
- The image-generation path depends on Gemini availability, model access, and valid API credentials.
- The current UI requests future images for `10`, `20`, and `30` years in parallel after the risk timeline is computed.
- `pricePerPack` is collected in the UI and used for display calculations on the client.

## Scripts

- `npm run dev`: start the local development server
- `npm run build`: create a production build
- `npm run start`: run the production server
- `npm run lint`: run ESLint
