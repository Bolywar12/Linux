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

---

To completely uninstall OpenVPN Access Server from your server, you can follow these steps. Please note that these instructions are for a typical Linux server (like Ubuntu, CentOS, or Debian) where OpenVPN Access Server is installed. 

### Step 1: Stop the OpenVPN Access Server Services
First, stop the OpenVPN services to ensure they’re not running during uninstallation.

```bash
sudo systemctl stop openvpnas
```

Or, if your system uses a different init system:

```bash
sudo service openvpnas stop
```

### Step 2: Uninstall OpenVPN Access Server
To remove the OpenVPN Access Server package, use the appropriate package manager for your operating system:

- **For Ubuntu/Debian**:

  ```bash
  sudo apt-get remove --purge openvpn-as
  ```

- **For CentOS/RHEL**:

  ```bash
  sudo yum remove openvpn-as
  ```

This command will uninstall the OpenVPN Access Server package. The `--purge` option on Ubuntu/Debian ensures that all configuration files associated with the package are removed.

### Step 3: Remove Configuration and Data Files
After uninstalling the package, there might still be residual configuration files and directories on your server. To ensure a complete cleanup, manually delete these files:

```bash
sudo rm -rf /usr/local/openvpn_as
sudo rm -rf /var/lib/openvpn
sudo rm -rf /etc/openvpn
sudo rm -rf /var/log/openvpnas
```

These commands remove the main OpenVPN Access Server directories and logs. **Be cautious** when running these commands, as they permanently delete these directories and files.

### Step 4: Remove Users (Optional)
If you had created specific users for OpenVPN Access Server, you might want to delete those users as well:

```bash
sudo userdel openvpn
```

### Step 5: Verify Removal
Check that the OpenVPN Access Server is no longer listed in running services:

```bash
sudo systemctl status openvpnas
```

If the output indicates that the service is not found, then the removal was successful.

### Step 6: Reboot the Server (Optional)
To ensure all services and settings are reset, you might want to reboot the server:

```bash
sudo reboot
```

### Summary
These steps should completely remove OpenVPN Access Server from your machine. If you plan to reinstall or switch to a different VPN solution, your server should now be clean and ready for a new setup.
