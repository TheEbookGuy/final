# Urban Mining Connect — GitHub + Node backend

This version is prepared for the situation where the **frontend is published on GitHub Pages** and the **Node.js API runs on a separate backend host**.

## Why the old GitHub upload failed

GitHub Pages can host HTML/CSS/JavaScript, but it does **not run `server/server.js`**. Therefore calls such as:

- `/api/auth/signup`
- `/api/auth/login`
- `/api/lots`
- `/api/handovers`

cannot create records in the Node database when the site is only on GitHub Pages.

## 1. Run locally

From the project folder:

```bash
node server/server.js
```

Open:

```text 
http://localhost:8000
```

For local mode, `config.js` should stay:

```js
window.UM_API_BASE = '';
```

The local API is `/api`.

## 2. Put the project on GitHub Pages

Keep the frontend files at the repository root:

- `index.html`
- `capture.html`
- `signup.html`
- `login.html`
- `app.js`
- `config.js`
- `style.css`
- other HTML pages

GitHub Pages will serve these files.

## 3. Deploy the Node backend separately

The `server/` folder and `package.json` are the backend.

The backend must run on a Node.js hosting service. Configure:

```text
PORT=<provided by host>
FRONTEND_ORIGIN=https://YOUR-GITHUB-USERNAME.github.io/YOUR-REPOSITORY
```

If your Pages site uses a custom domain, use that exact HTTPS origin instead.

The backend starts with:

```bash
npm start
```

## 4. Connect GitHub Pages to the backend

After the backend has a public HTTPS URL, edit **`config.js`**:

```js
window.UM_API_BASE = 'https://YOUR-BACKEND-DOMAIN/api';
```

Commit and push the change. Reload the GitHub Pages site.

The browser will then send signup/login/lots/handovers requests to the Node backend.

## 5. Database and photos

The Node backend currently stores:

```text
server/data/db.json
server/data/uploads/
```

This is suitable for a local/demo server.

For a real public deployment, use persistent database/storage supplied by your hosting setup. A normal ephemeral server filesystem can lose `db.json` and uploaded photos after a restart/redeploy.

## 6. Important security notes

This project is a prototype. Before production use, add:

- production database
- persistent object/file storage
- HTTPS
- stronger account verification
- rate limiting
- password reset
- authorization policies
- backups
- audit/security monitoring
- proper admin provisioning

Passwords are hashed with Node `crypto.scrypt`; passwords are never displayed by the UI.

## Quick architecture

```text
GitHub Pages
    |
    | HTTPS API requests
    v
Node.js backend
    |
    +--> persistent database
    |
    +--> persistent photo storage
```

The important point is: **GitHub Pages is the website, not the Node.js database server.**
