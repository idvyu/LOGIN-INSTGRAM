# - الفصل الأول تحويل الحزم (Chapter 1 — Packet Switching) - #2

## Chapter 1 — Packet Switching | CCNP Enterprise

### 1. Network Device Communication

* وظيفة الشبكة الأساسية: توفير الاتصال بين الأجهزة.
* **Switch** يعمل بشكل أساسي في **Layer 2** ويعتمد على **MAC Address**.
* **Router** يعمل في **Layer 3** ويعتمد على **IP Address**.
* نموذج **OSI** يتكون من 7 طبقات.
* Layer 2 تستخدم **MAC Address**.
* Layer 3 تستخدم **Logical Addressing / IP Address**.

***

### 2. Collision Domain

* **Collision Domain** هو النطاق الذي يمكن أن تحدث فيه تصادمات.
* آلية **CSMA/CD** مرتبطة باكتشاف التصادم في Ethernet.
* **Duplex** يحدد طريقة إرسال واستقبال البيانات.
* الـ **Hub** يجعل الأجهزة ضمن Collision Domain واحد.
* الـ Switch يفصل Collision Domains لكل منفذ.

***

### 3. Broadcast Domain

* الـ **Broadcast** هو Traffic موجه لجميع الأجهزة داخل الشبكة المحلية.
* الـ Broadcast لا يعبر الراوتر.
* إضافة Router تساعد على تقسيم Broadcast Domains.
* **VLANs** تسمح بتقسيم الشبكة منطقيًا إلى عدة Broadcast Domains.
* يمكن أن توجد عدة VLANs على نفس الـ Switch.

***

## 4. VLAN

الـ VLAN توفر تقسيمًا منطقيًا للشبكة.

#### Access Port

* يحمل **VLAN واحدة**.
* يستخدم عادةً لتوصيل أجهزة المستخدمين.
* أهم الأوامر:

```
switchport mode access
switchport access vlan 10
```

#### Trunk Port

* يحمل **عدة VLANs**.
* يستخدم بين:
  * Switch ↔ Switch
  * Switch ↔ Router
  * Switch ↔ Firewall

الأمر:

```
switchport mode trunk
```

ويستخدم **802.1Q** لتمييز VLANs على الـ Trunk.

***

## 5. Native VLAN

* الـ Native VLAN مرتبطة بالـ 802.1Q Trunk.
* الـ Traffic الخاص بها يمر بدون VLAN Tag.
* يجب أن تكون Native VLAN متوافقة بين طرفي الـ Trunk.

***

## 6. Allowed VLANs

يمكن تحديد VLANs المسموح لها بالمرور عبر الـ Trunk:

```
switchport trunk allowed vlan 10,20,30
```

يمكن إضافة VLAN:

```
switchport trunk allowed vlan add 40
```

وحذف VLAN:

```
switchport trunk allowed vlan remove 40
```

ويمكن السماح بكل الـ VLANs أو حذفها حسب الحاجة.

***

## 7. MAC Address Table

الـ Switch يبني **MAC Address Table** لمعرفة:

> MAC Address → Switch Port

السويتش يتعلم MAC Address من **Source MAC Address** للحزم التي يستقبلها.

#### Dynamic MAC

يتعلمها السويتش تلقائيًا.

#### Static MAC

يتم تحديدها يدويًا.

أوامر مهمة:

```
show mac address-table
```

```
show mac address-table dynamic
```

يمكن أيضًا تصفية النتائج حسب VLAN أو MAC.

***

## 8. Unknown Unicast

إذا وصلت حزمة إلى Switch والـ MAC Address الخاص بالوجهة غير موجود في MAC Address Table:

* السويتش يعمل **Flooding**.
* يرسل الحزمة إلى المنافذ الأخرى.
* لا يرسلها إلى المنفذ الذي استقبل الحزمة.

عندما يرد الجهاز المطلوب، يستطيع السويتش تعلم MAC Address الخاص به.

***

## 9. Broadcast

الـ Broadcast يتم إرساله لجميع الأجهزة الموجودة داخل الـ Broadcast Domain.

لكن:

> الـ Broadcast لا يعبر Router.

والـ VLANs تستخدم لتقسيم Broadcast Domains.

***

## 10. ARP

عند وجود جهاز يريد معرفة MAC Address المرتبط بـ IP Address:

**ARP** يستخدم للحصول على MAC Address.

بشكل مبسط:

```
IP Address → MAC Address
```

ويتم الاحتفاظ بهذه المعلومات في **ARP Table**.

***

## 11. Packet Forwarding داخل نفس الشبكة

إذا كان الجهاز والوجهة في نفس الشبكة:

1. الجهاز يعرف IP الوجهة.
2. يتأكد أن الوجهة في نفس الشبكة.
3. يستخدم ARP لمعرفة MAC Address.
4. يرسل Frame إلى MAC Address الخاص بالوجهة.
5. الـ Switch يستخدم MAC Address Table لتحديد المنفذ.

***

## 12. Packet Forwarding إلى شبكة أخرى

إذا كانت الوجهة في شبكة مختلفة:

1. الجهاز يحدد أن الوجهة ليست في نفس الشبكة.
2. يرسل الحزمة إلى **Default Gateway**.
3. يحتاج الجهاز إلى MAC Address للـ Gateway.
4. يستخدم ARP لمعرفة MAC Address.
5. يرسل Frame إلى الراوتر.
6. الراوتر يفحص **Routing Table**.
7. يحدد الـ Next Hop / Exit Interface.
8. يتم إعادة بناء Layer 2 Frame للوصلة التالية.
9. تستمر الحزمة حتى تصل إلى وجهتها.

***

## 13. Routing Table

الراوتر يستخدم **Routing Table** لمعرفة الطريق المناسب للوجهة.

يمكن أن تحتوي المعلومات على:

* Connected Networks
* Static Routes
* Dynamic Routes

الراوتر يبحث عن المسار المناسب ثم يحدد:

> أين أرسل الحزمة بعد ذلك؟

***

## 14. Router Interfaces

يمكن إعطاء Interface عنوان IP:

```
interface ...
ip address X.X.X.X X.X.X.X
```

ثم تشغيل الواجهة حسب التهيئة المطلوبة.

الـ Interface الذي يحتوي على IP ويعمل بشكل صحيح يمثل شبكة متصلة بالراوتر.

***

## 15. Subinterfaces

يمكن إنشاء Interfaces منطقية مشتقة من Interface أساسي.

تستخدم هذه الفكرة مع تصميمات مثل **Router-on-a-Stick** للتعامل مع عدة VLANs.

***

## 16. Layer 3 Switch

الـ Layer 3 Switch يستطيع تنفيذ وظائف Routing بالإضافة إلى Switching.

يمكن استخدام **SVI** لتمثيل Gateway للـ VLAN.

مثال الفكرة:

```
VLAN 10 → Gateway
VLAN 20 → Gateway
VLAN 30 → Gateway
```

وبذلك يمكن للسويتش تنفيذ **Inter-VLAN Routing**.

***

## 17. Cisco Express Forwarding — CEF

**CEF** هي آلية Forwarding من Cisco لتسريع عملية تحويل الحزم.

الفكرة الأساسية:

بدل الاعتماد على المعالجة التقليدية لكل Packet، يتم تجهيز معلومات تساعد الـ Hardware على تنفيذ Forwarding بسرعة.

أهم مكونات CEF المذكورة:

#### FIB

**Forwarding Information Base**

تحتوي معلومات تستخدم لاتخاذ قرار Forwarding.

#### Adjacency Table

تحتوي معلومات مرتبطة بالجيران وLayer 2 اللازمة لإرسال الحزمة.

***

## 18. Software vs Hardware Forwarding

#### Software Forwarding

* يعتمد على المعالجة البرمجية.
* يتم التعامل مع الحزم بواسطة CPU.
* أبطأ من Hardware Forwarding.

#### Hardware Forwarding

* يعتمد على Hardware مخصص.
* أسرع في Forwarding.
* مناسب لمعدلات Traffic مرتفعة.

***

## 19. TCAM

**TCAM** ذاكرة عالية السرعة تستخدم لمطابقة قواعد وحقول الشبكة في Hardware.

الفكرة المذكورة في الدرس:

```
Value + Mask → Result
```

* **Value**: القيمة التي يتم البحث عنها.
* **Mask**: يحدد الجزء المهم من البيانات.
* **Result**: الإجراء الناتج عند وجود Match.

ميزة TCAM:

> سرعة عالية في عمليات Matching.

***

## 20. Centralized Switching

في **Centralized Switching**:

* يوجد محرك تحويل مركزي.
* يستقبل المعلومات.
* يفحص Headers.
* يحدد منفذ الخروج.
* يرسل الحزمة إلى المنفذ المطلوب.

***

## 21. Distributed Switching

في **Distributed Switching**:

* كروت الإدخال والإخراج يمكن أن تحتوي على محركات Forwarding.
* يتم اتخاذ قرار Forwarding محليًا.
* إذا كان منفذ الخروج على نفس الكرت → يتم الإرسال محليًا.
* إذا كان على كرت آخر → تمر الحزمة عبر **Switch Fabric / Backplane**.

الميزة الأساسية:

> توزيع عملية Forwarding بدل الاعتماد الكامل على وحدة مركزية.

***

## 22. Hardware Forwarding Tables

الـ Hardware يحتاج إلى جداول جاهزة تساعده على تنفيذ Forwarding بسرعة.

من المعلومات المذكورة:

* Routing Information
* IP Prefixes
* Adjacency Information
* Layer 2 Information

عند حدوث تغيير في الشبكة يتم تحديث المعلومات المستخدمة في Forwarding.

***

## 23. High Availability / Stateful Switchover

الفكرة:

إذا حدثت مشكلة في وحدة المعالجة الأساسية، يمكن للوحدة الاحتياطية استلام التحكم.

الهدف:

> استمرار Forwarding وتقليل تأثير فشل وحدة التحكم.

ويتم الاحتفاظ بمعلومات تساعد الوحدة الاحتياطية على الاستمرار في العمل.

***

## 24. Memory / Resources

السويتش يستخدم موارد وذاكرة مخصصة لجداول مختلفة.

إذا امتلأت موارد Hardware، قد تتم معالجة بعض العمليات بواسطة CPU، وهذا يمكن أن يؤثر على الأداء.

يمكن تعديل تخصيص بعض الموارد باستخدام Templates حسب المنصة.

***

## 25. أهم الأوامر في الفصل

```
show mac address-table
```

عرض MAC Address Table.

```
show mac address-table dynamic
```

عرض الـ Dynamic MAC Entries.

```
show interfaces
```

عرض معلومات الـ Interfaces.

```
show interfaces status
```

عرض حالة المنافذ بشكل مختصر.

***

## ⭐ الأشياء التي تحفظها للاختبار

| المصطلح                | المعنى                      |
| ---------------------- | --------------------------- |
| Layer 2                | Switching / MAC             |
| Layer 3                | Routing / IP                |
| MAC Address Table      | MAC → Port                  |
| ARP                    | IP → MAC                    |
| Access Port            | VLAN واحدة                  |
| Trunk Port             | عدة VLANs                   |
| 802.1Q                 | VLAN Tagging                |
| Native VLAN            | Untagged VLAN على Trunk     |
| Broadcast Domain       | نطاق الـ Broadcast          |
| Collision Domain       | نطاق التصادم                |
| FIB                    | Forwarding Information Base |
| Adjacency Table        | معلومات الجيران             |
| CEF                    | Cisco Express Forwarding    |
| TCAM                   | Hardware Matching Memory    |
| Centralized Forwarding | Forwarding مركزي            |
| Distributed Forwarding | Forwarding موزع             |
| SVI                    | Layer 3 Interface للـ VLAN  |
| Default Gateway        | البوابة للشبكات الأخرى      |



إذا بتذاكره للاختبار، ركّز خصوصًا على:

**VLAN → Access/Trunk → 802.1Q → Native VLAN → MAC Table → ARP → Routing → CEF → FIB → Adjacency → TCAM.**
