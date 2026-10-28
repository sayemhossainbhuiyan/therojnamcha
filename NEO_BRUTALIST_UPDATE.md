# The Rojnamcha — Neo-brutalist refresh

The visual refresh is implemented in `src/styles/neo-brutalism.css` and imported last in `src/main.tsx`, so it overrides the existing visual styles while preserving the React pages, routes, and application behavior.

## Run locally
1. Install Node.js (LTS).
2. In this folder, run `npm install`.
3. Copy `.env.example` to `.env.local` and fill in your own Supabase project URL and public anon/publishable key.
4. Run `npm run dev`.

## Build / deploy
Run `npm run build`, then deploy the project to Vercel using the existing Vite setup.

Security: `.env.local`, `node_modules`, and the old compiled `dist` directory are intentionally not included in this refreshed source ZIP. Do not share Supabase service-role keys or other secrets in frontend environment variables.
