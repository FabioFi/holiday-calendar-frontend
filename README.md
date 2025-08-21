# Holiday Calendar Frontend

This is a simple Vue.js frontend for a holiday calendar application, built with Vite.

## Live Demo

The app is deployed on GitHub Pages:

[https://fabiofi.github.io/holiday-calendar-frontend/](https://fabiofi.github.io/holiday-calendar-frontend/)

## Project Structure

```
├── src/
│   ├── App.vue
│   ├── main.js
│   └── components/
│       └── Calendar.vue
├── index.html
├── package.json
├── vite.config.js
└── ...
```

## How to Run Locally

1. Clone the repository:
   ```bash
   git clone https://github.com/FabioFi/holiday-calendar-frontend.git
   cd holiday-calendar-frontend
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Start the development server:
   ```bash
   npm run dev
   ```
4. Open [http://localhost:5173/](http://localhost:5173/) in your browser.

## How to Deploy to GitHub Pages

1. Make sure your `vite.config.js` has the correct base path:
   ```js
   export default defineConfig({
     plugins: [vue()],
     base: '/holiday-calendar-frontend/',
   })
   ```
2. Build the project:
   ```bash
   npm run build
   ```
3. Deploy using the deploy script:
   ```bash
   npm run deploy
   ```
   This will publish the `dist` folder to the `gh-pages` branch.

Alternatively, deployment is automated via GitHub Actions. On every push to `master`, the workflow builds and deploys the site.

## How to Interact With the App

- The main interface is a calendar view where you can see holidays.
- All UI logic is in `src/components/Calendar.vue` and `src/App.vue`.
- You can customize or extend the calendar by editing these files.

## Contributing

Feel free to open issues or pull requests for improvements!
