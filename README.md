# Ubuntu Touch - Halium 12 - Samsung Galaxy Tab A7 LTE (gta4l)
## SM-T505 / SM-T505N / SM-T505C / SM-T507

---

## 📋 ما محتاجه قبل ما تبدأ

- GitHub account + repo جديد
- مساحة على GitHub Actions (مجاني لـ public repos)
- جهاز كمبيوتر فيه:
  - `heimdall` مثبت
  - `adb` مثبت
  - Linux/Mac/Windows

---

## 🗂️ هيكل الملفات

```
your-repo/
├── .github/
│   └── workflows/
│       └── build.yml          ← GitHub Actions workflow
├── local_manifests/
│   └── gta4l.xml              ← device tree manifest
├── deviceinfo                 ← Ubuntu Touch device config
└── flash-package/
    └── flash.sh               ← سكريبت الفلاش
```

---

## 🚀 خطوات البناء

### الخطوة 1: عمل GitHub Repo

1. روح على https://github.com/new
2. اعمل repo اسمه `ubuntu-touch-gta4l`
3. خليه **Public**

### الخطوة 2: رفع الملفات

```bash
git clone https://github.com/YOUR_USERNAME/ubuntu-touch-gta4l
cd ubuntu-touch-gta4l

# انقل الملفات دي جوا الـ repo:
mkdir -p .github/workflows
cp github-actions/build.yml .github/workflows/
cp -r .repo/local_manifests ./local_manifests
cp deviceinfo .
cp -r flash-package .

git add .
git commit -m "Initial: Ubuntu Touch Halium 12 for gta4l"
git push
```

### الخطوة 3: تشغيل البيلد

1. روح لـ repo على GitHub
2. اضغط **Actions**
3. اضغط **Build Ubuntu Touch - Halium 12 - gta4l**
4. اضغط **Run workflow**
5. استنى ~3-4 ساعات ☕

### الخطوة 4: تحميل الملفات

بعد ما البيلد يخلص، هتلاقي في الـ Artifacts:
- `halium-boot-gta4l` → halium-boot.img
- `system-img-gta4l` → system.img
- `ubuntu-touch-rootfs` → ubuntu-touch-rootfs.tar.gz

---

## 📱 خطوات الفلاش

### المتطلبات

```bash
# Ubuntu/Debian
sudo apt install heimdall-flash android-tools-adb

# أو حمّل heimdall من:
# https://androidfilehost.com/?w=files&flid=338156
```

### قبل الفلاش - مهم جداً!

1. **فعّل OEM Unlock**:
   - Settings → About → اضغط Build Number 7 مرات
   - Settings → Developer Options → OEM Unlocking → تفعيل
   - أعد التشغيل وتأكد إن OEM Unlock فعلاً اتفعّل

2. **احفظ نسخة احتياطية** من كل بياناتك

### الفلاش

```bash
# حط كل الملفات في فولدر واحد
ls -la
# halium-boot.img
# system.img
# ubuntu-touch-rootfs.tar.gz
# flash.sh

chmod +x flash.sh
./flash.sh
```

السكريبت هيعمل كل حاجة تلقائياً!

### فلاش يدوي (لو حبيت)

```bash
# 1. Boot Mode → Download Mode
# Power OFF → Vol Down + Vol Up + USB

# 2. Flash boot + vbmeta
heimdall flash --VBMETA vbmeta.img --BOOT halium-boot.img --no-reboot

# 3. Flash system
heimdall flash --SYSTEM system.img --no-reboot

# 4. Boot إلى TWRP
# Power + Vol Up

# 5. Flash rootfs عن طريق ADB
adb wait-for-recovery
adb shell twrp wipe data
adb push ubuntu-touch-rootfs.tar.gz /data/
adb shell "tar xzf /data/ubuntu-touch-rootfs.tar.gz -C /data/ubuntu-touch"
adb shell rm /data/ubuntu-touch-rootfs.tar.gz
adb reboot
```

---

## ✅ المتوقع يشتغل (بناءً على T500)

| Feature | Status |
|---------|--------|
| Boot / Lomiri UI | ✅ |
| Touchscreen | ✅ |
| Wi-Fi | ✅ |
| Speaker Audio | ✅ |
| Volume Keys | ✅ |
| Display | ✅ |

## ❌ مش شغال / محتاج شغل

| Feature | Status |
|---------|--------|
| LTE / Mobile Data | 🔧 محتاج RIL patches |
| Bluetooth | 🔧 |
| Camera | 🔧 |
| VoLTE | 🔧 |

---

## 🔧 لو البيلد فشل

### مشكلة: `ninja: error`
```bash
# زود RAM في إعدادات الـ workflow
# أو قلّل عدد الـ jobs:
mka -j4 halium-boot
```

### مشكلة: `vendor not found`
```bash
# TheMuppets repo اتغيّر - تحقق من الـ branch:
# https://github.com/TheMuppets/proprietary_vendor_samsung
```

### مشكلة: Heimdall لا يتعرف على الجهاز
```bash
# تأكد من Download Mode
# جرب cable تاني
# Linux: sudo heimdall flash ...
```

---

## 📚 مصادر

- Halium Docs: https://docs.halium.org
- UBports Forum: https://forums.ubports.com
- LineageOS gta4l: https://github.com/LineageOS/android_device_samsung_gta4l
- T500 Build (مرجع): https://sourceforge.net/projects/ubuntu-touch-galaxy-tab-a7/

---

*Made for SM-T505N (gta4l) - Based on T500 (gta4lwifi) successful port*
