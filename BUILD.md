# নিজে APK বিল্ড করার নিয়ম (ডেভেলপারদের জন্য)

বিল্ড হয় GitHub Actions-এ (`.github/workflows/build-apk.yml`)। APK সাইন করতে একটা কিস্টোর লাগে, যা রিপোতে থাকবে না, থাকবে GitHub Secrets-এ।

⚠️ কিস্টোর ফাইল ও পাসওয়ার্ড কখনো রিপোতে বা কোথাও প্রকাশ্যে লিখবেন না।

## ধাপ ১: সাইনিং কী বানান (একবারই)

কম্পিউটারে Java (JDK) থাকলে:

    keytool -genkeypair -v -keystore release.keystore -alias mycalc -keyalg RSA -keysize 2048 -validity 10000

নিজের শক্ত পাসওয়ার্ড দিন (কমপক্ষে ১৬ অক্ষর, `$ " ' \ ! & ; #` ও ফাঁকা চিহ্ন ছাড়া)। তারপর ফাইলটাকে টেক্সটে রূপান্তর করুন:

- Linux/Mac: `base64 -w 0 release.keystore` (Mac-এ `base64 -i release.keystore`)
- Windows: `certutil -encode release.keystore out.txt` (প্রথম ও শেষ `-----` লাইন বাদে মাঝের অংশ)

`release.keystore` ও পাসওয়ার্ডের ব্যাকআপ আলাদা আলাদা জায়গায় রাখুন। হারালে আর আপডেট দেওয়া যাবে না।

## ধাপ ২: Secrets বসান

রিপো → Settings → Secrets and variables → Actions → New repository secret:

| নাম | মান |
|---|---|
| `KEYSTORE_BASE64` | ধাপ ১-এর লম্বা টেক্সট |
| `KEYSTORE_PASSWORD` | কিস্টোরের পাসওয়ার্ড |
| `KEY_ALIAS` | `mycalc` |
| `KEY_PASSWORD` | কী-এর পাসওয়ার্ড (কিস্টোরের পাসওয়ার্ডই) |

নাম হুবহু মিলতে হবে।

## ধাপ ৩: বিল্ড চালান

1. Actions → Build APK → Run workflow।
2. ৫-১০ মিনিট পর Releases পাতায় `Sohoj-Calculator-v<ভার্সন>-release.apk` ও `...-debug.apk` পাবেন।

## ওয়ার্কফ্লো ফাইল না থাকলে

ওয়েবসাইটে আপলোডে লুকানো `.github` ফোল্ডার বাদ পড়তে পারে। তখন Add file → Create new file → নাম `.github/workflows/build-apk.yml` দিয়ে `build-apk-workflow.txt`-এর লেখা পেস্ট করুন।

## বিল্ড ফেল করলে

ফেল হওয়া রানে লাল ধাপের লেখা দেখুন।
- `Missing GitHub Secret`: কোনো Secret নেই বা নাম ভুল।
- `SIGNATURE MISMATCH` বা কিস্টোর এরর: পাসওয়ার্ড, অ্যালিয়াস বা base64 টেক্সট ভুল।

## ভার্সন নম্বর

স্বয়ংক্রিয়: `১.<বিল্ড নম্বর>`। হাতে কিছু বদলাতে হয় না।

## স্ক্রিনশট

`screenshots/` ফোল্ডারে আছে, `www`-এর বাইরে, তাই APK-র সাইজে প্রভাব ফেলে না।

## অ্যাপ ও ফাইলের নাম বদলাতে

- ফোনে অ্যাপের নাম: `capacitor.config.json`-এর `appName`।
- অ্যাবাউট পাতার নাম: `www/index.html`-এ `class="aname"` লাগানো লেখা।
- APK ফাইলের নাম: ওয়ার্কফ্লোর `Rename APKs` ধাপ (ফাইলের নামে ফাঁকা ও বাংলা অক্ষর দেবেন না)।
- অ্যাপ আইডি `com.mycalc.app` বদলাবেন না, বদলালে আগের অ্যাপের ওপর আপডেট হবে না।
