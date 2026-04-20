📡 Fix Wi-Fi Drops on Linux (RTL8723BE)

🧩 Problem

On laptops with the ****, Wi-Fi may:

- randomly disconnect
- show weak or unstable signal
- disappear ("No such device (-19)")
- reconnect intermittently

This is typically caused by:

- incorrect antenna selection
- aggressive power saving
- unstable Realtek driver behavior

---

💻 Affected Hardware

Example device:

- ASUS VivoBook Max

But applies to any laptop with RTL8723BE.

---

✅ Solution (Stable Configuration)

1. Configure Driver (Antenna Fix)

Create config file:

sudo nano /etc/modprobe.d/rtl8723be.conf

Add:

options rtl8723be ant_sel=2 ips=0 fwlps=0

🔧 Explanation

- "ant_sel=2" → forces correct antenna (critical fix)
- "ips=0" → disables power saving
- "fwlps=0" → disables firmware power saving

---

2. Apply Changes

Reload module:

sudo modprobe -r rtl8723be
sudo modprobe rtl8723be

Or reboot:

reboot

---

3. Disable NetworkManager Power Saving

Create config:

sudo nano /etc/NetworkManager/conf.d/wifi-powersave.conf

Add:

[connection]
wifi.powersave = 2

Apply:

sudo systemctl restart NetworkManager

---

4. Disable Wi-Fi Power Save (runtime)

Install tool:

sudo apt install iw

Check interface:

iw dev

Disable power save:

sudo iw dev wlp3s0 set power_save off

«Replace "wlp3s0" with your actual interface.»

---

🔍 Verification

Check parameters:

cat /sys/module/rtl8723be/parameters/ant_sel
cat /sys/module/rtl8723be/parameters/ips
cat /sys/module/rtl8723be/parameters/fwlps

Expected:

2
N
N

---

🧪 Troubleshooting

Check interface:

iw dev
ip link

Check driver:

lsmod | grep rtl8723be

Logs:

dmesg | grep -i rtl

---

⚠️ Notes

- "ant_sel" is hardware-dependent:
  - try "1" or "2" if unstable
- Realtek drivers are known to be unreliable on Linux

---

🚀 Recommended Alternative (Best Stability)

Replace internal Wi-Fi with:

- USB adapter (Intel / Atheros chipset)

Benefits:

- stable connection
- better signal
- no driver issues

---

📌 Summary

Fix| Required
ant_sel tuning| ✅ critical
disable power save| ✅ important
reload driver| ✅ required

---

📜 License

MIT / Free to use


---

# Настройка wi‑fi на Asus VivoBook Max

Драйвер: для драйвера rtl8723be
*если другой - заменить имя драйвера

Проверить модель адаптера Wi‑Fi:
```
lspci -nnk | grep -iA3 network
```

Создать или открыть файл конфигурации модуля:
```
sudo nano /etc/modprobe.d/rtl8723be.conf
```

Вставить одну строку:
```
options rtl8723be ant_sel=2 fwlps=0 ips=0
```

Если после этого на некоторых моделях Asus сигнал стал хуже, попробуйте другую антенну, поменяв параметр на:
```
options rtl8723be ant_sel=1 fwlps=0 ips=0
```

Применить изменения (перезагрузка):
```
sudo reboot
```

Или без перезагрузки — перезагрузить модуль:
```
sudo modprobe -r rtl8723be
sudo modprobe rtl8723be
```

Коротко о параметрах:
- ant_sel — выбор антенны (1 или 2);
- fwlps, ips — отключение режимов энергосбережения (0 — выключено).

Используйте эти команды с правами администратора и сохраняйте резервные копии конфигураций перед изменениями.
