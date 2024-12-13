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

Sizning ushbu `systemd` xizmatingiz botni avtomatik ishga tushirish uchun ishlaydi. Uni serverning yoqilishi va o'chirilishi hodisalarini kuzatish uchun ishlatishingiz mumkin. Quyida bot xizmatiga qo'shimcha qilib, server yoqilish va o'chirish hodisalarini kuzatadigan xususiyatlarni birlashtirish haqida batafsil ko'rsatmalar berilgan.

---

### **1. Hozirgi Xizmatni O'zgarishsiz Qoldirish**
Sizning mavjud xizmat konfiguratsiyangiz botni avtomatik ishga tushirish uchun yetarli. Unda biror o'zgartirish qilish shart emas. Ammo yangi xizmatlar yordamida yoqilish va o'chirish hodisalarini qayd qilishni qo'shishimiz kerak.

---

### **2. Yoqilish va O'chirilish Xizmatlarini Yaratish**

#### **A. Yoqilish Xizmati**
Fayl: `/etc/systemd/system/startup-event.service`

```ini
[Unit]
Description=Server yoqilgan vaqtda Telegram botga hodisa yuborish
After=network.target

[Service]
Type=oneshot
ExecStart=/home/bolywar/pingbot/venv/bin/python3 /home/bolywar/pingbot/pingbot6.py --log-event "Server yoqildi"

[Install]
WantedBy=multi-user.target
```

#### **B. O'chish Xizmati**
Fayl: `/etc/systemd/system/shutdown-event.service`

```ini
[Unit]
Description=Server o'chirilayotgan paytda Telegram botga hodisa yuborish
DefaultDependencies=no
Before=shutdown.target

[Service]
Type=oneshot
ExecStart=/home/bolywar/pingbot/venv/bin/python3 /home/bolywar/pingbot/pingbot6.py --log-event "Server o‘chirildi"

[Install]
WantedBy=halt.target reboot.target
```

#### **C. Yangi Xizmatlarni Faollashtirish**
Xizmatlarni yoqing va ishga tushiring:
```bash
sudo systemctl enable startup-event.service
sudo systemctl enable shutdown-event.service
```

---

### **3. Bot Kodingizni Yangi Xizmatlarga Moslashtirish**

Botga quyidagi funksiyani qo'shish kerak:

#### **A. Argparse yordamida hodisani qo'shish**
```python
import argparse

def main():
    # Argumentlarni olish
    parser = argparse.ArgumentParser()
    parser.add_argument("--log-event", help="Server hodisasini qayd qilish")
    args = parser.parse_args()

    if args.log_event:
        # Hodisani qayd qilish
        log_event(args.log_event)
        return

    # Telegram botni ishga tushirish
    asyncio.run(dp.start_polling(bot))
```

#### **B. Ma'lumotlar bazasiga hodisani yozish funksiyasi**
Kodni boshida ma'lumotlar bazasi bilan ishlash qismi qo'shiladi:
```python
def log_event(event_type):
    conn = sqlite3.connect("server_events.db")
    cursor = conn.cursor()
    cursor.execute("INSERT INTO events (event_type, event_time) VALUES (?, datetime('now'))", (event_type,))
    conn.commit()
    conn.close()
```

---

### **4. Yangi Xizmatlarni Sinash**

1. Serverni qayta yoqing va hodisalar ma'lumotlar bazasiga yozilganini tekshiring:
   ```bash
   sqlite3 /home/bolywar/pingbot/server_events.db "SELECT * FROM events;"
   ```
2. Agar ma'lumotlar bazasida yozuvlar mavjud bo'lsa, bot orqali hodisalarni ko'rishingiz mumkin.

---

### **5. To'liq Sinov**
- `systemctl status` yordamida barcha xizmatlarning ishlash holatini tekshiring:
   ```bash
   sudo systemctl status telegram-bot.service
   sudo systemctl status startup-event.service
   sudo systemctl status shutdown-event.service
   ```

Bu sozlamalar sizning mavjud botingizni server hodisalari bilan birlashtiradi va avtomatik xabar yuborishni ta'minlaydi. Agar qo'shimcha yordam kerak bo'lsa, ayting!
