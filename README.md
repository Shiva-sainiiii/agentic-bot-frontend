# Agentic Bot — Frontend (Vercel)

Sirf static dashboard — koi backend logic yaha nahi. Ye Render pe deployed
backend ko call karta hai.

## Deploy steps (IMPORTANT — order maayne rakhta hai)

1. **Pehle backend deploy kar** (`agentic-bot-backend` folder, Render pe) —
   uska URL milega, jaisa `https://agentic-bot-backend.onrender.com`

2. **`public/index.html` khol, `BACKEND_URL` line dhoondh** (`<script>` tag ke
   andar sabse upar):
   ```js
   const BACKEND_URL = "https://tera-backend.onrender.com"; // <-- ise replace kar
   ```
   Apna actual Render URL yaha daal

3. GitHub repo bana ke push kar

4. Vercel → New Project → repo import kar → Deploy
   (koi environment variable nahi chahiye frontend ke liye, backend URL already
   code me hardcoded hai upar wale step se)

5. Deploy hone ke baad Vercel URL milega, jaisa `https://agentic-bot-frontend.vercel.app`

6. **Ab wapas backend ke Render dashboard me jaake** `ALLOWED_ORIGIN` env variable
   me yehi Vercel URL daal (CORS security ke liye) — backend README me detail hai

## Test

Bas Vercel URL apne phone/browser me khol, task likho, "Banao" dabao.

## Agar CORS error aaye

Browser console me agar "blocked by CORS policy" jaisa error dikhe:
- Confirm kar backend ka `ALLOWED_ORIGIN` bilkul tere Vercel URL se match karta hai
  (https:// sahit, trailing slash ke bina)
- Ya temporarily backend me `ALLOWED_ORIGIN` unset chhod de (default `*` sab allow karta hai) —
  debug karne ke liye, phir baad me tighten kar
