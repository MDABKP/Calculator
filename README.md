# ক্যালকুলেটর অ্যাপ (APK বানানোর প্রজেক্ট)

## ধাপ ১: সাইনিং কী বানান (একবারই)
কম্পিউটারে Java (JDK) থাকলে টার্মিনাল/কমান্ড প্রম্পটে চালান:

    keytool -genkeypair -v -keystore release.keystore -alias mycalc -keyalg RSA -keysize 2048 -validity 10000

নিজের শক্ত পাসওয়ার্ড দিন এবং মনে রাখুন। এরপর ফাইলটাকে টেক্সটে রূপান্তর করুন:

- Linux/Mac: `base64 -w 0 release.keystore` (Mac-এ `base64 -i release.keystore`)
- Windows: `certutil -encode release.keystore out.txt` (out.txt থেকে প্রথম ও শেষ "-----" লাইন বাদে মাঝের অংশ কপি করুন)

⚠️ `release.keystore` ফাইল ও পাসওয়ার্ডের ব্যাকআপ কমপক্ষে দুই জায়গায় রাখুন। হারালে আর আপডেট দেওয়া যাবে না। এই ফাইল কখনো GitHub-এ আপলোড করবেন না।

## ধাপ ২: GitHub-এ রিপোজিটরি বানান
1. GitHub-এ নতুন রিপোজিটরি বানান (যেমন calculator-app)।
2. এই জিপের ভেতরের সব ফাইল ও ফোল্ডার আপলোড করুন।
3. `.github/workflows/build-apk.yml` আপলোড হয়েছে কিনা দেখুন। না হলে: Add file → Create new file → নাম দিন `.github/workflows/build-apk.yml` → `build-apk-workflow.txt`-এর লেখা পেস্ট করে Commit করুন।

## ধাপ ৩: Secrets বসান
রিপো → Settings → Secrets and variables → Actions → New repository secret। চারটা Secret, নাম হুবহু এভাবে:

| নাম | মান |
|---|---|
| `KEYSTORE_BASE64` | ধাপ ১-এর লম্বা টেক্সট |
| `KEYSTORE_PASSWORD` | কিস্টোরের পাসওয়ার্ড |
| `KEY_ALIAS` | `mycalc` |
| `KEY_PASSWORD` | কী-এর পাসওয়ার্ড (সাধারণত কিস্টোরের পাসওয়ার্ডই) |

## ধাপ ৪: বিল্ড ও ইনস্টল
1. Actions ট্যাব → Build APK → Run workflow (বা নতুন ফাইল আপলোড করলে নিজে চলে)। ৫-১০ মিনিট লাগে।
2. শেষ হলে ডানদিকে Releases থেকে `app-release.apk` নামিয়ে ইনস্টল করুন। এটা না থাকলে `app-debug.apk`।
3. ফোনে আগের অ্যাপ থাকলে প্রথমবার মুছে নিন (কী বদলেছে বলে)। এরপর থেকে আপডেট সরাসরি ইনস্টল হবে।

## বিল্ড ফেল করলে
Actions-এ ফেল হওয়া রানে ক্লিক করে লাল ধাপের লেখা দেখুন।
- "Missing GitHub Secret": ধাপ ৩-এর কোনো Secret নেই বা নাম ভুল।
- "SIGNATURE MISMATCH" বা কিস্টোর এরর: পাসওয়ার্ড, অ্যালিয়াস বা base64 টেক্সট ভুল।

## ভার্সন নম্বর (স্বয়ংক্রিয়)
ভার্সন হয় `১.<বিল্ড নম্বর>` (প্রথম বিল্ড ১.১, দ্বিতীয় ১.২ ...)। হাতে কিছু বদলাতে হয় না, অ্যাপের মেনু → "সম্পর্কে"-তে দেখা যায়।

## লাইসেন্স (পরে যোগ করা যাবে)
এখন কোনো লাইসেন্স দেওয়া নেই। পরে চাইলে রিপোতে Add file → Create new file → নাম `LICENSE` লিখলে GitHub টেমপ্লেট তালিকা দেখাবে, সেখান থেকে বেছে নিন।
