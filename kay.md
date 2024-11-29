Agar sizda **Python 3.12** allaqachon o'rnatilgan bo'lsa va uni saqlab qolishni xohlasangiz, siz Python 3.10 yoki 3.11-ni qo'shimcha sifatida o'rnatishingiz va ulardan mos ravishda foydalanishingiz mumkin. Quyida Python versiyalarini yo'qotmasdan bir necha versiyani birgalikda ishlatish bo'yicha qadamlar keltirilgan:

---

### 1. **Omborlarni yangilash va "deadsnakes" qo'shish**
Bu sizga yangi versiyalarni tizimingizda mavjud Python bilan birga o'rnatish imkonini beradi:

```bash
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.11 python3.11-venv
```

### 2. **Har bir versiyani ishlatish**
O'rnatgandan so'ng, har bir versiyaga alohida murojaat qilishingiz mumkin:
```bash
python3.12 --version
python3.11 --version
```

Agar `python3.11` ishlayotgan bo'lsa, virtual muhitni yaratishda uni tanlashingiz mumkin:
```bash
python3.11 -m venv myenv
```

---

### 3. **Manbadan o'rnatish**
Agar tizim omborida Python 3.11 yoki 3.10 topilmasa, ularni manbadan o'rnatsangiz, mavjud versiyalarga zarar yetkazmaydi. Quyidagi qadamlarni bajaring:

#### A. Manba kodini yuklash:
```bash
wget https://www.python.org/ftp/python/3.11.7/Python-3.11.7.tgz
tar -xvzf Python-3.11.7.tgz
cd Python-3.11.7
```

#### B. Qurish va o'rnatish:
```bash
./configure --enable-optimizations
make
sudo make altinstall
```

Bu bilan Python 3.11 `python3.11` deb chaqiriladi va tizimdagi boshqa Python versiyalariga ta'sir qilmaydi.

---

### 4. **Versiyalarni boshqarish uchun `update-alternatives` foydalanish**
O'rnatilgan barcha versiyalarni boshqarish uchun `update-alternatives`dan foydalanishingiz mumkin:
```bash
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.12 1
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 2
```

Keyin o'zingiz xohlagan asosiy versiyani tanlashingiz mumkin:
```bash
sudo update-alternatives --config python3
```

---

### 5. **Virtual muhitdan foydalanish (Tavsiya etiladi)**
Python versiyalarini chalkashtirmaslik uchun virtual muhitlar yordamida ishlang:
```bash
python3.11 -m venv myenv
source myenv/bin/activate
```

Bu muhit ichida faqat tanlangan Python versiyasi ishlatiladi, tizimdagi boshqa versiyalarga zarar yetkazilmaydi.

---

Agar savollaringiz bo'lsa yoki qo'shimcha yordam kerak bo'lsa, yozib qoldiring! 😊
