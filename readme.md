
ไฟล์ต่อไปนี้ได้รวบรวบช่องโหว่ที่เกิดขึ้นบนการตั้งค่า HTTPS ที่ประกอบไปด้วยรายละเอียดของช่องโหว่ ระดับความเสี่ยงตาม CVSS Base Score v.3 และวิธีการแก้ไขช่องโหว่ โดยจำแนกตามองค์ประกอบของ HTTPS ตามรายการต่อไปนี้
#Protocol
##Obsoleted / Vulnerable Protocol
| CVSS v3 Base Score | Vector |
| :---: | :---: |
|up to 7.5 | AV:N/AC:M/Au:N/C:C/I:N/A:N |
ปัจจุบันมี protocol ในกลุ่ม SSL/TLS ที่ใช้งานกันถึง 6 version:
- SSL v2 ไม่ปลอดภัย และ ห้ามนำมาใช้งาน เนื่องจาก version ดังกล่าวมีช่องโหว่ร้ายแรงทำให้สามารถเข้าถึง key และสามารถนำไปใช้โจมตีเว็บไซต์ที่มีชื่อเดียวกันได้แม้อยู่คนละ server (DROWN attack).
- SSL v3 ไม่ปลอดภัยเมื่อมีการใช้งานกับ HTTP (POODLE attack) และไม่แข็งแรงพอเมื่อนำไปใช้ร่วมกับ protocol อื่น จัดว่าเป็น version ที่ล้าสมัยแล้ว
- TLS v1.0 และ TLS v1.1 เป็น protocol ที่ล้าสมัครและไม่ควรนำมาใช้งาน ทั้งนี้ TLSv1.0 ถูกประกาศให้เลิกใช้งานโดย PCI-DSS แล้ว ตั้งแต่เดือนมิถุนายน ปี 2018 และ TLSv1.1ถูกเลิกใช้ใน web browser โดยส่วนใหญ่แล้วตั้งแต่ปี 2020
- TLS v1.2 และ v1.3 เป็น version ที่ยังไม่มีช่องโหว่ได้รับการเปิดเผย ทั้งนี้ TLS v1.3 มีการพัฒนาเพิ่มทั้งประสิทธิภาพ ความปลอดภัย รวมถึงยกเลิกใช้งาน feature ที่ไม่ปลอดภัย เช่น cipher suite ที่ไม่แข็งแรง และ compression

####Remediation:
- ใช้งานเฉพาะ TLS v.1.2 และ 1.3
- ในกรณีที่จำเป็นไม่สามารถใช้ version ข้างต้นได้ (เช่นกรณีที่ผู้ใช้งาน support เฉพาะ version ที่ล้าสมัย) ควรเลือกใช้งาน LS v1.0 และ TLS ไปก่อน แต่ควรมีแผนในการยกเลิกใช้งานในอนาคต 
#####สำหรับ windows server
สามารถแก้ไขค่า registry เพื่อตั้งค่า TLS ได้เอง โดยสามารถดูรายละเอียดได้ที
https://support.microsoft.com/en-us/help/245030/how-to-restrict-the-use-of-certain-cryptographic-algorithms-and-protoc
หรือสามารถใช้ IIS Crypto เพื่อช่วยในการตั้งค่า TLS 
https://www.nartac.com/Products/IISCrypto 
#####สำหรับ web server อื่น ๆ
https://ssl-config.mozilla.org

####Ref:
https://github.com/ssllabs/research/wiki/SSL-and-TLS-Deployment-Best-Practices

#CIPHER SUITE
##Support insecure/weak Ciphers
| CVSS v3 Base Score | Vector |
| :---: | :---: |
|up to 5.3 | AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N |
Cipher suite คือชุดของ cryptographic algorithm ที่ทำหน้าที่ต่าง ๆ ใน protocol SSL/TLS ดังตัวอย่างในภาพ
![alt text](https://docs.microsoft.com/th-th/windows/win32/secauthn/images/tls-cipher-suite.png)

รายการต่อไปนี้เป็น cipher ที่ไม่ควรเปิดใช้งาน
#####Null cipher
>คือ cipher suite ที่ไม่มีการทำ encryption ข้อความ ทำให้มีความเสี่ยงที่ข้อมูลถูกเปิดเผย
ตัวอย่าง
- TLS_NULL_WITH_NULL_NULL
- TLS_DHE_PSK_WITH_NULL_SHA
- TLS_ECDHE_PSK_WITH_NULL_SHA256

#####Anonymous ciphers
>คือ cipher  suite ที่ไม่มีการทำ Key-Exchange authentication ทำให้มีความเสี่ยงที่ข้อมูลถูกเปิดเผย
ตัวอย่าง
- TLS_DH_anon_WITH_AES_256_GCM_SHA384

#####EXPORT ciphers 
>จัดเป็น low-grade cipher suite ที่ไม่แข็งแรงโดยมีช่องโหว่ที่เป็นช่องโหว่ well-know ที่สามารถ break encryption ของข้อความได้ 
ตัวอย่าง
-	EXP-RC4-MD5
-	EXP-EDH-RSA-DES-CBC-SHA
-	EXP1024-RC2-CBC-MD5

#####Insecure/Weak cipher อื่น ๆ 
>-	RC4 - insecure
-	64-bit block cipher (3DES / DES / RC2 / IDEA) - weak
-	weak ciphers (112 bits or less) - weak
-	RSA key exchange - weak

####Remediation:
ยกเลิกการสนับสนุน algorithm ที่ไม่ปลอดภัย หรือ ไม่มีความแข็งแรง 
#####สำหรับ windows server
สามารถแก้ไขค่า registry เพื่อตั้งค่า TLS ได้เอง โดยสามารถดูรายละเอียดได้ที
https://support.microsoft.com/en-us/help/245030/how-to-restrict-the-use-of-certain-cryptographic-algorithms-and-protoc 
หรือสามารถใช้ IIS Crypto เพื่อช่วยในการตั้งค่า TLS 
https://www.nartac.com/Products/IISCrypto 
#####สำหรับ web server อื่น ๆ
https://ssl-config.mozilla.org

####Ref:
- https://github.com/ssllabs/research/wiki/SSL-and-TLS-Deployment-Best-Practices
- https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.md

##Perfect Forward Secrecy is not enabled
| CVSS v3 Base Score | Vector |
| :---: | :---: |
|6.5 | AV:N/AC:H/PR:N/UI:N/S:U/C:H/I:L/A:N |
หากใช้ RSA ในกระบวนการ key exchange จะทำให้ค่า private key เดียวกันผูกกับทุก ๆ session จึงทำให้เกิดความเสี่ยงที่เมื่อ private key ถูกเข้าถึงได้โดยผู้ไม่ประสงค์ดี จะทำให้ผู้ไม่ประสงค์ดีสามารถถอดรหัสข้อความได้ในทุก session ได้
Perfect Forward Secrecy จัดเป็น key agreement protocol ที่จะเปลี่ยนวิธีในการสร้าง key โดย key ที่สร้างจะมีลักษณะเป็น session key ที่สร้างขึ้นเฉพาะ session หนึ่งเท่านั้น เพื่อให้มั่นใจว่าต่อให้ session key ถูกเข้าถึงได้ ผู้ไม่ประสงค์ดีจะสามารถถอดรหัสได้เฉพาะ session นั้น ๆ เท่านั้น แต่จะไม่สามารถถอดรหัส session อื่นได้

####Remediation: 
ใช้งาน  Elliptic Curve Diffie-Hellman (ECDHE) แทน RSA ในการทำ key exchange โดยสามารถตั้งค่าบน web server ได้ตามขั้นตอนในลิงก์ต่อไปนี้
https://www.digicert.com/kb/ssl-support/ssl-enabling-perfect-forward-secrecy.htm

####Ref:
https://www.digicert.com/kb/ssl-support/ssl-enabling-perfect-forward-secrecy.htm
 
#CERTIFICATE
##Use Weak TLS Certificate Keys
| CVSS v3 Base Score | Vector |
| :---: | :---: |
|4.2 | AV:A/AC:H/PR:N/UI:N/S:U/C:L/I:L/A:N |
ค่า Key ที่ใช้ใน TLS Certificate ขาดความแข็งแรง โดยมีขนาดที่น้อยเกินไป ทำให้มีความเสี่ยงทำให้ข้อมูลสำคัญที่ถูกเข้ารหัสอาจถูกทำให้รั่วไหลได้ด้วยเทคนิค brute-force attacks ค่า key ที่ใช้ควรมีขนาดที่เหมาะสมตามแต่ละ algorithm ที่ใช้ดังต่อไปนี้
- สำหรับ RSA key ควรมีขนาด 2,048 bits ขึ้นไป
- และสำหรับ ECDSA key ควรมีขนาด and 256 bits ขึ้นไป

####Remediation:
ทำการ reissue TLS certificate โดยใช้ค่า key ที่มีขนาดเหมาะสมดังต่อไปนี้
- สำหรับ RSA key ควรมีขนาด 2,048 bits ขึ้นไป
- และสำหรับ ECDSA key ควรมีขนาด and 256 bits ขึ้นไป

####Ref:
https://github.com/ssllabs/research/wiki/SSL-and-TLS-Deployment-Best-Practices

##TLS Certificate Signed Using Weak Hashing Algorithm
| CVSS v3 Base Score | Vector |
| :---: | :---: |
|5.3 | AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:L/A:N |

Certificate ถูก sign ด้วย algorithm ที่ไม่แข็งแรง เช่น MD2, MD4, MD5, หรือ SHA1 ซึ่งมีความเสี่ยงต่อการโจมตีรูปแบบ collision attacks ที่สามารถปลอมแปลงเป็น certificate ที่มี digital signature เดียวกันได้

####Remediation
ทำการ reissue TLS certificate โดยใช้ SHA-2 ในการ sign digital signature แทน

####Ref:
-
 
##Untrusted Certification
| CVSS v3 Base Score | Vector |
| :---: | :---: |
| up to 6.5 | AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:L/A:N |

เพื่อให้ certificate ถูกเชื่อถือโดยผู้ใช้ certificate ต้องถูก sign ด้วย certificate authority (CA) ที่น่าเชื่อถือ 

####Remediation: 
#####ในกรณีของเว็บไซต์ที่เป็น internet facing 
- ควรเป็น CA ที่เป็นที่รู้จักและถูกเชื่อถือโดยอัตโนมัติบน web browser
- หรือใช้บริการ LetsEncrypt ที่ให้บริการออก certificate โดยไม่มีค่าใช้จ่าย และถูกเชื่อถือโดย web browser หลายเจ้า

#####สำหรับ Internal application 
- สามารถใช้ internal CA ในการ sign certificate ซึ่ง certificate ดังกล่าวจะถูกเชื่อถือโดยผู้ใช้ที่มีการติดตั้ง internal CA certificate 

####Ref:
-

##Certificate Chain of Trust Incomplete
| CVSS v3 Base Score | Vector |
| :---: | :---: |
| up to 6.5 | AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:L/A:N |
ในหลายครั้งที่ certificate ไม่ได้ถูก sign โดยตรงจาก root CA ซึ่งกรณีดังกล่าว certificate จะถูก sign ด้วยตัวกลางคือ intermediate CA
หากผู้ใช้ไม่รู้จักหรือไม่เชื่อถือ intermediate CA นั้น ๆ จะทำให้ certificate ไม่ผ่านการตรวจสอบและไม่ได้การเชื่อถือจากผู้ใช้

####Remediation:
เพื่อที่จะสามารถสร้าง chain of trust ได้ server จะต้องให้ intermediate certificate ที่มี พร้อมกับ certificate หลักแก่ผู้ใช้

####Ref:
https://www.ssl.com/faqs/what-is-a-chain-of-trust/

##Certificate Domain Names (Observation)
domain name บน certificate (หรือ subject) ต้องตรงกับค่า fully qualified name (FQDN) ซึ่งในอดีตจะอยู่ใน attribute commonName (CN) ของ certificate แต่อย่างไรก็ตามใน browser รุ่นใหม่บางรายเช่น chrome ไม่ยอมรับ certificate ที่ใช้ค่า CN แต่รองรับ subjectAlternativeName (SAN) แทน ดังนั้นเพื่อให้ไม่เกิดปัญหาในการใช้งานของผู้ใช้ก็ควรใช้งานร่วมกันทั้ง CN และ SAN
- ควรพิจารณาว่าควรใส่ subdomain “www” หรือไม่
- ห้ามใส่ non-qualified hostnames
- ห้ามใส่ค่า IP addresses
- ในกรณีที่เป็น certificate ของ internet facing service ไม่ควรใส่ข้อมูล internal domain names ลงใน certificate
>หาก server มีความจำเป็นต้องใช้งานทั้ง internal และ external ควรออก certificate แยกกันใน 2 ช่องทาง

##Wildcard Certificates (Observation)
Wildcard Certificates เป็นการใช้งาน certificate เพียงอันเดียว แต่สามารถใช้ได้กับหลาย subdomain เช่น *.example.org นำมาซึ่งความสะดวกสบาย แต่ทั้งนี้ก็มีความเสี่ยงที่เมื่อ private key ถูกขโมย subdomain อื่นก็จะได้รับผลกระทบด้วยเช่นกัน
- ควรใช้ Wildcard Certificates แต่ในกรณีที่จำเป็นเท่านั้น
- ห้ามใช้ wildcard certificates กับระบบที่มี trust level แตกต่างกัน โดยสามารถพิจารณาจากตัวอย่างต่อไปนี้
>- VPN gateway 2 อันสามารถใช้ wildcard certificate ร่วมกันได้
>- web application ที่มีหลาย instance สามารถใช้ wildcard certificate ร่วมกันได้
>- VPN gateway กับ public webserver ไม่ควรใช้ wildcard certificate ร่วมกัน
>- public webserver กับ  internal server ไม่ควรใช้ wildcard certificate ร่วมกัน
- จำกัด  scope ของ  wildcard certificate โดยแบ่งตาม subdomain (เช่น *.foo.example.org) หรือแบ่งตาม domain name

#OTHER
##Supports Weak Diffie-Hellman (DH) Key Exchange Parameter
| CVSS v3 Base Score | Vector |
| :---: | :---: |
| up to 3.7 | AV:N/AC:H/PR:N/UI:N/S:U/C:N/I:L/A:N |
Diffie-Hellman key exchange เป็น cryptographic algorithm ที่นิยมใช้งานใน internet protocal เพื่อตกลงค่า session key ในการสื่อสารแบบเข้ารหัสอย่างบน HTTPS
เมื่อมีการใช้งาน Diffie-Hellman algorithm ในขั้นตอน key exchange หากใช้ค่า prime numbers มีขนาดน้อยกว่า 1024 bits มีความเสี่ยงที่จะถูกคาดเดาได้ และจากเกณฑ์ของ PCI DSS ค่า prime numbers 
ต้องมีขนาด 2048 bits ขึ้นไป

####Remediation:
- ตั้งให้ใช้งาน Diffie-Hellman algorithm โดยใช้ prime numbers ที่มีขนาด 2048 bits ขึ้นไป
- ปิดการใช้งาน Diffie-Hellman และใช้งาน Elliptic-curve Diffie-Hellman (ECDH) แทน

####Ref: 
https://weakdh.org
 
##Compression Is not Disable
| CVSS v3 Base Score | Vector |
| :---: | :---: |
| 9.1 | AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N |
Compression Ratio Info-leak Made Easy (CRIME) เป็นช่องโหว่ที่อาศัยการทำงานร่วมกันของ protocol HTTPS และ SPDY ที่มีการทำ compression มีความเสี่ยงที่ทำให้ผู้ใช้ถูกโจมตีรูปแบบ session hijacking

####Remediation:
ปิดการสนับสนุน compression บน web server โดนสามารถดูขั้นตอนได้จากลิงก์ต่อไปนี้
https://crashtest-security.com/prevent-ssl-crime/

####Ref:
https://blog.qualys.com/product-tech/2012/09/14/crime-information-leakage-attack-against-ssltls

##Missing HTTP Strict Transport Security Policy
| CVSS v3 Base Score | Vector |
| :---: | :---: |
| 6.5 | AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:L/A:N |
HSTS header (HTTP Strict Transport Security) ซึ่งเป็น header ที่บังคับให้ client ติดต่อสื่อสารกับ server ผ่านทาง HTTPS protocol เท่านั้น อีกทั้งยังเป็น header ที่ช่วยลดความเสี่ยงจากการถูกโจมตีโดยเทคนิค SSLStrip อีกด้วย

Remediation:
- Redirect HTTP traffic ทั้งหมดไปยัง HTTPS
- Enable HSTS
- สำหรับกรณีที่ต้องการเพิ่มเว็บไซต์ไว้ใน preload list ของ web browser ซึ่งจะช่วยให้ web browser เรียกใช้งานเว็บไซต์ผ่าน HTTPS ตั้งแต่ครั้งแรก 
>- ตั้งค่า HSTS ตามขั้นตอนข้างต้นอย่างครบถ้วน
>- ใช้งาน valid certificate 
>- ทุก subdomains ต้องใช้งาน HTTPS โดยเฉพาะอย่างยิ่ง www subdomain
>- ตั้งค่า HSTS header บน base domain ตามรายการต่อไปนี้:
>>- ตั้งค่า max-age อย่างน้อย 31536000 วินาที (1 ปี)
>>- ตั้งค่าระบุ includeSubDomains 
>>- ตั้งค่าระบุ preload
>>- หากมีการ redirect ไปที่อื่น ในขั้นตอน redirect จะต้องมี HSTS header ตอบกลับมา (ไม่ใช่เพียงเฉพาะหน้าเว็บไซต์ที่ redirect ไป)

####Ref:
- https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html
- https://hstspreload.org

##Mixed HTTP Content (Observation)
หากเว็บไซต์มีการใช้งาน HTTPS แต่มีการเรียก content file ผ่าน protocol ที่ไม่ได้เข้ารหัส เช่น ไฟล์ CSS, JS ไฟล์เหล่านี้มีความเสี่ยงที่ถูกโจมตีในรูปแบบดักอ่านข้อมูลหรือปลอมแปลงข้อมูลได้ ในปัจจุบัน web browser บางตัวมีการ block การเรียก content ผ่าน protocol ที่ไม่ได้เข้ารหัสมาแสดงบนเว็บไซต์เข้ารหัสอีกด้วย


