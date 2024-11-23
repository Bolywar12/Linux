Sizning tizimingizda `openvpn-as` paketi omborda mavjud emas yoki noto'g'ri manbaga murojaat qilinmoqda. `OpenVPN Access Server` o'zining rasmiy manbalaridan o'rnatilishi kerak. Quyidagi amallarni bajarib ko'ring:

---

### 1. **Kerakli omborlarni qo'shish**

`openvpn-as` paketini o'rnatish uchun OpenVPN rasmiy omborlarini tizimga qo'shish kerak:

#### Omborni qo'shish:
```bash
wget -O - https://as-repository.openvpn.net/as-repo-public.gpg | sudo gpg --dearmor -o /usr/share/keyrings/openvpn-as-repo.gpg
echo "deb [signed-by=/usr/share/keyrings/openvpn-as-repo.gpg] https://as-repository.openvpn.net/as/debian noble main" | sudo tee /etc/apt/sources.list.d/openvpn-as-repo.list
```

---

### 2. **Tizimni yangilash va o'rnatish**
Yuqoridagi qadamlarni bajargandan so'ng, tizim omborlarini yangilang va OpenVPN Access Serverni o'rnating:
```bash
sudo apt update
sudo apt install openvpn-as
```

---

### 3. **OpenVPN Access Server xizmatini ishga tushirish**
O'rnatish tugagach, xizmatni boshlang va holatini tekshiring:
```bash
sudo systemctl start openvpnas
sudo systemctl status openvpnas
```

---

### 4. **Admin interfeysga kirish**
Odatda OpenVPN Access Server o'rnatilgandan keyin admin interfeys quyidagi manzilda bo'ladi:
```
https://<server-ip>:943/admin
```
Shuningdek, foydalanuvchilar interfeysi:
```
https://<server-ip>:943
```

Agar yuqoridagi amallarni bajargandan so'ng ham muammo hal bo'lmasa, OpenVPN Access Server konfiguratsiyasini qayta sozlash uchun quyidagi buyrug'ni ishlating:
```bash
sudo /usr/local/openvpn_as/scripts/sacli ResetToDefault all
```

Bu o'rnatilgan serverni standart sozlamalarga qaytaradi.

---

Agar boshqa muammo yoki xato paydo bo'lsa, shu yerdan loglarni ko'rsatishingiz mumkin. Yordamni davom ettiraman!
