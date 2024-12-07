Sizning `lsblk` natijalaringizni tahlil qilib ko'ramiz. Hozirda disk quyidagicha ajratilgan:

- **`sda1`**: 1GB (EFI bo'limi, boot uchun ishlatiladi).
- **`sda2`**: 2GB (`/boot` bo'limi).
- **`sda3`**: 928.5GB (`lvm` sifatida ishlatilgan, faqat bir qismi – 100GB – Ubuntu tizimi uchun ajratilgan).

Sizning maqsadingiz – qolgan 828.5GB joyni Windows tarmoq diski sifatida ulash. Buning uchun quyidagilarni amalga oshirasiz:

---

### 1. **LVM bo'sh joyni ajratish**
LVM tizimidan foydalanilayotganligi sababli, qolgan joyni LVM'dan ajratib, alohida disk sifatida sozlashingiz kerak bo'ladi.

#### a) Bo'sh joyni tekshirish
Avvalo, LVM tarkibida bo'sh joy borligini tasdiqlang:
```bash
sudo lvdisplay
```

#### b) Bo'sh joyni yangi bo'limga ajratish
1. **Yangi Logical Volume (LV) yaratish**:
   ```bash
   sudo lvcreate -L 828G -n shared ubuntu-vg
   ```
   Bu yerda:
   - `-L 828G` – yangi LV uchun 828GB joy ajratiladi.
   - `-n shared` – yangi LV nomi `shared` deb belgilanadi.

2. **Fayl tizimini o'rnatish**:
   Yangi bo'limni `ext4` formatida sozlang:
   ```bash
   sudo mkfs.ext4 /dev/ubuntu-vg/shared
   ```

3. **Bo'limni ulash**:
   Yangi bo'limni ulash uchun katalog yarating va ulashing:
   ```bash
   sudo mkdir /mnt/shared
   sudo mount /dev/ubuntu-vg/shared /mnt/shared
   ```

4. **Avtomatik ulanishni sozlash**:
   `/etc/fstab` fayliga yozing:
   ```plaintext
   /dev/ubuntu-vg/shared /mnt/shared ext4 defaults 0 0
   ```

---

### 2. **Tarmoq bo'limini ulash (Windows uchun Samba)**
Diskni Windows tarmoq diski sifatida ulash uchun Samba xizmatidan foydalaning:

#### a) Sambani o'rnating
```bash
sudo apt update
sudo apt install samba
```

#### b) Samba sozlamalari
1. Samba konfiguratsiya faylini oching:
   ```bash
   sudo nano /etc/samba/smb.conf
   ```

2. Fayl oxiriga quyidagilarni qo'shing:
   ```ini
   [SharedDisk]
   path = /mnt/shared
   browsable = yes
   writable = yes
   guest ok = yes
   create mask = 0755
   ```

3. Samba xizmatini qayta ishga tushiring:
   ```bash
   sudo systemctl restart smbd
   ```

---

### 3. **Windows'dan ulanish**
1. Windows tizimida "This PC"ni oching va "Map Network Drive" tugmasini bosing.
2. Tarmoq yo'lini kiriting:
   ```plaintext
   \\<server-ip>\SharedDisk
   ```
   Bu yerda `<server-ip>` – serveringizning IP manzili.

---

Agar qo'shimcha savollaringiz bo'lsa, yozib qoldiring! 😊

---


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

Ha, 700GB bo'limni qayta nomlash yoki to'g'ri ulash mumkin. Bundan tashqari, hozirgi bo'lim konfiguratsiyasini to'g'ri o'rnatish uchun kerakli sozlashlarni bajarishingiz mumkin. Quyidagi bosqichlar bilan davom eting:

---

### 1. **700GB bo'limni qayta ulash va qayta nomlash**
Bo'limni to'g'ri ulash va uning yangi nomini belgilash uchun quyidagilarni bajaring:

#### Bo'limni ajratish
Avval noto'g'ri ulangan bo'limni ajrating:
```bash
sudo umount /mnt/shared
```

#### Yangi ulash nuqtasini yaratish
700GB bo'lim uchun yangi ulash nuqtasi yarating:
```bash
sudo mkdir /mnt/shared700
```

#### Bo'limni yangi ulash nuqtasiga ulash
700GB bo'limni yangi ulash joyiga ulang:
```bash
sudo mount /dev/mapper/ubuntu--vg-shared /mnt/shared700
```

Bo'limni ulash muvaffaqiyatli bo'lganligini tekshirish uchun:
```bash
df -h
```
Bu sizga `/mnt/shared700` joyida 700GB hajmni ko'rsatadi.

#### Avtomatik ulashni sozlash
Bo'limni har safar tizim qayta yuklanganda avtomatik ulash uchun `/etc/fstab` faylini tahrirlang:
```bash
sudo nano /etc/fstab
```

Fayl oxiriga quyidagilarni qo'shing:
```
/dev/mapper/ubuntu--vg-shared /mnt/shared700 ext4 defaults 0 2
```

So'ngra, o'zgarishlarni saqlash va chiqish uchun `Ctrl+O` va `Ctrl+X` tugmalarini bosing. Yangi sozlamani qo'llash uchun:
```bash
sudo mount -a
```

---

### 2. **100GB bo'limni Windows uchun tarmoq ulashiga sozlash**
Ehtimol siz noto'g'ri `100GB` bo'limni ulagan bo'lishingiz mumkin (`/dev/mapper/ubuntu--vg-ubuntu--lv`). Bu bo'limning tarmoq ulanishini uzish va 700GB bo'limni ulash uchun Samba sozlamalarini yangilang:

#### Samba sozlamasini yangilash
`/etc/samba/smb.conf` faylini tahrirlang:
```bash
sudo nano /etc/samba/smb.conf
```

`[SharedDisk]` bo'limida `path` o'zgaruvchisini to'g'ri yo'lga o'zgartiring:
```ini
[SharedDisk]
path = /mnt/shared700
browsable = yes
read only = no
guest ok = yes
force user = nobody
create mask = 0777
directory mask = 0777
```

So'ngra Samba xizmatini qayta ishga tushiring:
```bash
sudo systemctl restart smbd
```

---

### 3. **Windows ulanishini yangilash**
Windows kompyuterda avvalgi tarmoq diskini uzing va yangi 700GB bo'limga ulaning:
1. Tarmoq diskini uzing.
2. Yangi manzilni kiriting:  
   ```
   \\<Ubuntu_server_IP>\SharedDisk
   ```

Endi 700GB hajmdagi bo'limni ko'rishingiz kerak.

---

Agar shunday qilganingizdan keyin ham noto'g'ri hajm aks etsa, qaysi bo'limni ulayotganingizni yana bir marta tekshiring. Har qanday muammolarga duch kelsangiz, xabar bering!


