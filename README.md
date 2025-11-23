# Інструкція з модифікації розділу завантаження
Інструкція дійсна для плати Luckfox Lyra та збірки Ubuntu_Luckfox_Lyra_MicroSD_250417.

В зкомпільованих файлах dts додано активацію двох додаткових spi-шин(0 та 1) на частоті 50мГц. Також видалено ініціалізацію DSI для керування стандартним дисплеєм.


---
### Компіляція та модифікація boot

Відредагувати dts, якщо потрібно.

---

Компілювати dts:

    dtc -I dts -O dtb -o new_dtb.dtb 0x800.dts
	
---

Виправити розмір *fdt.data-size* в header.

	ls -l new_dtb.dtb

В моєму випадку 29212

---

Компілювати header:

	dtc -I dts -O dtb -o new_header.dtb header.dts

---

Завантажити зкомпільовані dts на плату Lyra:

	adb push new_dtb.dtb /tmp/new_dtb.dtb
	adb push new_header.dtb /tmp/new_header.dtb

---

Записати файли в розділ boot на платі.

    dd if=/tmp/new_header.dtb of=/dev/mmcblk0p2 bs=1 seek=0 count=2048 conv=notrunc
    dd if=/tmp/new_dtb.dtb of=/dev/mmcblk0p2 bs=1 seek=2048 count=29212 conv=notrunc
    dd if=/tmp/new_dtb.dtb of=/dev/mmcblk0p2 bs=1 seek=4825088 count=29212 conv=notrunc

---


Перезавантажити.

---

Якщо на хості встановлено тільки Windows, тоді файли dts можна відредагувати на Windows, завантажити по ADB на плату Lyra та скомпілювати їх безпосередньо на платі.

