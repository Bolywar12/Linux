### Netplan yordamida IP manzilni o‘zgartirish hozirgi zamonaviy Linux tizimlarida eng samarali usullardan biri bo‘lib, u statik yoki dinamik IP-manzillarni boshqarish uchun foydalaniladi. Bu usulni qo‘llashda quyidagi qadamlarni bajarishingiz kerak:

Qadamlar
1. netplan konfiguratsiya faylini tahrirlash
Netplan konfiguratsiya fayllari odatda /etc/netplan/ papkasida joylashgan bo‘ladi. Bu papkada bir yoki bir nechta .yaml kengaytmali fayl bo‘ladi, masalan: 01-netcfg.yaml. Ushbu faylni tahrirlash uchun quyidagi buyruqdan foydalanishingiz mumkin:

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```
Eslatma: Fayl nomi tizimda boshqacha bo‘lishi mumkin. **`/etc/netplan/`** ichidagi barcha fayllarni tekshirib ko‘ring va mos faylni tahrirlang.

2. IP konfiguratsiyasini sozlash
Faylni ochganingizdan so‘ng, tarmoq interfeysining (masalan, ens33 yoki eth0) IP sozlamalarini quyidagi tarzda o‘zgartiring. Interfeys nomini to‘g‘ri tanlang — buni ip a yoki ifconfig buyruqlari yordamida bilib olishingiz mumkin.

Ushbu konfiguratsiyani .yaml fayliga kiriting:

```yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.1.196/24
      gateway4: 192.168.1.246
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```
Izohlar:

 - dhcp4: no — bu qator statik IP-manzil qo‘llanayotganini bildiradi.
 - addresses — IP manzil va subnet maskni ko‘rsatadi (192.168.1.196/24).
 - gateway4 — asosiy shlyuzni belgilash uchun ishlatiladi.
 - nameservers — DNS server manzillarini ko‘rsatadi (Google DNS: 8.8.8.8, Cloudflare DNS: 1.1.1.1).
3. O‘zgartirishlarni saqlash va amalga oshirish
Faylga o‘zgartirishlarni kiritganingizdan so‘ng, Ctrl + O tugmasi bilan saqlang va Ctrl + X bilan fayldan chiqing.

Keyin, netplan apply buyrug‘i bilan o‘zgartirishlarni qo‘llang:

```bash
sudo netplan apply
```
4. IP manzilni tekshirish
ip a yoki ifconfig buyrug‘ini ishlatib, yangi IP manzil muvaffaqiyatli o‘zgarganini tekshiring:

```bash
ip a
```

---
## Yakuniy Eslatmalar
Agar xatolik yuz bersa, yaml faylidagi bo‘sh joylar yoki qatorlarni tekshiring, chunki yaml format juda aniq bo‘sh joylarga bog‘liq.
Statik IP-manzil konfiguratsiyasi tizim qayta ishga tushirilganda ham saqlanib qoladi.





