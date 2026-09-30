# Round 2 build

## What changed
- Stable FastAPI entry point: `backend.app.main:app`.
- Background video processing with a cancellation event.
- `Restart` replaces a queued/processing analysis instead of returning the old stuck job.
- `Upload video` runs a new uploaded video on the backend.
- 14 YOLO classes are grouped into dashboard categories: Car, Bus, Truck, Two-wheeler, Three-wheeler, Van, Bicycle, Other.
- Unique vehicle counts are calculated from tracking IDs, not frame detections.
- Stopped-vehicle incidents are recorded.
- Density, congestion, flow, heatmap, frame statistics and live state are retained.
- Detection metadata is saved so category-filter videos can be generated without running YOLO a second time.
- Dashboard category filters create an actual filtered detection video.
- Dashboard watches the processing frame counter. If it stops advancing for 30 seconds, it requests a fresh backend analysis.
- Frontend API polling uses the configured `VITE_API_BASE_URL`/`VITE_API_URL` instead of an incorrect relative URL.

## Backend
From the project root:

```powershell
.\.venv\Scripts\Activate.ps1
python -m uvicorn backend.app.main:app --reload --port 8000
```

Open:

`http://127.0.0.1:8000/docs`

## Frontend

```powershell
cd frontend
npm install
npm run dev
```

The frontend defaults to `http://127.0.0.1:8000`. If needed create `frontend/.env`:

```env
VITE_API_BASE_URL=http://127.0.0.1:8000
```

## Video workflow

1. Start backend.
2. Start frontend.
3. Click **Restart** for the demo video in `data/raw/traffic.mp4`, or click **Upload video** for a new video.
4. Wait for processing to complete.
5. Click Car / Bus / Truck / 2-Wheeler / 3-Wheeler etc.
6. The dashboard requests a filtered video generated from the stored YOLO detections.

## Important
FFmpeg is recommended on PATH for reliable browser H.264 MP4 output. The backend falls back to the OpenCV MP4 if FFmpeg is unavailable, but browser compatibility is better with FFmpeg/H.264.
