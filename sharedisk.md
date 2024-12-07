Bu xatolik Samba orqali ulashgan papkangizda ruxsat masalasi tufayli yuzaga kelgan. Windows tarmoq orqali papkaga ulana olishi uchun o'sha papka va uning tarkibidagi fayllar uchun kerakli ruxsatlarni sozlash kerak. Quyida bu muammoni hal qilish uchun bosqichma-bosqich ko'rsatmalar berilgan:

---

### 1. **Papka ruxsatlarini to'g'rilash**
Sizning `lost+found` katalogingiz faqat root foydalanuvchisi tomonidan foydalaniladigan tizim katalogi. Agar `/mnt/shared` papkangizga kirish ruxsatini barcha foydalanuvchilar uchun ochmoqchi bo'lsangiz, quyidagilarni bajaring:

1. Ruxsatlarni o'zgartiring:
   ```bash
   sudo chmod -R 777 /mnt/shared
   ```
   Bu barcha foydalanuvchilarga yozish, o'qish va bajarish imkonini beradi. Agar xavfsizlikni yaxshiroq ta'minlash kerak bo'lsa, ruxsatlarni faqat kerakli darajada o'rnating.

2. Papka egasini `nobody` va `nogroup` ga o'zgartiring (Samba xizmatidan foydalanish uchun qulay):
   ```bash
   sudo chown -R nobody:nogroup /mnt/shared
   ```

---

### 2. **Samba konfiguratsiyasini tekshirish**
`/etc/samba/smb.conf` faylidagi tarmoq papkasi konfiguratsiyasini qayta tekshiring. Fayl oxirida quyidagilarni qo'shganingizni tasdiqlang:

```ini
[SharedDisk]
path = /mnt/shared
browsable = yes
writable = yes
guest ok = yes
force user = nobody
force group = nogroup
create mask = 0777
directory mask = 0777
```

Keyin Samba xizmatini qayta ishga tushiring:
```bash
sudo systemctl restart smbd
```

---

### 3. **Windows ulanishini qayta sinab ko'ring**
Windows tizimingizdan ulanishni qayta sinab ko'ring:
1. **Windows Explorer**ga kiring va quyidagi yo'lni kiriting:
   ```plaintext
   \\192.168.1.174\SharedDisk
   ```
   Bu yerda `192.168.1.174` — sizning serveringizning IP-manzili.

2. Agar hamon ulana olmasangiz, quyidagi buyruqni sinab ko'ring:
   ```plaintext
   net use Z: \\192.168.1.174\SharedDisk /user:guest
   ```
   Bu `Z:` harfini tarmoq diskiga ulaydi.

---

### 4. **Muammo davom etsa**
Agar yuqoridagi amallar yordam bermasa:
1. Samba log-fayllarini ko'rib chiqing:
   ```bash
   sudo tail -f /var/log/samba/log.*
   ```
   Bu loglar muammo sababini ko'rsatadi.

2. Windows keshini tozalang va ulanishni qayta sinab ko'ring:
   ```plaintext
   net use * /delete
   ```

---
Ha, Samba konfiguratsiyasi orqali faqat ma'lum foydalanuvchilarga ruxsat berish yoki tarmoq diski ulanishini parol bilan himoya qilish mumkin. Quyida bunga qanday erishish mumkinligi haqida bosqichma-bosqich ko'rsatmalar berilgan:

---

### 1. **Yangi foydalanuvchi yaratish (Samba uchun)**

1. Linux tizimida yangi foydalanuvchi yarating (agar u hali mavjud bo'lmasa):
   ```bash
   sudo adduser sambauser
   ```
   Bu yerda `sambauser` — Samba uchun foydalaniladigan foydalanuvchi nomi. Parolni kiritishingizni so'raydi, uni eslab qoling.

2. Yangi foydalanuvchini Samba foydalanuvchilari ro'yxatiga qo'shing:
   ```bash
   sudo smbpasswd -a sambauser
   ```
   Bu Samba uchun alohida parol belgilaydi.

---

### 2. **Papka ruxsatlarini sozlash**

1. Faqat yangi foydalanuvchi foydalanishi mumkin bo'lgan papka egasini o'zgartiring:
   ```bash
   sudo chown -R sambauser:sambauser /mnt/shared
   ```

2. Ruxsatlarni cheklang (faqat o'qish va yozish imkoniyatini belgilang):
   ```bash
   sudo chmod -R 770 /mnt/shared
   ```

---

### 3. **Samba konfiguratsiyasini sozlash**

1. Samba konfiguratsiya faylini oching:
   ```bash
   sudo nano /etc/samba/smb.conf
   ```

2. Tarmoq papkasi konfiguratsiyasini o'zgartiring yoki yangisini qo'shing:
   ```ini
   [SharedDisk]
   path = /mnt/shared
   browsable = yes
   writable = yes
   valid users = sambauser
   create mask = 0770
   directory mask = 0770
   force user = sambauser
   force group = sambauser
   ```
   Bu yerda:
   - **`valid users = sambauser`** — faqat `sambauser` foydalanuvchisi ulanishi mumkin.
   - **`force user` va `force group`** — fayllar `sambauser` nomidan yaratiladi.

3. O'zgarishlarni saqlang va chiqib keting.

4. Samba xizmatini qayta ishga tushiring:
   ```bash
   sudo systemctl restart smbd
   ```

---

### 4. **Windows foydalanuvchisi sifatida ulanish**

1. Windows'da Explorer oynasida quyidagi manzilni kiriting:
   ```plaintext
   \\192.168.1.174\SharedDisk
   ```

2. Kirish uchun foydalanuvchi va parolni so'raydi:
   - **Username**: `sambauser`
   - **Password**: Siz Samba uchun yaratgan parol.

---

### 5. **Bir nechta foydalanuvchiga ruxsat berish (ixtiyoriy)**

Agar bir nechta foydalanuvchi ulanishi kerak bo'lsa:
1. Yangi foydalanuvchi yarating va uni Samba'ga qo'shing:
   ```bash
   sudo adduser newuser
   sudo smbpasswd -a newuser
   ```

2. Samba konfiguratsiyasida `valid users` qatoriga foydalanuvchilarni vergul bilan ajratib yozing:
   ```ini
   valid users = sambauser, newuser
   ```

---

Agar yana qo'shimcha yordam kerak bo'lsa, bemalol so'rang! 😊


