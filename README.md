<!-- 

GPT:
https://chatgpt.com/share/68b23d5d-db48-8001-abaa-9c59231c8e30

Repo:
https://github.com/rarrdeguzman/my-react-app

Site:
https://rarrdeguzman.github.io/my-react-app/

-->

# React + Vite App Deployment to GitHub Pages

This document summarizes the steps to host a **React + Vite app** on GitHub Pages with **automatic deployment using GitHub Actions**.

Each section includes the **purpose** of the step.

---

## 1️⃣ Project Setup
**Purpose:** Set up the project structure and install all required dependencies to run a React + Vite application.
- Create a React + Vite app.
- Install dependencies:

```bash
npm install react react-dom
npm install --save-dev vite @vitejs/plugin-react eslint gh-pages
```

- Optional: Install React Router:

```bash
npm install react-router-dom
```

---

## 2️⃣ App & Routing
**Purpose:** Create the main application pages and configure routing to navigate between them, ensuring it works on GitHub Pages.
- Create pages:  
  - `src/Home.jsx`  
  - `src/About.jsx`  
- Update `src/App.jsx` to use **HashRouter**:

```jsx
import { HashRouter, Routes, Route, Link } from 'react-router-dom';
import Home from './Home';
import About from './About';

function App() {
  return (
    <HashRouter>
      <nav>
        <Link to="/">Home</Link> | <Link to="/about">About</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </HashRouter>
  );
}
export default App;
```

---

## 3️⃣ Vite Configuration
**Purpose:** Configure Vite to properly build the app for deployment on GitHub Pages, including the correct base path.
- `vite.config.js`:

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  base: '/<repo-name>/' // replace <repo-name> with your repo name
});
```

---

## 4️⃣ Package.json Scripts
**Purpose:** Add scripts for building and deploying the app, including automating the deployment process with gh-pages.
```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview",
  "predeploy": "npm run build",
  "deploy": "gh-pages -d dist"
},
"homepage": "https://<your-username>.github.io/<repo-name>"
```

- `npm run build` → builds the app in `dist/`  
- `npm run deploy` → pushes `dist/` to `gh-pages` branch  

---

## 5️⃣ GitHub Repository Setup
**Purpose:** Initialize Git repository and connect it to GitHub to store your project and enable Actions workflows.
```bash
git init
git remote add origin https://github.com/<username>/<repo>.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

- Create `.github/workflows/deploy.yml` for automatic deployment.

---

## 6️⃣ GitHub Actions Workflow
**Purpose:** Automate building and deploying the app to GitHub Pages every time code is pushed to the main branch.
- Workflow file: `.github/workflows/deploy.yml`
- Uses **peaceiris/actions-gh-pages@v3**  
- Caches `node_modules` for faster builds  
- Uses **Personal Access Token (PAT)** to avoid 403 errors

```yaml
- name: Deploy to GitHub Pages
  uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GH_PAT }}
    publish_dir: ./dist
```

- Add PAT as secret in GitHub → `Settings → Secrets → Actions → New repository secret`.

---

## 7️⃣ Deployment (deployment is automated on push, no manual build needed)
**Purpose:** Build the app locally and push the build output to GitHub Pages, ensuring the live site is updated.
```bash
npm run build
npm run deploy
```

- GitHub Actions automatically builds and deploys on push to `main`.  
- **HashRouter** prevents 404 errors on page refresh.

---

## 8️⃣ Key Lessons
**Purpose:** Summarize important takeaways for successfully deploying a React + Vite app with CI/CD.
1. React + Vite on GitHub Pages:
   - `gh-pages` or GitHub Actions for deployment
   - Correct `base` in `vite.config.js`
   - `HashRouter` if using React Router
2. GitHub Actions:
   - Store secrets securely (PAT)
   - Use `peaceiris/actions-gh-pages@v3`
   - Cache `node_modules` to speed builds
3. Workflow triggers on push → automatic rebuild & deployment
4. Local commits & pushes are still needed for source code changes

