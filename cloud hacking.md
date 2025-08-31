
- --
## ▪ What is Amazon S3? 
▪ Amazon Simple Storage Service (S3) is a cloud-based object storage service. 
▪ It allows users to store and retrieve any amount of data at any time from anywhere.
▪ Used for a wide variety of applications: backups, file storage, static website hosting, etc. 
## ▪ Why Focus on S3 Hacking? 
▪ Misconfigured S3 buckets can expose sensitive data to unauthorized users. 
▪ Attackers exploit weak or improper access control to read or write into the buckets.
- دول يعتبر اساسيات او مفاهيم لازم تكون عارفها عن S3
- بنركز علي S3 عشان بيكون في misconfigration كتير 
---
## Bucket:
▪ A container for objects stored in Amazon S3. 
▪ Object:
▪ Files stored in a bucket (e.g., images, documents, backups). 
## Permissions: 
▪ Public/Private access settings control who can read/write to a bucket. ▪ Policies and Access Control Lists (ACLs) specify user and role permissions
- خلي بالك ان premissions هي دي ال بيكون فيها اللعب 
- --
## Common Access Levels:
- ▪ Public Read: Anyone can read objects in the bucket. 
- ▪ Public Write: Anyone can write objects to the bucket (dangerous!).
- ▪ Private: Only authenticated users can access the bucket.
- يعني في حاله وجود خطا زي انه public بيكون متاح ليه write دي بيكون ثغره وتقدر تستغلها 
- --
- دي تعتبر اهم الادوات ال هنستعمها في cloud hacking 
- اول واحد هو هنسخدمه فعلا الباقي دول للعلم فقط 
- ▪ AWS CLI (Command Line Interface): Official AWS command-line tool for interacting with S3 buckets.
- ▪ Boto3: Python SDK for AWS, useful for scripting interactions with S3. 
- ▪ S3Scanner, S3BucketList: Tools used for discovering and enumerating open or misconfigured S3 buckets. 
- ▪ Burp Suite with AWS Extension: Can be used for testing S3
- --

![](Pasted /images ٢٠٢٥٠٨٢٩١٥١٠٤٦.png)
- اولا نت عشان تاجر سيرفر بيدوك حاجه زي username  الحاجه دي بعد كدا بيتستعملوها في  ان هما بيدوك bucket ممكن تتاجر سيرفر مثلا لحد 1 TB ram او اكتر 
- بعد كدا هما بيربطوا الدومين بتاعك ال نت ماجرله السيرفر عليه عن طريق CNAME  
- ممكن تعرفه عن طريق امر dig + domain name  من غير http or https 
- --
- - ➢Bucket can be as ➢http://s3.amazonaws.com/[bucket_name]/ ➢http://[bucket_name].s3.amazonaws.com/ 
- ➢To Get the bucket name from the subdomain =>>
-  dig CNAME subdomain.example.com
- طبعا اشكال ال ممكن تلاقي فيها bucket name ممكن تلاقي region وممكن لا عادي 
- خلي بالك ممكن لو معاك اكتر من subdomain  تستعمل امر   dig -f subs.txt CNAME | grep s3
- --
![[Pasted image ٢٠٢٥٠٨٢٩١٥١٤٣٠.png]]
- زي كدا وخلي بالك بردو ان اي ip 50 دا بيكون تبع amazonaws     عموما  54 - 52
- https://ip-ranges.amazonaws.com/ip-ranges.json  والموقع دا فيه كله ip rangeلو عاوز تشوفه 
- نت بستغل الموضوع دا ان لو مثلا مده الايجار دي خلصت وصاحب الدومين ما جددش الايجار 
- نت بتروح تاجر مكانه وطبعا بتكون كدا استحوذت علي domain 
- وبردو لو قدرت تعمله     ls           cp        sync        upload         delete 
- --- 
- عندك مويقع زي http://flaws.cloud/  دا بيدك مراحل ازاي تستغل ال cloud (تطبق عليه)
- https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html الموقع دا خش سجل عليه هيديك مفاتيح aws مجاني هتفيدك وتقدر تحمل منه امر aws بس الزم رقم فيزا تقدر تحول بيه برا مصر لانه ممنوع الاشتراك في اي موقع حتي ولو بدولار واحد 
- دول مفاتيح تقدر تستعملها 
-  sudo apt install awscli
-  aws configure
- access_key AKIAJ366LIPB4IJK7SA
- secret_access_key OdNa7m+bqUvF3Bn/qgSnPE1kBpqcBTTjqwP83JysD 
- حاول تجيب مفاتيح لان في الغالب المفاتيح دي مش شغاله 

----
- ![[Pasted image ٢٠٢٥٠٨٢٩١٧٤٣١٣.png]]
- - لو ظهرلك مثلا ip 50 زي م قولنا عشان تعرف اي المتركب عليه بتستعمل nslookup +ip 
- دا الامر بعد ما بتلاقي bucketname طبعا  
- aws s3 command s3 : bucketname --region  --no-sign-request
- ممكن لو ملاقتش bucket name تجرب ب domain name + region
- You can use --no-sign-request if you don't have aws keys
- --
## To copy the content of the bucket and download it

- aws s3 cp s3:// bucket name /file name  ./ 

## To upload data or files to the bucket cloud

- aws s3 cp ./example.com s3:// bucket name 
- خلي بالك ان في الغالب لازم يكون معاك مفاتيح عشان خطوه upload 
- ---
## ACLs: 
▪ Define who can access the bucket and at what level (read/write). 
▪ Improperly configured ACLs can allow public access to sensitive files. 
## S3 Bucket Policies: 
▪ JSON-based policies used to grant or deny permissions at the bucket or object level

---
## Common Misconfigurations: 
▪ Allowing s3:ListBucket to Everyone:
▪ This allows anyone to list the bucket contents. 
▪ Allowing s3:GetObject to Everyone: 
▪ This allows anyone to download files from the bucket. 
▪ Allowing s3:PutObject to Everyone: ▪ This allows anyone to upload files to the bucket

--- 
![[Pasted image ٢٠٢٥٠٨٣١٢٠٣٧٢٨.png]]
- هنا بق دا بيديك ACL عشان تشوف تقدر تعمل اي بمعني اصح انك تشوف اي misconfigrationخلي بالك من الخطوه حتي لو رفض يعملك ls جرب الامر دا ممكن تعدل منه صلاحياتك وبعدين تشوف كل حاجه 
- خد بالك نت هنا ممكت تقدر تغير صلاحياتك زي مثلا تغير READ => WRITE بس بيكون في حاله لو مسموح ليك بس يعني يكون في خطا 
- بتحمل الملف بتاخد منه cp وبعدين بتعدله وترفعه تاني للسيرفر وبكدا بتكون اخدت كل الصلاحيات 
- ![[Pasted image ٢٠٢٥٠٩٠١٠٠٢١٢٣.png]]
- زي الحاله دي  ودي تعتبر ثغره CRITICAL 
FULL_CONTROL: Grants full control over the bucket. 
   ▪ WRITE: Allows writing to the bucket. 
   ▪ READ: Allows reading from the bucket. 
   ▪ READ_ACP: Allows reading the ACL 
- WRITE_ACP: Allows writing the ACL
- You can access the bucket but with specific permissions as read or write or write-policy or
-  https://assets.ine.com/labs/ad-manuals/walkthrough 2304.pd
---
https://buckets.grayhatwarfare.com/ 
- الموقع دا لوفتحته هيديك كل bucket name المتاحه في العالم كله 
- ممكن تفلترهم وتدور علي الموقع ال نت شغال عليه وغالبا هتلاقيه 
- ممكن تجرب تعمل ls ولو حصلت يبق ثغره عشان بتكون قدرت نت تشوف ملفات الموقع 
- ---
- 