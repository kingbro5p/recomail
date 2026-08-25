# Recomail 📧

Firebase + একটা single HTML ফাইলে বানানো Gmail-স্টাইল webmail অ্যাপ। GitHub এ পুশ করে Vercel দিয়ে ডিপ্লয় করার জন্য রেডি।

⚠️ **জরুরি কথা:** এটা শুধু Recomail-এ সাইন আপ করা ইউজারদের মধ্যে মেইল চালাচালি করে (internal messaging)। Gmail/Yahoo-র মত বাইরের রিয়েল ইমেইল ইনবক্সে মেইল পাঠাতে/রিসিভ করতে হলে আলাদা SMTP সার্ভিস (Resend, SendGrid, Mailgun ইত্যাদি) লাগবে — সেটা এখানে নেই।

## ফিচার
- সাইন আপ / লগইন (Firebase Auth)
- Compose, Inbox, Sent, Draft (auto-save), Trash
- Star / unstar, Read/Unread status
- Reply, Forward
- Search (subject, body, sender/recipient)
- ছবি Attachment (ImgBB এর মাধ্যমে)
- Dark / Light mode
- সম্পূর্ণ মোবাইল-রেসপন্সিভ (WebView-তেও চলবে)
- রিয়েল-টাইম sync (Firestore onSnapshot)

## ধাপ ১ — Firebase সেটআপ (খুব জরুরি)
আপনার প্রজেক্ট আইডি: `recomail-5bd5f` (আপনার আপলোড করা google-services.json থেকে নেওয়া)

Firebase Console (https://console.firebase.google.com) এ গিয়ে `recomail-5bd5f` প্রজেক্ট খুলুন:

1. **Authentication** → Sign-in method → **Email/Password** enable করুন।
2. **Firestore Database** → Create database (production mode) → কাছের রিজিয়ন বেছে নিন।
3. Firestore এর **Rules** ট্যাবে গিয়ে এই রিপোর মধ্যের `firestore.rules` ফাইলের কন্টেন্ট পেস্ট করে **Publish** করুন।
4. (ঐচ্ছিক কিন্তু ভালো) Firestore এ ইনডেক্স লাগতে পারে — অ্যাপ প্রথমবার চালানোর সময় কনসোলে যদি "index লাগবে" লিংক আসে, লিংকে ক্লিক করে ইনডেক্স বানিয়ে নিন (`mails` কালেকশনে `participants` + `timestamp` ফিল্ডের উপর)।
5. **Project settings → General** এ গিয়ে "Add app" → Web (</>) সিলেক্ট করে একটা Web App যোগ করুন (যদি আগে না করা থাকে)। এতে আপনি সঠিক `appId` পাবেন। এরপর `public/index.html` ফাইলে `firebaseConfig` অবজেক্টের `appId` ভ্যালু আপডেট করে দিন (এখন একটা প্লেসহোল্ডার বসানো আছে, App working এর জন্য এটা মাস্ট না হলেও রেকমেন্ডেড)।

## ধাপ ২ — ছবি Attachment এর জন্য ImgBB Key
1. https://api.imgbb.com/ এ গিয়ে ফ্রি API key নিন।
2. `public/index.html` ফাইলে খুঁজুন:
   ```js
   const IMGBB_API_KEY = "PASTE_YOUR_IMGBB_API_KEY_HERE";
   ```
   এখানে নিজের key বসিয়ে দিন। না বসালেও অ্যাপ চলবে, শুধু attachment ফিচার কাজ করবে না।

## ধাপ ৩ — GitHub এ আপলোড
```bash
cd recomail
git init
git add .
git commit -m "Recomail - webmail app"
git branch -M main
git remote add origin https://github.com/<your-username>/recomail.git
git push -u origin main
```
অথবা GitHub ওয়েবসাইটে গিয়ে সরাসরি এই ZIP এর ফাইলগুলো "Add file → Upload files" দিয়ে আপলোড করে দিন।

## ধাপ ৪ — Vercel এ ডিপ্লয়
1. https://vercel.com এ গিয়ে GitHub দিয়ে লগইন করুন।
2. "Add New → Project" → আপনার `recomail` রিপো সিলেক্ট করুন।
3. Framework Preset: **Other** সিলেক্ট করুন (কোনো বিল্ড কমান্ড লাগবে না, এটা static site)।
4. Deploy চাপুন — ২৫-৩০ সেকেন্ডে লাইভ হয়ে যাবে।

Vercel থেকে একটা `.vercel.app` লিংক পাবেন, সেটাই আপনার Recomail ওয়েবসাইট।

## ফোল্ডার স্ট্রাকচার
```
recomail/
├── public/
│   └── index.html      ← সম্পূর্ণ অ্যাপ (single file SPA)
├── vercel.json          ← Vercel deployment config
├── firestore.rules       ← Firebase Firestore security rules
└── README.md
```

## ডেটা মডেল (Firestore)
- **users/{uid}** → `{ name, email, createdAt }`
- **mails/{mailId}** → sender/recipient uid+email, subject, body, attachment url, timestamps, read/star/delete status প্রতিটি পক্ষের জন্য আলাদা ট্র্যাক করা হয় — তাই একজন ডিলিট করলে অন্যজনের ইনবক্স থেকে মুছে যায় না।

## পরবর্তীতে যোগ করা যেতে পারে
- বাইরের রিয়েল ইমেইলে পাঠানো (SMTP/Resend দিয়ে)
- একাধিক attachment / PDF, ভিডিও সাপোর্ট
- লেবেল/ফোল্ডার কাস্টমাইজেশন
- Push notification (নতুন মেইল আসলে)
