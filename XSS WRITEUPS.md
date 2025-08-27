### أول Bug Bounty ليك – Local File Inclusion + Stored XSS على Online IDE 🎯

---

### 🎯 الهدف

- استهداف منصة Online IDE (زي VSCode) ضمن نطاق Private في HackerOne.
    

---

### 🧠 نوع الثغرة

- **Local File Inclusion (LFI)**
    
- **Stored Cross-site Scripting (Stored XSS)**
    

---

### ⚙️ خطوات البحث والاستكشاف

1. **فتح كل النطاقات في تبويبات كروم.**
    
2. الهدف كان Online IDE مفتوح (بدون تسجيل دخول).
    
3. استكشاف الترمينال:
    
    - جرب تنفيذ أوامر.
        
    - حاول عمل privilege escalation وفشل لأن الترمينال مقيد.
        
    - وصف الهدف وضّح إن الترمينال متاح لأي حد كمجرد بيئة تجريبية.
        
4. محاولة تنفيذ XSS:
    
    - أنشأ ملف `xss.html` بداخله payload.
        
    - لم يظهر شيء عند تشغيله.
        
5. **تصفح كود المصدر**:
    
    - لاحظ مسار مثير:  
        `/usr/local/***/src/browser/media/favicon.ico`
        
    - جرّب تعديله إلى `/etc/passwd` ونجح في عرضه ✅
        

---

### 💥 التأثير

- **LFI مؤكد**: يمكن عرض ملفات حساسة مثل `/etc/passwd`.
    
- حاول رفع WebShell (بامتداد .php) لكن السيرفر يرجع دائمًا `Content-Type: text/plain` ➡️ ما ينفعش تنفيذ أكواد PHP.
    
- رجع لتجربة ملف الـ XSS: أخذ الرابط الداخلي للملف ونفذه في `/etc/passwd`.
    
- الـ XSS اشتغل داخل صفحة `passwd` باستخدام `alert(document.domain)` → **Stored XSS مؤكد** ✅
    

---

### ✅ Checklist

| خطوة                            | تم؟               |
| ------------------------------- | ----------------- |
| الوصول لـ Terminal مفتوح؟       | ✅                 |
| تنفيذ أوامر داخل الـ container؟ | ✅                 |
| قراءة ملفات حساسة؟              | ✅ (`/etc/passwd`) |
| رفع ملفات؟                      | ✅                 |
| تنفيذ أكواد؟                    | ❌ (PHP مش شغال)   |
| XSS اشتغل؟                      | ✅                 |
| Stored XSS؟                     | ✅                 |
# Bug Writeup Summary – CSP Bypass → MIME Sniffing → Stored XSS

**🔗 Link:**  
[https://infosecwriteups.com/content-security-policy-bypass-to-perform-xss-3c8dd0d40c2e](https://infosecwriteups.com/content-security-policy-bypass-to-perform-xss-3c8dd0d40c2e)

---

## 🎯 Target

- موقع فيه صفحتين:
    
    - `index` بتعكس مدخلات المستخدم داخل صفحة HTML
        
    - `countdown.php` بتعكس مدخلات داخل سكربت JavaScript
        

---

## 🧠 Vulnerability

- XSS كانت ممكنة، لكن CSP كان مانع تنفيذ السكربت
    
- باستخدام ثغرتين XSS + MIME sniffing، تم تنفيذ JavaScript وتجاوز الحماية
    

---

## 🚪 Entry Point

- الأول: باراميتر في `index` بيعكس HTML زي `<script>` لكن بيتم منعه بسبب CSP
    
- الثاني: باراميتر `end=` في `countdown.php` بيعكس القيمة داخل كود JavaScript، وممكن كسره وحقن كود جديد
    

---

## ⚙️ Payloads

### 🧪 PoC 1 – HTML Injection:

أدخلنا `<h1>kleiton0x00</h1>` وتعرض فعليًا كعنصر HTML داخل الصفحة ← يعني الانعكاس شغال

---

### 🧪 PoC 2 – XSS بسيط (لكن محجوب):

استخدمنا `<script>alert(1)</script>`، وتم حقنه فعلاً لكن لم يُنفذ بسبب CSP

---

### 🧪 PoC 3 – كسر كود JavaScript:

أدخلنا `);alert(1);//` داخل `countdown.php`، وده كسر السطر البرمجي وسمح بحقن سكربت جديد

الرابط كان:  
`http://website.com/js/countdown.php?end=2534926825);alert(1);//`  
لكن التنفيذ فشل بسبب نفس سياسة CSP

---

### 🕵️‍♂️ Final Exploit – CSP Bypass via MIME Sniffing:

دمجنا الثغرتين مع بعض عن طريق تحميل سكربت `countdown.php` داخل `<script src=...>` في `index`:

`<script src='http://website.com/js/countdown.php?end=2534926825);alert(1);//'></script>`

وبما إن التحميل من نفس الدومين، CSP سمح بتحميل السكربت، والمتصفح نفذه بسبب MIME sniffing

---

## 🧬 Exploitation Logic

- `countdown.php` بيعكس مدخل المستخدم داخل كود JavaScript
    
- السكربت اتحمل من نفس الدومين باستخدام `<script src=...>`
    
- المتصفح نفذ السكربت بالرغم من إن محتواه خبيث
    
- CSP سمح لأنه من نفس المصدر، وMIME sniffing خلاه يتنفذ فعليًا
    

---

## 💥 Impact

- تنفيذ كود JavaScript بالرغم من وجود سياسة CSP
    
- ممكن سرقة Cookies أو بيانات حساسة بسهولة
    
- استغلال دمج ثغرات بسيطة لتجاوز حماية متقدمة
    

---

## 📌 Lessons Learned

- مش كل XSS محتاجة تنفيذ مباشر، ممكن تستغل ثغرات تانية معاها
    
- سكربت داخلي بيعكس مدخلات = مرشح للاستغلال
    
- CSP مش دايمًا فعالة لو MIME sniffing شغال
    
- دمج XSS في HTML + JS بيخلق سيناريوهات استغلال قوية
    

---

## 🔁 Where to Retest

- صفحات HTML بتعكس مدخلات جوه HTML
    
- سكربتات `.php` أو `.js` بتعكس مدخلات جوه JavaScript
    
- مواقع بتستخدم CSP وبتسمح بتحميل سكربتات من نفس الدومين
    

---

## 


## Checklist (CSP Bypass via MIME Sniffing)

- ( ) هل يوجد انعكاس HTML مباشر؟ مثل: `<h1>test</h1>`
    
- ( ) هل يوجد سكربت داخلي يعكس مدخلات داخل JavaScript؟
    
- ( ) هل يمكن كسر كود JavaScript بحقن مثل: `);alert(1);//`؟
    
- ( ) هل يمكن تحميل السكربت من خلال `<script src=...>`؟
    
- ( ) هل سياسة CSP تسمح بتحميل السكربت لأنه من نفس الدومين؟
    
- ( ) هل المتصفح يستخدم MIME sniffing ويهمل نوع المحتوى؟
    
- ( ) هل تم تنفيذ الكود فعليًا رغم وجود CSP؟
    

---# Bug Writeup Summary – Web Cache Poisoning → Reflected XSS → Account Takeover

**🔗 Link:**  
[https://lutfumertceylan.com.tr/posts/acc-takeover-web-cache-xss/](https://lutfumertceylan.com.tr/posts/acc-takeover-web-cache-xss/)

---

## 🎯 Target

- موقع شركة ألعاب فيديو فيه باراميتر `language` ظاهر في كل صفحات الموقع
    
- الباراميتر ده بيعكس القيمة مباشرة في الـ HTML، بدون أي فلاتر
    

---

## 🧠 Vulnerability

- Reflected XSS في `language` parameter
    
- القيم اللي بيدخلها المستخدم بتتحفظ في **الـ Web Cache** ← تصعيد للثغرة
    

---

## 🚪 Entry Point

- عند فتح الرابط مع القيمة المصابة، القيمة بتنعكس داخل الصفحة
    
- السيرفر بيكاش الباراميتر الضعيف `language` جزئيًا (مش الصفحة كلها)
    

---

## ⚙️ Payloads

### 🧪 PoC – Reflected XSS:

أدخلنا `1</script><svg/onload=confirm("document.cookie")>` في باراميتر `language`  
✅ تم تنفيذ XSS بنجاح وسرقة الـ session

---

### 🕵️‍♂️ Exploitation via Web Cache Poisoning:

بسبب إن السيرفر بيكاش القيمة المصابة، نفس البايلود بقى بيتعرض في صفحات تانية بشكل تلقائي

📌 مثال على السيناريو:

- المهاجم بيزور الرابط ويحقن البايلود
    
- الموقع بيكاش القيمة داخل الصفحة
    
- أي ضحية تزور نفس الصفحة بعد كده بتشوف البايلود بيتنفذ تلقائيًا
    

---

## 🧬 Exploitation Logic

- Reflected XSS داخل `language` parameter
    
- القيمة بتتخزن في الكاش ← **Web Cache Poisoning**
    
- الضحية لما تزور أي صفحة فيها الباراميتر، بيتم تنفيذ الكود تلقائي
    
- مفيش `HttpOnly` أو `Secure` أو `CSP` تمنع سرقة الـ cookies
    

---

## 💥 Impact

- سرقة الجلسة (Session Hijacking)
    
- إمكانية تنفيذ **Account Takeover** لكل مستخدم يزور الموقع بعد التلويث
    
- انتشار الثغرة عبر الكاش = عدد ضحايا أكبر بدون تدخل مباشر
    

---

## 📌 Lessons Learned

- Web Cache Poisoning ممكن يحوّل XSS بسيطة لثغرة شديدة الخطورة
    
- غياب `HttpOnly`, `Secure`, و `CSP` بيخلّي الثغرات أسهل في الاستغلال
    
- لازم تتأكد إن الباراميترات مش بتنعكس مباشرة بدون فلاتر
    
- الـ caching الجزئي أخطر من الكامل لو حصل في مناطق فيها مدخلات مستخدم
    

---

## 🔁 Where to Retest

- أي باراميتر بيظهر في كل الصفحات (زي language, theme, preview)
    
- سيرفرات بتستخدم Web Cache داخلي أو CDN بدون فلترة على القيم
    
- مواقع بتخزن أجزاء من الرد في الكاش بدون التحقق من صلاحية المحتوى
    

---

## ✅ Checklist (Web Cache Poisoning → XSS → ATO)

- ( ) هل فيه باراميتر منعكس في صفحة HTML؟
    
- ( ) هل القيمة المصابة ممكن تخزن في الكاش؟
    
- ( ) هل تم تنفيذ XSS باستخدام payload بسيط زي: `</script><svg/onload=alert(1)>`؟
    
- ( ) هل الموقع بيستخدم كاش داخلي أو CDN؟
    
- ( ) هل في غياب تام لـ `HttpOnly`, `Secure`, أو `CSP`؟
    
- ( ) هل يمكن الوصول لنفس الصفحة من الضحية بعد التلويث؟
    
- ( ) هل حصل تنفيذ تلقائي للبايلود بعد الكاش؟
    

---