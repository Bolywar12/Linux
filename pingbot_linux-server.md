Telegram botni Python yordamida yaratib, uni tizimda avtomatik ishga tushirish uchun `systemd` xizmati sifatida sozlash jarayonini batafsil ko'rib chiqamiz. Quyida keltirilgan barcha bosqichlarni ketma-ket bajaring:

---

### 1. **Kerakli dasturlarni o'rnatish**
Terminalda quyidagi buyruqlarni bajaring:

```bash
sudo apt update
sudo apt install python3 python3-pip -y
sudo apt install python3.12-venv -y
```
Bu buyruqlar Python 3 va `venv` (virtual muhit yaratish uchun kutubxona) o'rnatadi.

---

### 2. **Virtual muhit yaratish**
Virtual muhit Python kutubxonalarini izolyatsiya qilish uchun ishlatiladi. Quyidagi buyruqlarni kiriting:

```bash
python3.12 -m venv venv
source venv/bin/activate
```
Bu buyruqlar `venv` nomli virtual muhit yaratadi va uni faollashtiradi.

---

### 3. **Kerakli kutubxonalarni o'rnatish**
Bot uchun kerakli kutubxonalarni o'rnating:

```bash
pip install aiogram
```
Bu buyruq `aiogram` kutubxonasini o'rnatadi, u Telegram botlarini yaratish uchun ishlatiladi.

---

### 4. **Botni sinab ko'rish**
Bot faylingiz (`pingbot6.py`) mavjud bo'lsa, uni virtual muhit ichida sinab ko'ring:

```bash
python3 pingbot6.py
```

Agar hech qanday xato bo'lmasa, botingiz to'g'ri ishlayotgan bo'ladi.

---

### 5. **`systemd` xizmati faylini yaratish**
`systemd` orqali botni avtomatik ishga tushirish uchun xizmat faylini yarating. 

Faylni oching:
```bash
sudo nano /etc/systemd/system/pingbot.service
```

Quyidagi tarkibni faylga joylashtiring:

```
[Unit]
Description=Telegram bot
After=network.target

[Service]
Type=simple
WorkingDirectory=/home/bolywar/
ExecStart=/home/bolywar/venv/bin/python3 /home/bolywar/pingbot6.py
Restart=on-failure
RestartSec=5
User=bolywar

[Install]
WantedBy=multi-user.target
```

**Eslatma**: `WorkingDirectory` va `ExecStart` yo'llarini mos ravishda o'zgartiring. Ular botingiz joylashgan katalogga mos kelishi kerak.

---

### 6. **`systemd` xizmati sozlamalarini yangilash**
`systemd`ni qayta yuklang va xizmatni faollashtiring:

```bash
sudo systemctl daemon-reload
sudo systemctl enable pingbot.service
sudo systemctl start pingbot.service
```

---

### 7. **Xizmat holatini tekshirish**
Xizmat ishlayotganligini tekshirish uchun quyidagi buyruqni bajaring:

```bash
sudo systemctl status pingbot.service
```

Agar xizmat ishlayotgan bo'lsa, yashil rangli `active (running)` holati ko'rinadi. Agar xatolik yuzaga kelsa, quyidagi buyruq bilan batafsil ma'lumotni ko'rishingiz mumkin:

```bash
journalctl -u pingbot.service
```

---

### 8. **Avtomatik ishga tushirishni ta'minlash**
`systemd` orqali xizmat tizim yoqilganda avtomatik ishga tushadi. Shu bilan botingiz doimiy ishlashini ta'minlaysiz.

---

### Qo'shimcha maslahatlar
- **Koddagi muammolarni tekshirish**: Python skriptni ishga tushirishdan oldin barcha bog‘liq kutubxonalar o‘rnatilganligiga va kodda xato yo‘qligiga ishonch hosil qiling.
- **Xavfsizlik**: API kalitlarini va boshqa maxfiy ma'lumotlarni kod ichida saqlash o'rniga `.env` fayliga joylashtiring va `python-dotenv` kutubxonasidan foydalaning.
  
Bu jarayonni to'liq amalga oshirish orqali botingiz muvaffaqiyatli ishlashi kerak. Agar muammo yuzaga kelsa, xabar bering, yordam beraman!

---

## Qo'shimcha ravishda 2ta service qo'shildi

Agar botingiz virtual muhit (`venv`) ichida ishlayotgan bo'lsa, bu juda yaxshi, chunki u Python kutubxonalarini izolyatsiya qilish imkonini beradi. Ammo `venv` dan foydalanayotganingizda, `systemd` xizmat konfiguratsiyasini to'g'ri sozlash muhimdir.

Quyida botni `venv` muhitida ishlatish uchun to'liq tavsiyalar va xizmat konfiguratsiyasi berilgan.

---

### **1. `venv` sozlamalari**
Bot dasturingiz uchun `venv` yaratish va kutubxonalarni o'rnatishni eslatib o'taman:

```bash
cd /home/bolywar/pingbot/
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

- `pingbot6.py` faylingiz joylashgan katalog: `/home/bolywar/pingbot/`
- Virtual muhit direktoriyasi: `/home/bolywar/pingbot/venv/`

---

### **2. `systemd` Xizmat Fayli**
Bu xizmat botni `venv` muhitida ishga tushirish uchun sozlangan:

Fayl: `/etc/systemd/system/telegram-bot.service`

```ini
[Unit]
Description=Telegram Bot Service
After=network.target

[Service]
Type=simple
# Ishlaydigan katalogni ko'rsatamiz
WorkingDirectory=/home/bolywar/pingbot/
# `venv` muhitida botni ishga tushiramiz
ExecStart=/home/bolywar/pingbot/venv/bin/python3 /home/bolywar/pingbot/pingbot6.py
# Xatolik bo'lsa, qayta ishga tushadi
Restart=on-failure
RestartSec=5
# Xizmatni qaysi foydalanuvchi ishga tushirishi
User=bolywar

[Install]
WantedBy=multi-user.target
```

---

### **3. Yoqilish va O'chish Hodisalari Xizmatlari**
Yuqorida berilgan xizmat fayllarini `venv` bilan moslashtiramiz:

#### Yoqilish Xizmati
Fayl: `/etc/systemd/system/startup-event.service`

```ini
[Unit]
Description=Server yoqilishida Telegram botga hodisa yuborish
After=network.target

[Service]
Type=oneshot
WorkingDirectory=/home/bolywar/pingbot/
ExecStart=/home/bolywar/pingbot/venv/bin/python3 /home/bolywar/pingbot/pingbot6.py --log-event "Server yoqildi"

[Install]
WantedBy=multi-user.target
```

#### O'chish Xizmati
Fayl: `/etc/systemd/system/shutdown-event.service`

```ini
[Unit]
Description=Server o'chirilayotganda Telegram botga hodisa yuborish
DefaultDependencies=no
Before=shutdown.target

[Service]
Type=oneshot
WorkingDirectory=/home/bolywar/pingbot/
ExecStart=/home/bolywar/pingbot/venv/bin/python3 /home/bolywar/pingbot/pingbot6.py --log-event "Server o‘chirildi"

[Install]
WantedBy=halt.target reboot.target
```

---

### **4. Xizmatlarni Yoqish**
Yuqoridagi fayllarni yaratgandan so'ng, quyidagi buyruqlar bilan xizmatlarni yoqing va ishga tushiring:

```bash
sudo systemctl daemon-reload
sudo systemctl enable telegram-bot.service
sudo systemctl enable startup-event.service
sudo systemctl enable shutdown-event.service

sudo systemctl start telegram-bot.service
```

---

### **5. Tekshirish**
Xizmatlarning holatini tekshirish uchun:

- **Telegram bot xizmati**:
  ```bash
  sudo systemctl status telegram-bot.service
  ```

- **Yoqilish hodisasi xizmati**:
  ```bash
  sudo systemctl status startup-event.service
  ```

- **O'chish hodisasi xizmati**:
  ```bash
  sudo systemctl status shutdown-event.service
  ```

---

### **6. Maslahat**
1. **Loglarni kuzatish**: Agar xizmatlar ishlamasa, loglarni tekshirish uchun quyidagi buyruqdan foydalaning:
   ```bash
   sudo journalctl -u telegram-bot.service
   sudo journalctl -u startup-event.service
   sudo journalctl -u shutdown-event.service
   ```

2. **Virtul muhitga kirish**: Xizmat ishlashi uchun virtual muhit (`venv`) to'g'ri ishlashiga ishonch hosil qiling:
   ```bash
   source /home/bolywar/pingbot/venv/bin/activate
   python /home/bolywar/pingbot/pingbot6.py
   ```

---

Ushbu sozlamalar botni `venv` muhitida ishlatishga to'liq tayyor qiladi. Agar qo'shimcha savollaringiz bo'lsa, bemalol so'rashingiz mumkin!
