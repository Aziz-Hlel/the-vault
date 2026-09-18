---
tags:
  - how-to
  - cloud/zoho/mail
  - tutorial
---

---

- *NOTE:* this note is not official (that's why it's in the To Be Continued folder ) because these are recollection after the fact, and i didn't test it out yet
- video reference : https://www.youtube.com/watch?v=ooCaJDoKfKY
## Enable IMAP Access

- login to zoho mail account from the web from https://accounts.zoho.com/signin
-  go to `settings` -> `Mail account` -> and check **IMAP Access**

## Generate App Passwords

-   *info* : Application-Specific Passwords is the password you'd use to login to you zoho account in 3rd-party apps such as Outlook
- login to the zoho settings account from https://accounts.zoho.com/
- Generate a new password under `security`  -> `App Passwords` 

## Set up Zoho Mail in Outlook

- Open up outlook , type in you email and choose `Advanced options`, do not type in the password yet
- Select `IMAP` as your email provider
- Paste the **App Password**
- below is the **IMAP Server Configuration**, you should input these values into outlook, can't tell you where exactly since Outlook change this shit every time in every version :

| Type | Server/ Host     | Port | Mode |
| ---- | ---------------- | ---- | ---- |
| In   | imappro.zoho.com | 993  | SSL  |
| Out  | smtppro.zoho.com | 465  | SSL  |
- At some point Outlook will prompt you to type another password, and that's the password to login locally from the outlook app, it has nothing to the with the app-password