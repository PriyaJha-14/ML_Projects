# 🎬 Movie Recommendation System

A full-stack movie discovery and recommendation app. Search any movie, open its details page, and get recommendations from two engines: a **content-based TF-IDF model** trained locally, and **genre-based suggestions** from the TMDB API.

- **Backend:** FastAPI (REST API, deployed on Render)
- **Frontend:** Streamlit (poster grid UI with search autocomplete)
- **ML:** TF-IDF + cosine similarity (scikit-learn)
- **Data/Images:** [TMDB API](https://www.themoviedb.org/documentation/api)


> The Render free tier sleeps when idle, so the first request after a pause can take 30–60 seconds. Later requests are fast.

---

## ✨ Features

- 🏠 **Home feed** with five categories: Trending, Popular, Top Rated, Now Playing, Upcoming
- 🔎 **Keyword search** with a suggestions dropdown (title + year) and a poster results grid
- 📄 **Movie details page**: poster, release date, genres, overview, backdrop
- 🔎 **Similar Movies (TF-IDF):** content-based recommendations with similarity scores, enriched with TMDB posters
- 🎭 **More Like This (Genre):** popular movies from the selected movie's primary genre
- 🛟 **Graceful fallbacks:** if the bundled recommendation call fails, the app falls back to genre-only recommendations
- 🎛️ Adjustable grid width (4–8 columns) and shareable URLs (`?view=details&id=<tmdb_id>`)

---


### Content-based recommendations (TF-IDF)

1. The movie data is vectorised offline with **TF-IDF** and saved as pickle files.
2. At startup the API loads the dataset (`df.pkl`), the title-to-row lookup (`indices.pkl`), the vectoriser (`tfidf.pkl`) and the sparse matrix (`tfidf_matrix.pkl`).
3. For a given title, the API finds its row, computes **cosine similarity** against every movie in the matrix, and returns the top N most similar titles (excluding the movie itself).
4. Each recommended title is then looked up on TMDB to attach a poster and TMDB ID so it can be clicked and opened.

### Genre-based recommendations

The API reads the selected movie's first genre and queries TMDB's `/discover/movie` endpoint sorted by popularity, excluding the selected movie.

---

---

## 🔌 API Documentation

The backend is built with FastAPI, which generates interactive API documentation automatically. Once the server is running, open `/docs` (Swagger UI) or `/redoc` to explore and try every endpoint.

- **FastAPI documentation:** https://fastapi.tiangolo.com/
- **FastAPI tutorial (path/query parameters, response models):** https://fastapi.tiangolo.com/tutorial/
- **FastAPI interactive docs (Swagger UI and ReDoc):** https://fastapi.tiangolo.com/features/#automatic-docs
- **TMDB API reference (the external API this project calls):** https://developer.themoviedb.org/reference/intro/getting-started

---

## 🚀 Run Locally

### Prerequisites

- Python 3.10+ (the code uses the `dict | None` type syntax)
- A free [TMDB API key](https://www.themoviedb.org/settings/api)

### 1. Clone and install

```bash
git clone https://github.com/PriyaJha-14/Movie_Recommendation_System.git
cd Movie_Recommendation_System

python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

pip install -r requirements.txt
```

### 2. Add your TMDB key

Create a `.env` file in the project root:

```env
TMDB_API_KEY=your_tmdb_api_key_here
```

The backend refuses to start if this key is missing. Never commit `.env`.

### 3. Start the backend

```bash
uvicorn main:app --reload --port 8000
```

Open http://127.0.0.1:8000/docs to try the endpoints.

### 4. Start the frontend

The frontend reads the backend URL from the `API_BASE` environment variable (default: the deployed Render API).

```bash
# Windows (cmd)
set API_BASE=http://127.0.0.1:8000
# Windows (PowerShell)
$env:API_BASE="http://127.0.0.1:8000"
# macOS / Linux
export API_BASE=http://127.0.0.1:8000

streamlit run app.py
```

Open http://localhost:8501.

---

## ☁️ Deployment

**Backend (Render, Web Service)**

| Setting | Value |
|---|---|
| Build command | `pip install -r requirements.txt` |
| Start command | `uvicorn main:app --host 0.0.0.0 --port $PORT` |
| Environment variable | `TMDB_API_KEY` |

**Frontend (e.g. Streamlit Community Cloud):** point it at `app.py` and set `API_BASE` to your Render URL.

---

## 🛠️ Tech Stack

| Layer | Tools (official documentation) |
|---|---|
| Frontend | [Streamlit](https://docs.streamlit.io/), [Requests](https://requests.readthedocs.io/) |
| Backend | [FastAPI](https://fastapi.tiangolo.com/), [Uvicorn](https://www.uvicorn.org/), [HTTPX](https://www.python-httpx.org/), [Pydantic](https://docs.pydantic.dev/) |
| ML / Data | [scikit-learn](https://scikit-learn.org/stable/) ([TF-IDF](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html), [cosine similarity](https://scikit-learn.org/stable/modules/metrics.html#cosine-similarity)), [pandas](https://pandas.pydata.org/docs/), [NumPy](https://numpy.org/doc/), [SciPy sparse matrices](https://docs.scipy.org/doc/scipy/reference/sparse.html) |
| External API | [TMDB API](https://developer.themoviedb.org/docs) |
| Config | [python-dotenv](https://saurabh-kumar.com/python-dotenv/) |
| Deployment | [Render](https://render.com/docs), [Streamlit Community Cloud](https://docs.streamlit.io/deploy/streamlit-community-cloud) |

---

## ⚠️ Notes & Limitations

- **Pickle compatibility:** the `.pkl` files were created with specific library versions. Keep `scikit-learn`, `pandas`, `numpy` and `scipy` pinned as in `requirements.txt`, or regenerate the files from `movies.ipynb`.
- **Coverage:** TF-IDF recommendations only work for titles in the local dataset. Movies outside it get genre-based recommendations only.
- **Genre logic** uses only the movie's first genre.
- **Latency:** each TF-IDF recommendation triggers a TMDB lookup for its poster, so the details page makes several external calls.

## 🔭 Future Improvements

- Combine all genres and TF-IDF scores into a single hybrid ranking
- Fetch recommendation posters concurrently (`asyncio.gather`) and cache TMDB responses
- Add user ratings / collaborative filtering
- Filters by year, language and rating
- Unit tests and a CI workflow

---

## 🙏 Acknowledgements

This product uses the TMDB API but is not endorsed or certified by TMDB. Movie data and images are provided by [The Movie Database](https://www.themoviedb.org/).

## 👩‍💻 Author

**Priya Jha** · [GitHub](https://github.com/PriyaJha-14)

If you found this project useful, consider giving it a ⭐
