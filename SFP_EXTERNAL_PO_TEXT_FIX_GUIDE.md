# External PO – Clean Section-Based Design (Option B)

هذا المستند يوضح التنفيذ النظيف الذي طلبته: **كل قسم له Node مستقل** في الـ Interface والـ Layout، مع إخفاء تلقائي للقسم إذا كان المحتوى فارغ.

## 1) ما تم تطبيقه في Layout XML
تم تحويل منطقة النصوص من نموذج قديم (حقول عامة مثل `PO_HD_TEXT`/`DOC_REQ`) إلى أقسام مستقلة:

- `SPECIAL_TERMS`
- `HEALTH_ENV_REQ`
- `PRE_SHIPMENT_ADVICE`
- `DOC_REQUIRED_CLEARANCE`
- `SHIPMENT_ADVICE`
- `REMARKS`
- `BANK_DETAILS`

وكل قسم يحتوي:
1. عنوان ثابت (Label)
2. حقل Multi-line مربوط بـ Node خاص بالقسم
3. Script في `layout:ready` لإخفاء القسم إذا الحقل فارغ

## 2) Nodes المطلوبة في SFP Interface (HD)
تأكد أن Context في SFP (تحت `HD`) يحتوي نفس الأسماء التالية:

- `SPECIAL_TERMS`
- `HEALTH_ENV_REQ`
- `PRE_SHIPMENT_ADVICE`
- `DOC_REQUIRED_CLEARANCE`
- `SHIPMENT_ADVICE`
- `REMARKS`
- `BANK_DETAILS`
- (اختياري) `GENERAL_TERMS_NOTE`

> تم أيضًا إضافتها داخل data description في XML حتى يكون الربط واضحًا.

## 3) المطلوب في Driver ABAP
في البرنامج الرئيسي، عبّئ كل Node من مصدره (READ_TEXT أو أي مصدر آخر) بشكل مستقل:

- `HD-SPECIAL_TERMS`
- `HD-HEALTH_ENV_REQ`
- `HD-PRE_SHIPMENT_ADVICE`
- `HD-DOC_REQUIRED_CLEARANCE`
- `HD-SHIPMENT_ADVICE`
- `HD-REMARKS`
- `HD-BANK_DETAILS`

### ملاحظة
إذا كانت بنية DDIC (`ZMM_ST_EXTERNALPO`) لا تحتوي هذه الحقول بعد، أضفها أولًا في DDIC/Interface ثم فعّل.

## 4) سلوك الإخفاء التلقائي
كل Section Subform يحتوي JavaScript:
- إذا القيمة فارغة أو مسافات فقط → `presence = hidden`
- غير ذلك → `presence = visible`

وبذلك لا يظهر عنوان قسم بدون محتوى.

## 5) نتيجة التصميم
النتيجة الآن متوافقة مع طلبك:
- شكل أقسام منفصلة مثل الصورة
- كل قسم يعرض نصه فقط من مصدره
- لا يوجد حشر كل شيء داخل `DOC_REQ`
- دعم أفضل للـ multiline


## 6) Suggested Text IDs mapping in Driver
- `F07` -> `HD-SPECIAL_TERMS` (fallback from old `PO_HD_TEXT`)
- `F05` -> `HD-HEALTH_ENV_REQ`
- `F14` -> `HD-PRE_SHIPMENT_ADVICE`
- `F25` -> `HD-DOC_REQUIRED_CLEARANCE`
- `F26` -> `HD-SHIPMENT_ADVICE`
- `F27` -> `HD-REMARKS`
- `F28` -> `HD-BANK_DETAILS`

> If `F26/F27/F28` are not configured in your SAP text determination yet, the code still initializes section nodes and keeps them empty (thus section auto-hides).
