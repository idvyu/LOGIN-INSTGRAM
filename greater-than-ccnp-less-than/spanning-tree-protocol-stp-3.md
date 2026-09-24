# - Spanning Tree Protocol (STP) - #3

## Chapter 2 — Spanning Tree Protocol (STP)

### 1. مقدمة عن STP

بروتوكول **STP (Spanning Tree Protocol)** يستخدم لمنع حدوث **Loops** داخل الشبكات التي تحتوي على عدة Switches.

فكرة STP الأساسية هي أن السويتشات تتبادل معلومات بينها باستخدام **BPDU**، ومن خلالها يتم بناء أفضل مسار داخل الشبكة، مع وضع بعض المنافذ في حالة **Blocking** لمنع الحلقات.

***

### 2. إصدارات STP

من أشهر الإصدارات:

* **STP 802.1D** — الإصدار الأصلي.
* **PVST+**
* **RSTP 802.1w**
* **Rapid PVST+**

والإصدارات الحديثة تعتمد على تحسينات تجعل عملية اكتشاف التغييرات والتعافي من الأعطال أسرع.

***

## 3. حالات منافذ STP

في STP التقليدي توجد عدة حالات للمنفذ:

#### 1. Disabled

المنفذ مغلق إداريًا ولا يعمل.

#### 2. Blocking

المنفذ لا يمرر User Traffic، ويمنع حدوث Loop.

#### 3. Listening

المنفذ يستعد للدخول في حالة Forwarding، لكنه لا يمرر البيانات ولا يتعلم MAC Addresses.

#### 4. Learning

المنفذ يبدأ بتعلم عناوين MAC وتحديث MAC Address Table، لكنه لا يزال لا يمرر User Traffic.

#### 5. Forwarding

المنفذ يمرر Traffic ويستطيع تعلم وتحديث MAC Address Table.

***

## 4. أنواع المنافذ الأساسية

### Root Port — RP

هو أفضل منفذ على السويتش للوصول إلى **Root Bridge**.

كل Non-Root Switch يكون لديه **Root Port واحد فقط**.

يتم اختياره بناءً على أقل Root Path Cost، ثم يتم استخدام معايير إضافية عند التعادل.

***

### Designated Port — DP

هو المنفذ المسؤول عن Forwarding على Segment معين.

يتم اختياره بناءً على أفضل مسار باتجاه Root Bridge.

***

### Alternate / Blocking Port

عندما توجد أكثر من وصلة يمكن أن تسبب Loop، يتم وضع إحدى الوصلات في حالة تمنع Forwarding.

وهذا هو جوهر عمل STP:\
**وجود مسار احتياطي بدون السماح بوجود Loop.**

***

## 5. Root Bridge

أول خطوة في STP هي تحديد **Root Bridge**.

كل Switch في البداية يعتبر نفسه Root Bridge، ثم يبدأ بمقارنة معلومات BPDU القادمة من السويتشات الأخرى.

يتم اختيار Root Bridge باستخدام:

1. أقل **Bridge Priority**.
2. إذا حدث تعادل → أقل **MAC Address**.

إذن القاعدة:

**Lowest Bridge ID = Root Bridge**

والـ Bridge ID يعتمد بشكل أساسي على:

**Priority + System ID Extension + MAC Address**

***

## 6. BPDU

الـ **BPDU (Bridge Protocol Data Unit)** هي الرسائل التي تستخدمها Switches للتواصل مع بعضها داخل STP.

من خلالها يتم تبادل معلومات مثل:

* Root Bridge
* Root Path Cost
* Bridge ID
* Port ID
* معلومات تغييرات الـ Topology

يوجد نوعان مهمان:

#### Configuration BPDU

تستخدم لتبادل معلومات STP الأساسية.

#### TCN BPDU

تستخدم للإبلاغ عن حدوث تغيير في الـ Topology.

***

## 7. أهم قيم STP الزمنية

#### Hello Time

الوقت بين رسائل BPDU.

القيمة الافتراضية:

**2 seconds**

#### Forward Delay

المدة التي يقضيها المنفذ في:

* Listening
* Learning

القيمة الافتراضية:

**15 seconds لكل حالة**

#### Max Age

المدة التي يحتفظ خلالها السويتش بمعلومات BPDU قبل اعتبارها قديمة.

القيمة الافتراضية:

**20 seconds**

***

## 8. Root Path Cost

الـ **Root Path Cost** يمثل تكلفة الوصول إلى Root Bridge.

كلما كانت تكلفة المسار أقل، كان المسار أفضل.

عند اختيار Root Port يتم أولًا البحث عن:

**Lowest Root Path Cost**

ثم عند التعادل ننتقل إلى المعايير التالية.

***

## 9. اختيار Root Port

ترتيب اختيار Root Port يكون كالتالي:

1. أقل **Root Path Cost**.
2. أقل **Sender Bridge ID**.
3. أقل **Sender Port ID**.
4. عند الحاجة يتم استخدام **Local Port ID** كعامل كسر للتعادل.

بمعنى مختصر:

**Lowest Cost → Lowest Bridge ID → Lowest Port ID**

***

## 10. اختيار المنفذ الذي سيتم وضعه في Blocking

إذا كان لدينا اتصالان بين Switches، فلا يمكن السماح لكلا المسارين بالـ Forwarding إذا كان ذلك سيؤدي إلى Loop.

لذلك تتم مقارنة المسارات.

بشكل عام:

**المسار ذو الـ Cost الأعلى يتم منعه عندما تتم مقارنة المسارين المتنافسين.**

وعند التعادل تتم المقارنة باستخدام:

1. Bridge Priority
2. MAC Address
3. Port ID

والهدف النهائي هو تحديد المسار الأفضل للـ Forwarding ووضع المسار الآخر في Blocking.

***

## 11. أوامر التحقق من STP

من أهم الأوامر المستخدمة للتحقق من STP:

```
show spanning-tree
```

يعرض معلومات مثل:

* Root Bridge
* Root Port
* Designated Ports
* Port State
* Root Cost
* Bridge ID
* معلومات VLAN

ولفحص منفذ محدد يمكن استخدام:

```
show spanning-tree interface <interface>
```

***

## 12. Topology Changes — TCN

عند حدوث تغيير في الشبكة، مثل:

* فصل Cable.
* توقف Interface.
* إعادة تشغيل Switch.
* انقطاع الطاقة.
* تغير حالة Port.

يقوم STP بالتعامل مع التغيير وإبلاغ بقية الشبكة.

السويتش الذي يكتشف التغيير يرسل **TCN BPDU** باتجاه Root Bridge.

بعد وصول التغيير إلى Root Bridge، يتم نشر المعلومات لبقية السويتشات.

***

## 13. MAC Address Table بعد حدوث تغيير

عند حدوث Topology Change، قد تصبح بعض معلومات MAC Address Table قديمة.

لذلك يتم تقليل مدة الاحتفاظ بالمعلومات القديمة، ثم يتم التخلص من الإدخالات التي لم تعد صالحة، بحيث تبدأ السويتشات في تعلم أماكن الأجهزة من جديد.

***

## 14. التعامل مع فشل الروابط

إذا فشل رابط موجود أصلًا في حالة Blocking، غالبًا لن يكون هناك تأثير مباشر على الـ User Traffic؛ لأن الرابط لم يكن يستخدم في Forwarding من الأساس.

أما إذا فشل رابط أساسي مثل Root Port، فيجب على STP البحث عن مسار بديل.

مثال مبسط:

```
Switch 1 ---- Switch 2
    \           /
     \         /
       Switch 3
```

إذا فشل الرابط الأساسي، يستطيع STP استخدام الرابط الاحتياطي بعد إعادة حساب الـ Topology.

***

## 15. Direct Failure

يحدث عندما يكتشف السويتش فشل الرابط المتصل به مباشرة.

مثال:

```
SW1 -------- SW3
```

إذا انقطع الرابط، يستطيع SW1 أو SW3 اكتشاف المشكلة مباشرة من خلال حالة الـ Interface.

بعدها يتم إرسال معلومات التغيير داخل الشبكة حتى يتم إعادة بناء الـ Topology.

***

## 16. Indirect Failure

يحدث عندما لا يكون الفشل في الرابط المتصل بالسويتش مباشرة، وإنما في مسار يعتمد عليه السويتش للوصول إلى Root Bridge.

مثال:

```
SW1 ---- SW2 ---- Root Bridge
```

إذا فقد SW2 اتصاله بالـ Root Bridge، فإن SW1 قد لا يعرف مباشرة أن المسار الأساسي أصبح غير صالح.

لذلك يعتمد STP على الـ BPDUs والـ Timers لاكتشاف المشكلة وإعادة حساب المسار.

***

## 17. RSTP

**RSTP — Rapid Spanning Tree Protocol**

هو تطوير لـ STP التقليدي، والهدف الأساسي منه هو:

**تقليل الوقت اللازم للتعافي من تغييرات الشبكة.**

المعيار:

**IEEE 802.1w**

RSTP أسرع من STP التقليدي لأنه يستخدم آلية مختلفة للمزامنة بين السويتشات، بالإضافة إلى تقليل حالات المنافذ.

***

## 18. حالات منافذ RSTP

RSTP يستخدم ثلاث حالات رئيسية:

#### Discarding

المنفذ لا يمرر User Traffic ولا يتعلم MAC Addresses.

#### Learning

المنفذ يتعلم MAC Addresses ولكنه لا يزال لا يمرر User Traffic.

#### Forwarding

المنفذ يمرر Traffic ويتعلم MAC Addresses.

***

## 19. Port Roles في RSTP

RSTP يحتوي على أدوار مثل:

#### Root Port

أفضل مسار باتجاه Root Bridge.

#### Designated Port

المنفذ المسؤول عن Forwarding في الـ Segment.

#### Alternate Port

مسار بديل إلى Root Bridge.

#### Backup Port

مسار احتياطي إضافي عندما توجد اتصالات متعددة على نفس الـ Segment.

***

## 20. Edge Port

الـ **Edge Port** يكون عادةً متصلًا بجهاز نهائي مثل:

* PC
* Server
* Printer

ولا يوجد خلفه Switch آخر يمكن أن يصنع Loop.

لذلك يمكن للمنفذ الانتقال بسرعة إلى Forwarding بدل انتظار مراحل STP التقليدية.

***

## 21. Point-to-Point

RSTP يستطيع تحديد أن الرابط **Point-to-Point** عندما يكون الاتصال بين سويتشين باستخدام Full-Duplex.

هذا يسمح لـ RSTP باستخدام آلية أسرع للمزامنة والانتقال بين حالات المنافذ.

***

## 22. RSTP Handshake

عند اتصال Switches ببعضها:

1. يتم التأكد من طبيعة الاتصال.
2. يتم تبادل BPDUs.
3. يتم الإعلان عن معلومات الـ Bridge.
4. يتم تحديد الـ Root Bridge باستخدام نفس منطق STP.
5. يتم تحديد المنافذ المناسبة.
6. تتم عملية Synchronization.
7. يتم نقل المنفذ إلى Forwarding عند تحقق الشروط.

***

## الخلاصة المهمة للاختبار

احفظ هذه النقاط:

**Root Bridge:**

> Lowest Bridge ID

**Bridge ID:**

> Priority + System ID Extension + MAC Address

**Root Port:**

> أفضل مسار للوصول إلى Root Bridge.

**Root Port Selection:**

> Lowest Root Path Cost → Lowest Bridge ID → Lowest Port ID

**STP Timers:**

> Hello = 2s\
> Forward Delay = 15s\
> Max Age = 20s

**STP States:**

> Blocking → Listening → Learning → Forwarding

**RSTP States:**

> Discarding → Learning → Forwarding

**RSTP Standard:**

> IEEE 802.1w

**الهدف الأساسي من STP/RSTP:**

> منع Layer 2 Loops مع توفير مسارات احتياطية عند حدوث Failure.
