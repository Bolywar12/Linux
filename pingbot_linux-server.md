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
