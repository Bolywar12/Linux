Ubuntu Serverda qurilmani (kompyuter yoki virtual mashinani) xavfsiz va to'g'ri tarzda o'chirish uchun quyidagi usullarni ishlatishingiz mumkin:

---

### **1. `shutdown` buyrug'i yordamida:**
```bash
sudo shutdown now
```
Bu buyruq tizimni darhol o'chiradi.

Agar ma'lum bir vaqtdan keyin o'chirmoqchi bo'lsangiz:
```bash
sudo shutdown +5
```
Bu buyruq tizimni 5 daqiqadan keyin o'chiradi.

Agar bekor qilish kerak bo'lsa:
```bash
sudo shutdown -c
```

---

### **2. `poweroff` buyrug'i yordamida:**
```bash
sudo poweroff
```
Bu tizimni to'g'ridan-to'g'ri o'chirish uchun ishlatiladi.

---

### **3. `halt` buyrug'i yordamida:**
```bash
sudo halt
```
Bu buyruq ham tizimni to'xtatadi va o'chiradi.

---

### **4. `reboot` orqali o'chirish (agar qayta yoqish kerak bo'lmasa):**
Agar qayta yoqish o'rniga tizimni to'xtatib o'chirishni istasangiz:
```bash
sudo reboot --poweroff
```

---

### Eslatma:
- Har doim tizim o'chirilishidan oldin barcha muhim ma'lumotlarni saqlang va ishlayotgan jarayonlarni tugatishga ishonch hosil qiling.
- Agar tizimni uzoqdan boshqarayotgan bo'lsangiz (masalan, SSH orqali), tizim o'chirilganidan keyin unga kirish imkoniyati bo'lmaydi.
