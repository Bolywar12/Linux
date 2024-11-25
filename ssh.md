Ubuntu 24.04 serverida SSH (Secure Shell) xizmatini o‘rnatish va sozlash uchun quyidagi qadamlarni bajaring. SSH Ubuntu serveriga masofaviy ulanishni ta'minlaydi va uni o‘rnatish juda oddiy.

---

### 1. **SSH o‘rnatilganligini tekshirish**
Ubuntu serverining so'nggi versiyalarida SSH oldindan o‘rnatilgan bo‘lishi mumkin. Tekshirish uchun quyidagi buyruqni bajaring:

```bash
sudo systemctl status ssh
```

Agar "active (running)" holati ko‘rsatilsa, SSH allaqachon o‘rnatilgan va ishlamoqda. Aks holda davom eting.

---

### 2. **SSH serverni o‘rnatish**
Agar SSH o‘rnatilmagan bo‘lsa, `openssh-server` paketini o‘rnating:

```bash
sudo apt update
sudo apt install openssh-server -y
```

---

### 3. **SSH xizmatini yoqish va ishga tushirish**
O‘rnatgandan so‘ng, SSH xizmatini yoqing va ishga tushiring:

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

Xizmatning holatini yana bir bor tekshiring:

```bash
sudo systemctl status ssh
```

---

### 4. **SSH uchun portni sozlash**
Standart bo‘yicha SSH 22-portda ishlaydi. Agar portni o‘zgartirishni istasangiz, konfiguratsiya faylini tahrirlash kerak:

```bash
sudo nano /etc/ssh/sshd_config
```

Faylda quyidagi qatorni toping va kerakli portni belgilang:

```
Port 22
```

Keyin, konfiguratsiyani yangilash uchun SSH xizmatini qayta yuklang:

```bash
sudo systemctl restart ssh
```

---

### 5. **Ulanishni sinab ko‘rish**
Masofaviy kompyuterdan yoki boshqa qurilmadan serveringizga ulaning:

```bash
ssh username@server_ip_address
```

**Misol:**

```bash
ssh user@192.168.1.100
```

---

### 6. **Firewall sozlamalari (agar kerak bo‘lsa)**
Agar Ubuntu serveringizda `ufw` firewall yoqilgan bo‘lsa, SSH ulanishlariga ruxsat bering:

```bash
sudo ufw allow ssh
```

Yoki boshqa port ishlatilayotgan bo‘lsa:

```bash
sudo ufw allow <port_number>/tcp
```

---

Shundan so‘ng, SSH orqali serveringizga muvaffaqiyatli ulanishingiz kerak. 
