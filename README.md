# 🎵 Spotlist

> **Discover your next favorite track with a swipe.**
> Spotlist is an AI-powered, Tinder-style song discovery and playlist generation web application. By combining Natural Language Processing (to understand your "vibe") with Machine Learning (to dynamically adapt to your taste), Spotlist serves up a continuous, personalized feed of Spotify tracks.

---

## ✨ Key Features

* **🗣️ Prompt-to-Vibe:** Describe what you want to listen to. Spotlist's AI parses your text into a precise Spotify audio feature vector (danceability, energy, acousticness, etc.).
* **👉 Swipe to Discover:** A smooth, Tinder-like swiping interface built with React Spring and Use-Gesture. 
  * Swipe **Right** to like a track.
  * Swipe **Left** to pass.
  * Swipe **Up** to super-like!
* **🧠 Real-Time ML Adaptation:** Every swipe dynamically updates your "user vibe vector." The Python ML microservice instantly recalculates cosine similarities to serve the next best track seamlessly.
* **⚡ Blazing Fast Architecture:** A robust Node.js backend acts as a high-speed bridge between the React frontend (via Socket.io) and the Python recommendation engine (via WebSockets).
* **🎧 Spotify Integration:** Seamlessly fetches track data, connects to your Spotify profile, and manages your new playlists.

---

## 🛠️ Tech Stack

### Frontend (User Interface)
* **Framework:** React 19 + Vite
* **Routing:** React Router DOM v7
* **Styling:** Tailwind CSS + DaisyUI
* **Animations/Gestures:** `@react-spring/web` & `@use-gesture/react` for fluid, physics-based swipe cards
* **Real-time Client:** Socket.io-client

### Primary Backend (API & Orchestration)
* **Environment:** Node.js + Express
* **Database:** MongoDB (via Mongoose)
* **Real-time Comms:** Socket.io (to Frontend) + `ws` (to Python ML Server)
* **Integrations:** Spotify Web API (OAuth, Track Fetching, Playlist Management)

### ML Microservice (Recommendation Engine)
* **Framework:** Python + FastAPI + Uvicorn
* **Machine Learning:** `scikit-learn`, `numpy`, `joblib`
* **Algorithm:** Cosine Similarity on an optimized dataset of normalized Spotify audio features.

---

## 🏗️ How it Works (The Architecture)

Spotlist operates on a dual-backend architecture to separate standard web traffic from heavy vector calculations:

1. **Initialization:** The user provides a text prompt. The Node.js backend (`/intel` route) processes this into a baseline audio feature dictionary (e.g., `danceability: 0.8, energy: 0.75`).
2. **The Handshake:** Node.js sends this base vibe to the **Python FastAPI server** via a direct WebSocket connection. 
3. **The First Match:** Python normalizes the vibe using a pre-trained `scaler.pkl` and calculates the cosine similarity against thousands of tracks in `features.npy`. The top match is sent back to Node.js, which emits it to React.
4. **The Swipe Loop:** When a user swipes, React sends a Socket.io event to Node.js. Node.js instantly forwards the swipe direction (left, right, up) to Python.
5. **Dynamic Adjustment:** Python applies predetermined weights to the user's vector based on the swipe:
   * *Left (-0.5):* Moves the vibe vector away from the rejected song's features.
   * *Right (+0.8):* Moves the vibe vector towards the liked song's features.
   * *Up (+1.5):* Heavily shifts the vibe vector towards the super-liked song.
6. **Repeat:** The next best track is calculated and pushed to the UI in milliseconds.

---

## 🚀 Getting Started

### Prerequisites
* Node.js (v18+)
* Python (3.9+)
* MongoDB instance
* Spotify Developer Account (Client ID & Secret)

### 1. Clone the Repository
```bash
git clone [https://github.com/yourusername/spotlist.git](https://github.com/yourusername/spotlist.git)
cd spotlist
