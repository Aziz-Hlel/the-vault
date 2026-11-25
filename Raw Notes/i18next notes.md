---
tags:
  - libraries/typescript/i18
  - ksi
  - frontend/react
---

## Interpolation

```json

"greeting": "Hello {{name}}",

"appointment": "Appointment with {{doctor}} at {{time}}",

"newMessage": "{{sender}} sent you a message",

"profileDone": "{{percent}}% of your profile is complete"

```

```ts

t('greeting', { name: 'Ahmed' })                          // Hello Ahmed

t('appointment', { doctor: 'Dr. Sara', time: '10:00' })   // Appointment with Dr. Sara at 10:00

t('newMessage', { sender: 'Omar' })                       // Omar sent you a message

t('profileDone', { percent: 80 })                         // 80% of your profile is complete

```
  
## Context

```json

"sender": "{{name}}",

"sender_DOCTOR": "Dr. {{name}}",

"sender_GUARDIAN": "{{name}} (Guardian)",

  

"welcome_MALE": "أهلًا بك يا {{name}}",

"welcome_FEMALE": "أهلًا بكِ يا {{name}}"

```
  
```ts

t('sender', { name: 'Ahmed', context: 'DOCTOR' })    // Dr. Ahmed

t('sender', { name: 'Ali', context: 'GUARDIAN' })    // Ali (Guardian)

t('sender', { name: 'Sara', context: 'PATIENT' })    // Sara  (no match → falls back to "sender")

t('welcome', { name: 'Mona', context: 'FEMALE' })    // أهلًا بكِ يا Mona

```

- Case-sensitive: `DOCTOR` ≠ `doctor`.

  

## Plurals (`count` is required)

```json

// en

"msg_one": "{{count}} message",

"msg_other": "{{count}} messages",

  

// ar: needs zero, one, two, few, many, other

"msg_zero": "لا توجد رسائل",

"msg_one": "رسالة واحدة",

"msg_two": "رسالتان",

"msg_few": "{{count}} رسائل",

"msg_many": "{{count}} رسالة",

"msg_other": "{{count}} رسالة"

```

  

```ts

t('msg', { count: 1 })    // en: 1 message    ar: رسالة واحدة

t('msg', { count: 2 })    // en: 2 messages   ar: رسالتان

t('msg', { count: 5 })    // en: 5 messages   ar: 5 رسائل    (few: 3–10)

t('msg', { count: 25 })   // en: 25 messages  ar: 25 رسالة   (many: 11–99)

```

  

With context too (`key_<context>_<plural>`):

  

```json

"patients_DOCTOR_one": "Dr. {{name}} has {{count}} patient",

"patients_DOCTOR_other": "Dr. {{name}} has {{count}} patients"

```

  

```ts

t('patients', { name: 'Ahmed', context: 'DOCTOR', count: 1 })   // Dr. Ahmed has 1 patient

t('patients', { name: 'Ahmed', context: 'DOCTOR', count: 12 })  // Dr. Ahmed has 12 patients

```

  

## Nesting

  

```json

"appName": "CareApp",

"roles": { "DOCTOR": "Doctor", "PATIENT": "Patient", "GUARDIAN": "Guardian" },

  

"welcome": "Welcome to $t(appName)",

"joinedAs": "You joined as $t(roles.{{role}})",

"footer": "© 2026 $t(appName)",

"inviteTitle": "Join $t(appName) as a $t(roles.{{role}})"

```

  

```ts

t('welcome')                           // Welcome to CareApp

t('joinedAs', { role: 'DOCTOR' })      // You joined as Doctor

t('footer')                            // © 2026 CareApp

t('inviteTitle', { role: 'GUARDIAN' }) // Join CareApp as a Guardian

```

  

## Formatting

  

```json

"date": "{{d, datetime(dateStyle: medium)}}",

"price": "{{n, currency(USD)}}",

"due": "{{n, relativetime(day)}}",

"names": "{{arr, list}}"

```

  

```ts

t('date', { d: new Date('2026-12-01') })   // en: Dec 1, 2026      ar: ١ ديسمبر ٢٠٢٦

t('price', { n: 1500 })                    // en: $1,500.00

t('due', { n: 3 })                         // en: in 3 days        ar: خلال ٣ أيام

t('due', { n: -1 })                        // en: 1 day ago

t('names', { arr: ['Ahmed', 'Sara', 'Omar'] })  // en: Ahmed, Sara, and Omar   ar: Ahmed وSara وOmar

```

  

- Arabic may output `١٢٣`. Use locale `ar-u-nu-latn` to get `123`.

  

## Keys with dots

  

- `.` means nesting. A flat `"chat.newMessage": {...}` is **not found**.

- Nest it instead:

  

```json

{

  "chat": { "newMessage": { "title": "New message" } },

  "appointment": { "reminder": { "title": "Appointment soon" } },

  "guardianship": { "request": { "title": "New guardian request" } }

}

```

  

```ts

t('chat.newMessage.title')           // New message

t('appointment.reminder.title')      // Appointment soon

t('guardianship.request.title')      // New guardian request

t(`${kind}.title`)                   // kind = "chat.newMessage" → New message

```