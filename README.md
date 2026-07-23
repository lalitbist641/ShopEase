# ShopEase — live demo storefront with the Aria AI chatbot

A professional e-commerce storefront (static) with the **Aria** support chatbot as a
floating widget. Hosting has two parts:

- **Frontend** (`index.html`) → **GitHub Pages** (free, static).
- **Chat backend** (`backend/server.py`) → **Render** (free, runs Python + calls OpenAI).

The two are wired together: the widget in `index.html` calls the Render backend URL.

> This is your own demo site. The "internal" data in the bot is fake — never put real
> secrets in it, and treat the public deployment as a test instance.

---

## Part A — Deploy the chat backend on Render (do this first)

You need the backend URL before you finish the frontend.

1. Put this whole `shopease-site/` folder in a **GitHub repo** (see "Create the repo" below).
2. Go to <https://render.com>, sign up, and click **New → Web Service**.
3. Connect your GitHub repo.
4. Configure:
   - **Root Directory:** `backend`
   - **Runtime:** Python 3
   - **Build Command:** *(leave blank)*
   - **Start Command:** `python3 server.py`
5. Add **Environment Variables**:
   - `OPENAI_API_KEY` = your OpenAI key (`sk-...`)
   - `OPENAI_MODEL` = `gpt-4o-mini`
6. Click **Create Web Service**. After it builds, you get a URL like
   `https://shopease-chat.onrender.com`.
7. Test it: open `https://shopease-chat.onrender.com/health` → should show `{"ok":true}`.

## Part B — Point the frontend at your backend

1. Open `index.html` and find this line near the bottom:
   ```js
   const BACKEND_URL = "https://YOUR-BACKEND.onrender.com/chat";
   ```
2. Replace it with your Render URL, keeping `/chat` at the end, e.g.:
   ```js
   const BACKEND_URL = "https://shopease-chat.onrender.com/chat";
   ```
3. Save and commit.

## Part C — Deploy the frontend on GitHub Pages

1. In your GitHub repo, go to **Settings → Pages**.
2. Under **Source**, choose **Deploy from a branch**, branch **main**, folder **/(root)**.
   *(If the site is in a subfolder, choose that folder, or move `index.html` to the repo root.)*
3. Save. After a minute you get a live URL like
   `https://YOURNAME.github.io/shopease-site/`.
4. Open it — the shop loads, and clicking the blue chat button talks to Aria live.

---

## Create the repo (from this folder)

On your computer, inside `shopease-site/`:

```bash
git init
git add -A
git commit -m "ShopEase live demo storefront + Aria chat backend"
git branch -M main
git remote add origin https://github.com/YOURNAME/shopease-site.git
git push -u origin main
```

## Notes

- **Render free tier sleeps** after inactivity, so the first chat message after a while
  may take ~30s to wake it up. That's normal on the free plan.
- **CORS** is already handled by the backend, so GitHub Pages can call it.
- To test it with PIScanner once live (it's your own site, so authorized):
  ```bash
  piscan probe-site https://YOURNAME.github.io/shopease-site/ --profile <profile>.json --benign --headful
  ```
  (Build a profile with the widget selectors: launcher `#chat-launcher`, input `#ci`,
  send `#sb`, messages `.msg.bot`.)
