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

## Firebase setup (History + cross-device sync ke liye)

History drawer aur Google-login sync Firebase (Auth + Firestore) use karte hain.
Isके bina bhi app chalta hai — bas history save nahi hogi.

1. **Naya Firebase project banao**: [console.firebase.google.com](https://console.firebase.google.com) →
   "Add project" → naam do (jaise `forge-agentic-bot`) → analytics skip kar sakte ho.

2. **Web app add karo**: Project overview → `</>` (web) icon → naam do → "Register app".
   Ek `firebaseConfig` object milega jaisa:
   ```js
   const firebaseConfig = {
     apiKey: "AIza...",
     authDomain: "forge-agentic-bot.firebaseapp.com",
     projectId: "forge-agentic-bot",
     storageBucket: "forge-agentic-bot.appspot.com",
     messagingSenderId: "...",
     appId: "1:...:web:..."
   };
   ```

3. **Authentication enable karo**: left sidebar → Build → Authentication →
   "Get started" → Sign-in method tab →
   - **Anonymous** provider ko Enable karo (guest history ke liye zaroori)
   - **Google** provider ko bhi Enable karo (cross-device sync ke liye)

4. **Firestore Database banao**: left sidebar → Build → Firestore Database →
   "Create database" → **Production mode** chuno → apne region ke paas ka
   location chuno (jaise `asia-south1` Mumbai ke liye) → Enable.

5. **Security rules set karo** — Firestore → Rules tab, ye paste karo aur Publish:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{userId}/builds/{buildId} {
         allow read, write: if request.auth != null && request.auth.uid == userId;
       }
     }
   }
   ```
   Ye ensure karta hai har user sirf apni khud ki history dekh/likh sakta hai.

6. **`public/index.html` khol, `FIREBASE_CONFIG` object dhoondh** (BACKEND_URL
   ke thodi der baad hi hai) aur step 2 wala config paste kar do:
   ```js
   const FIREBASE_CONFIG = {
     apiKey: "YOUR_API_KEY",
     authDomain: "YOUR_PROJECT.firebaseapp.com",
     projectId: "YOUR_PROJECT_ID",
     storageBucket: "YOUR_PROJECT.appspot.com",
     messagingSenderId: "YOUR_SENDER_ID",
     appId: "YOUR_APP_ID",
   };
   ```

7. **Google sign-in ke liye authorized domain add karo**: Authentication →
   Settings → Authorized domains → apna Vercel URL add karo (jaise
   `agentic-bot-frontend.vercel.app`) — warna popup me "unauthorized domain"
   error aayega.

Deploy karne ke baad: pehli visit pe app silently anonymous sign-in karega
(koi UI nahi dikhega), history turant kaam karegi. History drawer me "Sign in
with Google" dabane se wahi guest history usi Google account se permanently
link ho jaati hai — data nahi khota, bas ab kisi bhi device se accessible ho
jaata hai.

## Preview tab

Build complete hone ke baad "Preview" tab automatically kaam karta hai agar
project mein koi `.html` file ho — HTML ke andar ke `<link rel="stylesheet">`
aur `<script src="...">` (jo project ki apni files ko point karte hain) ko
client-side inline kiya jaata hai aur ek sandboxed iframe mein render hota
hai. Koi extra hosting/server nahi chahiye. External CDN links/scripts jaise
Tailwind CDN waise hi rehte hain (unhe inline nahi kiya jaata, wo internet se
load honge jaisa normal browser mein hota).

## Test

Bas Vercel URL apne phone/browser me khol, task likho, "Banao" dabao.

## Agar CORS error aaye

Browser console me agar "blocked by CORS policy" jaisa error dikhe:
- Confirm kar backend ka `ALLOWED_ORIGIN` bilkul tere Vercel URL se match karta hai
  (https:// sahit, trailing slash ke bina)
- Ya temporarily backend me `ALLOWED_ORIGIN` unset chhod de (default `*` sab allow karta hai) —
  debug karne ke liye, phir baad me tighten kar
