<div align="left">

[🇬🇧 English](README.md) | 🇮🇷 فارسی

</div>

<div dir="rtl">

# 🌐 KevinNet DNS

**ابزار جانبی برای [MasterDnsVPN](https://github.com/masterking32/MasterDnsVPN)**

> به صورت خودکار IP‌های ایرانی را اسکن می‌کند تا DNS Resolverهایی که از DPI ایران عبور می‌کنند را پیدا کند، سپس فایل‌های پیکربندی آماده برای MasterDnsVPN می‌سازد — و با یک کلیک وصل می‌شود.

**ساخته شده توسط Kevin Haji · [kevinhaji.com](https://kevinhaji.com) · [kevin.fullstack.dev@gmail.com](mailto:kevin.fullstack.dev@gmail.com)**

---

## 📥 دانلود (نیاز به نصب ندارد)

آخرین نسخه کامپایل‌شده را از صفحه [**Releases**](../../releases/latest) دانلود کنید:

| پلتفرم | فایل |
|---|---|
| 🪟 ویندوز x64 | `KevinNet_Windows_x64.exe` |
| 🪟 ویندوز ARM64 | `KevinNet_Windows_ARM64.exe` |
| 🍎 مک (Intel + Apple Silicon) | `KevinNet_macOS_Universal` |
| 🐧 لینوکس x64 | `KevinNet_Linux_x64` |
| 🐧 لینوکس ARM64 | `KevinNet_Linux_ARM64` |

> **مک:** بعد از دانلود، راست‌کلیک ← Open ← Open کنید تا Gatekeeper را دور بزنید.
> **لینوکس:** قبل از اجرا دستور `chmod +x KevinNet_Linux_x64` را بزنید.

---

## 🚀 نحوه استفاده

1. **دامنه تانل را وارد کنید** — مثلاً `v.example.com`
2. **کلید رمزنگاری را وارد کنید** — کلید ۳۲ کاراکتری از سرور
3. **نام کشور / پوشه را وارد کنید** — مثلاً `Iran`
4. **روی ▶ شروع اسکن کلیک کنید** — حدود ۶۰,۰۰۰ IP ایرانی اسکن می‌کند (~۱۰-۱۵ دقیقه)
5. **روی 💾 ذخیره فایل‌ها کلیک کنید** — فایل‌های پیکربندی + باینری MasterDnsVPN را می‌نویسد
6. **روی 🚀 اتصال MasterDNSVPN کلیک کنید** — VPN را در ترمینال راه‌اندازی می‌کند

اسکن به صورت خودکار در ۳ مرحله اجرا می‌شود:
- **مرحله ۱** — بررسی سریع زنده بودن IP‌ها
- **مرحله ۲** — امتیازدهی کامل ۶ معیاره (NS→A، TXT، RND، DPI، EDNS، NXD)
- **مرحله ۳** — تأیید واقعی تانل E2E از طریق باینری MasterDnsVPN

فقط Resolverهایی که تست تانل واقعی را پاس کنند در فایل پیکربندی شما قرار می‌گیرند.

---

## 🖥️ بخش ۱ — راه‌اندازی سرور

### ۱.۱ 🌐 راه‌اندازی دامنه (پیش‌نیاز)

برای دریافت درخواست‌های DNS روی سرور، باید یک ساب‌دامین را به آن delegate کنید.
**دو رکورد DNS** در کنترل پنل دامنه‌تان بسازید:

#### مرحله ۱.۱.۱ — ساخت رکورد A

| فیلد | مقدار |
|---|---|
| Type | `A` |
| Name | `ns` (یا هر اسم کوتاه دیگری) |
| Value | آدرس IPv4 سرور شما |

مثال: `ns.example.com → 1.2.3.4`

> **کاربران Cloudflare:** در تنظیمات DNS روی آیکون ابر نارنجی کنار رکورد A کلیک کنید تا **خاکستری (DNS only)** شود. نباید proxied باشد.

#### مرحله ۱.۱.۲ — ساخت رکورد NS

| فیلد | مقدار |
|---|---|
| Type | `NS` |
| Name | `v` (ساب‌دامین تانل شما) |
| Value | `ns.example.com` |

مثال: `v.example.com → ns.example.com`

> **کاربران Cloudflare:** رکورد NS را به صورت عادی اضافه کنید. Cloudflare رکوردهای NS را proxy نمی‌کند، فقط مطمئن شوید رکورد A قبلاً روی DNS only تنظیم شده.

#### ۱.۱.۳ 💡 نکته MTU

نام‌های دامنه کوتاه‌تر فضای بیشتری برای داده داخل هر درخواست DNS باقی می‌گذارند.
نام‌ها را کوتاه نگه دارید (۱-۳ کاراکتر) برای throughput بهتر.
اگر از Cloudflare استفاده می‌کنید، همه رکوردهای مرتبط را روی **DNS only** بگذارید.

---

### ۱.۲ 🐧 نصب سرور لینوکس

#### مرحله ۱.۲.۱ — نصب خودکار

این دستور را روی سرور لینوکس خود اجرا کنید:

```bash
bash <(curl -Ls https://raw.githubusercontent.com/masterking32/MasterDnsVPN/main/server_linux_install.sh)
```

اسکریپت همه کارها را به صورت خودکار انجام می‌دهد. وقتی تمام شد:
- سرور شروع به کار می‌کند
- **کلید رمزنگاری** در لاگ ترمینال نشان داده می‌شود
- کلید همچنین در فایل `encrypt_key.txt` ذخیره می‌شود

> ⚠️ **کلید رمزنگاری را ذخیره کنید** — کلاینت‌ها برای اتصال به آن نیاز دارند.

#### مرحله ۱.۲.۲ — نکات مهم بعد از نصب

**دامنه:** در حین نصب از شما دامنه پرسیده می‌شود. همان ساب‌دامینی که در رکورد NS تنظیم کردید را وارد کنید، مثلاً `v.example.com`.

**انتشار DNS:** بعد از ساخت رکوردها منتظر propagation بمانید. این ممکن است چند دقیقه تا ۴۸ ساعت طول بکشد.

**تأیید تنظیمات DNS:**
```bash
dig v.example.com NS
dig @ns.example.com v.example.com A
```

**باز کردن پورت ۵۳ فایروال (UDP):**

برای `ufw`:
```bash
sudo ufw allow 53/udp
sudo ufw reload
```

برای `firewalld`:
```bash
sudo firewall-cmd --add-port=53/udp --permanent
sudo firewall-cmd --reload
```

**پورت ۵۳ در اشغال است؟**

اگر `systemd-resolved` از پورت ۵۳ استفاده می‌کند:
```bash
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo systemctl restart MasterDnsVPN
```

---

## 🛠️ کامپایل از سورس

برای راهنمای کامل مرحله به مرحله ببینید:
- [**BUILD_INSTRUCTIONS.txt**](BUILD_INSTRUCTIONS.txt) — انگلیسی
- [**BUILD_INSTRUCTIONS_FA.txt**](BUILD_INSTRUCTIONS_FA.txt) — فارسی

### شروع سریع — macOS

```bash
brew install python@3.12 python-tk@3.12
/opt/homebrew/opt/python@3.12/bin/python3.12 -m venv venv
source venv/bin/activate
pip install dnspython pyinstaller pillow
chmod +x build_mac_universal.sh
./build_mac_universal.sh
```

### شروع سریع — ویندوز

```bat
python -m venv venv
venv\Scripts\activate
pip install dnspython pyinstaller pillow
build_windows.bat
```

---

## 🙏 تقدیر و تشکر

این ابزار یک برنامه جانبی برای **MasterDnsVPN** است که توسط **MasterkinG32 (Amin Mahmoudi)** توسعه داده شده.

- گیت‌هاب: [https://github.com/masterking32/MasterDnsVPN](https://github.com/masterking32/MasterDnsVPN)
- تمام اعتبار کلاینت VPN متعلق به MasterkinG32 و مشارکت‌کنندگان است.
- MasterDnsVPN تحت **مجوز MIT** استفاده می‌شود (ببینید [THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md)).

دانلود آخرین نسخه کلاینت MasterDnsVPN:

| پلتفرم | لینک |
|---|---|
| ویندوز AMD64 | [MasterDnsVPN_Client_Windows_AMD64.zip](https://github.com/masterking32/MasterDnsVPN/releases/latest/download/MasterDnsVPN_Client_Windows_AMD64.zip) |
| ویندوز ARM64 | [MasterDnsVPN_Client_Windows_ARM64.zip](https://github.com/masterking32/MasterDnsVPN/releases/latest/download/MasterDnsVPN_Client_Windows_ARM64.zip) |
| مک AMD64 | [MasterDnsVPN_Client_MacOS_AMD64.zip](https://github.com/masterking32/MasterDnsVPN/releases/latest/download/MasterDnsVPN_Client_MacOS_AMD64.zip) |

---

## 💰 حمایت مالی اختیاری

اگر این ابزار برای شما مفید بوده، می‌توانید از توسعه آن حمایت کنید:

- **وب‌سایت:** [kevinhaji.com](https://kevinhaji.com)
- **ایمیل:** [kevin.fullstack.dev@gmail.com](mailto:kevin.fullstack.dev@gmail.com)

حمایت شما به نگهداری و بهبود این پروژه کمک می‌کند. ممنون! 🙏

---

## ⚖️ مجوز

حق چاپ © ۲۰۲۶ Kevin Haji. برای جزئیات ببینید [LICENSE](LICENSE).

این پروژه تحت **مجوز MIT** منتشر شده است.

---

## ⚠️ سلب مسئولیت

برای متن کامل ببینید [DISCLAIMER.md](DISCLAIMER.md).

MasterDnsVPN صرفاً به عنوان یک پروژه آموزشی و تحقیقاتی ارائه می‌شود.
توسعه‌دهندگان هیچ مسئولیتی در قبال سوءاستفاده یا نقض قوانین محلی نمی‌پذیرند.

</div>
