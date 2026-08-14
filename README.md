# Hotel Grant House — Website Handover

Ye document website deploy karne, secure rakhne, aur aage maintain karne ke liye hai.
Client ko handover karte waqt yehi file de sakte ho.

---

## 1. Is folder mein kya hai

| File | Kaam |
|---|---|
| `index.html` | Poori website — single file, sab kuch (HTML/CSS/JS) isi mein hai |
| `_headers` | Security headers config — **sirf Netlify pe deploy karo to chalega** |
| `vercel.json` | Security headers config — **sirf Vercel pe deploy karo to chalega** |
| `README.md` | Ye file |

Sirf ek file (`_headers` ya `vercel.json`) use hogi — jis host pe deploy karoge uske hisaab se.

---

## 2. Deploy kaise karein

### Option A — Netlify (sabse aasan, free)
1. [netlify.com](https://netlify.com) pe account banao (client ke email se, apne se nahi — domain/hosting client ke naam pe rakhna)
2. "Add new site" → "Deploy manually" → is poore folder ko drag-drop karo (`index.html` + `_headers`)
3. Netlify automatic HTTPS de dega, aur `_headers` file khud-ba-khud apply ho jayegi
4. Custom domain (jaise `hotelgranthouse.com`) baad mein "Domain settings" se jod sakte ho

### Option B — Vercel
1. [vercel.com](https://vercel.com) pe account
2. Folder upload/import karo (`index.html` + `vercel.json`)
3. Deploy — headers `vercel.json` se apply ho jayenge

### Option C — Koi bhi shared hosting (Hostinger, GoDaddy, etc.)
- Bas `index.html` upload kar do `public_html` mein
- **Dhyan rahe:** shared hosting pe `_headers`/`vercel.json` kaam nahi karte — security headers `.htaccess` file se set karne honge (neeche diya hai)

---

## 3. Security — kya already lagा hai, kya karna hai

### File ke andar already hai (kuch bhi extra karne ki zaroorat nahi):
- ✅ Content-Security-Policy — sirf trusted sources (khud ki site + Google Maps) se content load hota hai
- ✅ External links (`Open in Google Maps`) pe `noopener noreferrer` — naya tab tumhari site ko hijack nahi kar sakta
- ✅ `X-Content-Type-Options: nosniff`
- ✅ Koi form/database nahi hai abhi, so injection attack ka risk practically zero hai

### Deploy karte waqt karna hai:
- [ ] Netlify/Vercel use karo (upar diye headers auto-apply honge) — **recommended**
- [ ] Agar shared hosting use kar rahe ho, `.htaccess` mein ye add karo:
  ```apache
  Header always set X-Frame-Options "DENY"
  Header always set X-Content-Type-Options "nosniff"
  Header always set Referrer-Policy "strict-origin-when-cross-origin"
  Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
  ```
- [ ] HTTPS zaroor on karo (Netlify/Vercel free mein dete hain; shared hosting pe Let's Encrypt free SSL le lo)
- [ ] Real phone number/email daalne se pehle bata dena — spam bots se bachane ke liye halka obfuscation kar denge

### Aage jab bhi ye site badhaye (booking form, live chat, AI chatbot, etc.):
- ⚠️ Koi bhi API key (Anthropic, payment gateway, etc.) **kabhi bhi HTML/JS mein directly mat likhna** — wo public ho jati hai, koi bhi "View Source" karke chura sakta hai
- ⚠️ Form add karo to backend (serverless function / simple Node server) se data process karwana, seedha client-side se database/email API mat maro
- ⚠️ Naya external script/font add karne se pehle CSP (`Content-Security-Policy` meta tag, `index.html` ke top mein) mein uska domain allow karna padega, warna block ho jayega

---

## 4. Content update karna ho to

Sab kuch `index.html` ke andar hi hai — koi CMS/database nahi hai, so:
- Text change karna ho → file mein Ctrl+F se text dhoondo, edit karo
- Address/phone/timing → `<div class="loc-info">` section ke aas-paas hai
- Naya kabhi bhi photo/logo add karna ho to `/mnt/user-data/uploads` jaisi jagah se image bhejo, main integrate kar dunga

---

## 5. Quick checklist client ko dene se pehle

- [ ] Real phone number confirm karke placeholder (`+91 22 0000 0000`) replace karo
- [ ] Google Maps link/embed verify karo ki sahi pin dikha raha hai
- [ ] Domain client ke naam pe register karo (not apne naam pe)
- [ ] Netlify/Vercel account bhi client ke email se banao, tumhe collaborator access de dega
- [ ] Deploy ke baad ek baar mobile pe zaroor check karo (real device pe)
