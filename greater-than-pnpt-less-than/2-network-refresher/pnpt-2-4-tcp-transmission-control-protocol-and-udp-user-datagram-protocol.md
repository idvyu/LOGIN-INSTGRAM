# PNPT - 2 - 4 = TCP (Transmission Control Protocol) and UDP (User Datagram Protocol)

## شرح Layer 4 – Transport Layer

### طبقة النقل في نموذج OSI

الآن وصلنا إلى الطبقة الرابعة (Layer 4) في نموذج OSI، وتُسمى Transport Layer – طبقة النقل.

وظيفة هذه الطبقة هي تنظيم عملية نقل البيانات بين الأجهزة، والتأكد من وصولها بالطريقة المناسبة، وهنا عندنا بروتوكولين أساسيين لازم تفهم الفرق بينهم:

* TCP – Transmission Control Protocol
* UDP – User Datagram Protocol

### أولاً: ما الفرق بين TCP و UDP؟

[TCP Working: 3-Way Handshake & Reliable Communication | by Samir Bala | May, 2026 | Medium](https://images.openai.com/static-rsc-4/Ted5mVMMCQ9GKuMT43RRJfcXf5mUVApJceHUUFdu_9rN6hiZJYq5sJzmpq_ep7ZB_jON9RLFy9G_1wyt8JScHBgQm1W1TlllJpgxEySMWf81RYR3TMnv5dwWsNmMAhFziDYQxJMUkHmGA0CpVj0nHxGGNlhyptykVFcMhDXzX0g?purpose=inline)

TCP – بروتوكول موثوق

يُسمى Connection-Oriented، أي يعتمد على إنشاء اتصال بين الجهازين قبل نقل البيانات.

يهتم بموثوقية النقل، وترتيب البيانات، وإعادة إرسال البيانات المفقودة.

موثوقية عالية[HTTP vs HTTPS vs TCP vs UDP - The Visual Guide You Wish You Had Earlier - DEV Community](https://images.openai.com/static-rsc-4/sq3uska4iQGAjPy1Ab_ipPAoWOvW_diV2UdOUSwS1qb-0z-UK3tu28epLMItKxoIeXxmTlJI-3G_81D1wmIOvHXcvyHxDHyM_BomZQ0fXnynZDV9ty_rCVGiMb91FrH0wY_UtVAW7PDf6guAJSaUdnG75vpSnF9WpqIOIMMzR0Y?purpose=inline)

UDP – بروتوكول سريع وخفيف

يُسمى Connectionless، أي يرسل البيانات دون إنشاء اتصال مسبق بالطريقة التي يستخدمها TCP.

لا يضمن وصول كل البيانات أو ترتيبها، لكنه يقلل التأخير.

سرعة وتأخير أقل

#### أين نستخدم كل بروتوكول؟

| TCP          | UDP               |
| ------------ | ----------------- |
| HTTP / HTTPS | DNS غالباً        |
| SSH          | VoIP              |
| FTP          | بث الفيديو والصوت |
| نقل الملفات  | الألعاب أونلاين   |

مثال يوضح الفرق:

تخيل أنك تحمل ملفاً:

* TCP: يتأكد من وصول البيانات، وإذا فقد جزءاً منها يطلب إعادة إرساله.
* UDP: يرسل البيانات بسرعة، ولا ينتظر بالضرورة التأكد من وصول كل جزء.

لذلك TCP مناسب عندما تكون دقة البيانات مهمة، وUDP مناسب عندما يكون تقليل التأخير مهماً.

### ثانياً: TCP Three-Way Handshake

مفهوم مهم جداً في اختبار PNPT واختبارات الاختراق

بروتوكول TCP لا يبدأ نقل البيانات مباشرة، بل ينشئ اتصالاً باستخدام عملية اسمها:

Three-Way Handshake – المصافحة الثلاثية

تتكون من ثلاث خطوات:

### مراحل إنشاء اتصال TCP

الخطوة 1

SYN

الجهاز الأول يطلب إنشاء اتصال

الخطوة 2

SYN-ACK

الجهاز الثاني يستقبل الطلب ويرد بالموافقة

الخطوة 3

ACK

الجهاز الأول يؤكد استلام الرد ويكتمل الاتصال

TCP Connection Established

أصبح الاتصال جاهزاً لنقل البيانات

#### خلنا نبسطها بمثال واقعي

تخيل أنك ذهبت إلى جارك وتريد التحدث معه:

1. SYN: تقول له: السلام عليكم، هل أنت موجود؟
2. SYN-ACK: يرد عليك: وعليكم السلام، نعم أنا موجود.
3. ACK: تقول له: ممتاز، خلنا نتحدث.

الآن أصبح الاتصال جاهزاً.

وهذا بالضبط ما يحدث بين جهازك والخادم عند إنشاء اتصال TCP.

### ثالثاً: ما المقصود بـ Ports؟

الـ Port أو المنفذ هو رقم منطقي يستخدمه الجهاز لتحديد الخدمة أو التطبيق الذي يستقبل الاتصال.

تخيل أن عنوان IP هو عنوان المبنى، والـ Port هو رقم المكتب داخل المبنى.

مثلاً:

| الخدمة | رقم المنفذ |
| ------ | ---------- |
| HTTP   | 80         |
| HTTPS  | 443        |
| SSH    | 22         |
| FTP    | 21         |
| DNS    | 53         |

لدينا 65,535 منفذاً متاحاً لكل بروتوكول نقل (TCP وUDP).

مثال عملي:

عندما تفتح موقعاً يستخدم HTTPS، يتصل جهازك بعنوان IP الخاص بالخادم على المنفذ 443 باستخدام TCP في الحالة المعتادة.

يرسل جهازك SYN إلى الخادم، وإذا كان المنفذ مفتوحاً ويقبل الاتصال، يرد الخادم بـ SYN-ACK، ثم يرسل جهازك ACK.

وبذلك يتم إنشاء الاتصال.

### رابعاً: كيف نرى TCP و UDP عملياً باستخدام Wireshark؟

[Lab#1.1 - OSI model in practice | NetworkAcademy.IO](https://images.openai.com/static-rsc-4/zdvWP-q-banRppd6QnzVnbwfOQ4Lb9xjAjIldiX8CAhcK6HcrkfkQO17WSXRt_vNmWvxICmr3gesWTx6_kfjzYaIhdxG98QIudNlTExWGK9r0R9-d5RuUyVgxBGc3MCW6C0-gYVXvcuE8LLN9Coz88LzRmoTG9gvjl_nGiTyBnU?purpose=inline)[TCP Connection Establishment: An In-Depth Exploration | by Rohit S | Medium](https://images.openai.com/static-rsc-4/j22J6sUgasC0kOTQlpy02_9HVDuU5YViCS3OnFV5ZUnAbToBoT6F30LsJeXJzayzuXa-CCLPazQk7mhU6JYATIc038hO3GZj7GbWSDL64hQ747wBNcZO6b-IvvxwefCf3BdXqp13c3BZqYAqMN7QyQhK3TGLyxoU0Ak18tsrnjE?purpose=inline)[News - 极速电讯](https://images.openai.com/static-rsc-4/bv6gOVds0TbEPdntbJXuyw9YSV86xMDcjHobwX3nBmCKm3p57Ylv1DIOLOZuK3ytrdWemMSMHs2MWkf1mkSntG7KblKEktHXzmLJhn_f-9MHE4TjLShnE_sHSRnvCoFY463GlTRLY_0KrrolCIxXDltaZjvQiotTTZ_mJ71_m4g?purpose=inline)5

المدرب الآن ينتقل إلى أداة اسمها Wireshark، وهي أداة لتحليل حركة مرور الشبكة (Network Traffic Analysis).

وظيفتها التقاط وتحليل الحزم التي تمر عبر كرت الشبكة (Network Interface Card – NIC).

الخطوات التي نفذها:

1. فتح Wireshark على Kali Linux.
2. اختيار واجهة الشبكة لالتقاط البيانات.
3. بدء عملية Capture.
4. فتح المتصفح والدخول إلى Google.
5. تحديث الصفحة لإنشاء حركة مرور جديدة.
6. إيقاف الالتقاط وتحليل الحزم.

لاحظ المدرب وجود بروتوكولات مختلفة، ومنها TCP وUDP.

#### ماذا شاهد داخل Wireshark؟

وجد اتصالاً بين عنواني IP:

* Source IP: `192.168.57.39`
* Destination IP: `74.125.21.155`
* Destination Port: `443`

الجهاز الأول يحاول الاتصال بالخادم على المنفذ 443.

وشاهد تسلسل المصافحة الثلاثية:

| الحزمة  | معناها              |
| ------- | ------------------- |
| SYN     | طلب إنشاء اتصال     |
| SYN-ACK | رد الخادم بالموافقة |
| ACK     | تأكيد إنشاء الاتصال |

وهذا يوضح أن عملية الاتصال تحدث في الخلفية أثناء استخدامك للإنترنت، حتى لو لم تكن تراها.

### خامساً: لماذا هذا الموضوع مهم في اختبار الاختراق Penetration Testing؟

هنا نصل إلى الجزء المهم بالنسبة لمختبر الاختراق.

أثناء عملية الاستطلاع وفحص الشبكات (Scanning)، نحتاج إلى معرفة المنافذ المفتوحة والخدمات التي تعمل عليها.

نستخدم أدوات مثل:

* Nmap
* Wireshark

وسنحتاج إلى فحص TCP وUDP معاً.

والأهم أن فهم Three-Way Handshake سيساعدنا لاحقاً على فهم نوع متقدم من الفحص يسمى:

TCP SYN Scan – Stealth Scan

وهو أسلوب فحص يعتمد على إرسال SYN ومراقبة استجابة الهدف دون إكمال المصافحة الثلاثية بالطريقة المعتادة.
