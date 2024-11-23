Agar interfeys orqali VPN serverni sozlashni istasangiz, eng qulay vositalardan biri **OpenVPN Access Server** hisoblanadi. Bu OpenVPN serverining grafik interfeysini taqdim etadi va sozlash jarayonini sezilarli darajada osonlashtiradi. Quyida bosqichma-bosqich tushuntirib beraman:

---

### 1. **OpenVPN Access Serverni o‘rnatish**
   1. **Repositoryni qo‘shing**:
      ```bash
      wget https://as-repository.openvpn.net/as-repo.asc
      gpg --import as-repo.asc
      echo "deb http://as-repository.openvpn.net/as/debian bionic main" > /etc/apt/sources.list.d/openvpn-as-repo.list
      apt update
      ```

   2. **OpenVPN Access Serverni o‘rnatish**:
      ```bash
      apt install openvpn-as -y
      ```

---

### 2. **Administrator parolini sozlash**
   O‘rnatish jarayonidan so‘ng, admin foydalanuvchi uchun parolni o‘rnating:
   ```bash
   passwd openvpn
   ```

---

### 3. **Interfeysga kirish**
   1. Serveringizning IP-manzilini bilib oling:
      ```bash
      ip a
      ```
      Masalan, IP: `192.168.1.100`.

   2. Brauzeringizda quyidagi manzilni oching:
      ```
      https://<Server_IP>:943/admin
      ```
      Masalan: `https://192.168.1.100:943/admin`.

   3. Tizimga kirish uchun:
      - **Username**: `openvpn`
      - **Password**: yuqorida o‘rnatgan parolingiz.

---

### 4. **VPN serverni sozlash**
   Interfeys orqali kerakli sozlamalarni amalga oshiring:
   - **Server Settings** bo‘limida:
     - VPN protokolini (UDP yoki TCP) tanlang.
     - Tarmoq manzilini (`10.8.0.0/24`) va DNS sozlamalarini qo‘shing.

   - **User Management** bo‘limida:
     - Foydalanuvchilar qo‘shing va ularga VPN ulanish huquqini bering.

---

### 5. **Mijoz fayllarini yuklab olish**
   VPN mijozlari uchun ulanish fayllarini tayyorlash va ularga yuborish:
   - VPN interfeysida mijoz qurilmasi uchun `user.ovpn` faylini yarating.
   - Har bir foydalanuvchi interfeys orqali o‘z faylini yuklab olishi mumkin:
     ```
     https://<Server_IP>:943/
     ```

---

### 6. **Firewall va portlar**
   OpenVPN Access Server 943 (Admin UI), 443 (mijoz ulanishi) va 1194 (VPN ulanish) portlaridan foydalanadi. Bu portlarni ochib qo‘ying:
   ```bash
   ufw allow 943/tcp
   ufw allow 443/tcp
   ufw allow 1194/udp
   ufw enable
   ```

---

### 7. **Mijoz qurilmasida ulanish**
   1. Mijoz qurilmasida OpenVPN ilovasini o‘rnating:
      - Windows, macOS: [OpenVPN Connect](https://openvpn.net/client-connect-vpn-for-windows/)
      - Android, iOS: App Store yoki Play Store'dan **OpenVPN Connect** ilovasini yuklab oling.

   2. O‘zingizning `user.ovpn` faylingizni mijoz qurilmasiga import qiling va VPN ulanishini ishga tushiring.

---

Agar qaysidir bosqichda qiyinchilikka duch kelsangiz, qo‘shimcha savol berishdan tortinmang! 😊
