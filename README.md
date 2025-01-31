## Tiny Linux

Побачив оце
(https://www.youtube.com/watch?v=u2Juz5sQyYQ).

І мене не покидає мета повторити це для LuckFox Pico Mini.

Ще буду дивитись на Sipeed M1s.

Роблю це із допомогою deepseek. Аж цікаво наскільки він в цьому допоможе.

## Буду просто нотувати шо я робив.

```sh
brew install riscv64-elf-gcc
```

Бляха, який поставився?

```
riscv64-unknown-elf-gcc --version
riscv64-unknown-elf-gcc (gc891d8dc2) 13.2.0

riscv64-elf-gcc --version
riscv64-elf-gcc (GCC) 14.2.0
/opt/homebrew/bin/riscv64-elf-gcc
/opt/homebrew/bin/riscv64-unknown-elf-gcc
```

Мабуть другий. Перший вже якось встановлено раніше.


```sh
git clone --depth 1 --branch v6.13 https://github.com/torvalds/linux.git
cd linux
make help
> Makefile:15: *** GNU Make >= 4.0 is required. Your Make version is 3.81.  Stop.
```

Ну починається...

```
brew install make
/opt/homebrew/opt/make/libexec/gnubin/make --version
```

```
GNU "make" has been installed as "gmake".
If you need to use it as "make", you can add a "gnubin" directory
to your PATH from your bashrc like:

    PATH="/opt/homebrew/opt/make/libexec/gnubin:$PATH"
```

Ок, **gmake** так **gmake**.

```sh
gmake ARCH=riscv CROSS_COMPILE=riscv64-elf- tinyconfig
```

Хтоб сумнівався.

```
Value of CONFIG_CC_OPTIMIZE_FOR_SIZE is redefined by fragment ./kernel/configs/tiny.config:
Previous value: # CONFIG_CC_OPTIMIZE_FOR_SIZE is not set
New value: CONFIG_CC_OPTIMIZE_FOR_SIZE=y

sed: 1: "./.tmp.config.udpWnNd9t2": invalid command code .
gmake[4]: *** [scripts/kconfig/Makefile:110: tiny.config] Error 1
gmake[3]: *** [Makefile:733: tiny.config] Error 2
gmake[2]: *** [scripts/kconfig/Makefile:116: tinyconfig] Error 2
gmake[1]: *** [/Volumes/external/SDK/my-tiny-lunux/linux/Makefile:733: tinyconfig] Error 2
gmake: *** [Makefile:251: __sub-make] Error 2
```


Буду пробувати те саме під Linux

```
sudo apt install gcc-riscv64-linux-gnu
riscv64-linux-gnu-gcc --version
> riscv64-linux-gnu-gcc (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0
sudo apt install bc binutils bison dwarves flex gcc git gnupg2 gzip libelf-dev libncurses5-dev libssl-dev make openssl pahole perl-base rsync tar xz-utils
```

Не впевнен шо то все потрібне. Ну ок.

```
git clone --depth 1 --branch v6.13 https://github.com/torvalds/linux.git
cd linux
ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- make tinyconfig
```

Спрацювало. Ну ок, поки під Linux продовжимо.

```
ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- make menuconfig
```

Шо треба включити:

- Executable file formats->Kernel support for ELF binaries [*]
- Device Drivers → Character devices → Enable TTY [*]
- Device Drivers → Character devices → Serial drivers → 8250/16550 and compatible UARTs → Console on 8250/16550 and compatible UART
- Device Drivers → MMC/SD/SDIO card support → MMC block device driver   (Якось не впевнен шо увімкнув те шо треба)
- Device Drivers → Device Tree and Open Firmware support → Device Tree support  (це начебто само вмикається)
- File systems → The Extended 4 (ext4) filesystem
- General setup -> Initial RAM filesystem and RAM disk (і в середині повимикати всі протоколи стискання)
- General setup -> Configure standard kernel features -> Enable support for printk
- Platform type -> FPU support [*]

```
ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- make -j 4

> Kernel: arch/riscv/boot/Image.xz is ready
```

Зібралось, і дуже швидко.

Чи є в мене qemu? (https://risc-v-getting-started-guide.readthedocs.io/en/latest/linux-qemu.html)

```
sudo apt install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev \
                 gawk build-essential bison flex texinfo gperf libtool patchutils bc \
                 zlib1g-dev libexpat-dev git
cd ..
git clone --depth 1 --branch v9.2.0 https://github.com/qemu/qemu
cd qemu
mkdir build
cd build
sudo apt install python3 python3-pip python3-setuptools python3-wheel ninja-build
sudo apt install meson
sudo apt install libglib2.0-dev
../configure --target-list=riscv64-softmmu
make -j4
```

В інструкції гілка v5.0.0 для qemu, та v5.4.0 для linux. Якшо будуть проблеми, буду пробувати ці гілки.

Такс, qmake начеб-то зібрався теж.

```
sudo make install
```

Ох, як мені не подобається цей **make install**.
Зберіг [лог](./make-install-output.md)

```
qemu-system-riscv64 --version
> QEMU emulator version 9.2.0 (v9.2.0)
```


Чи будемо колупати Busybox?

```
cd ../..
git clone --depth 1 --branch 1_37_0 https://git.busybox.net/busybox
cd busybox
CROSS_COMPILE=riscv64-linux-gnu- make defconfig
CROSS_COMPILE=riscv64-linux-gnu- make -j 4
```


Пробуємо створити SD-карту.

```
sudo umount /dev/sda1
sudo fdisk /dev/sda
sudo mkfs.vfat -F 32 /dev/sda1
sudo mkfs.ext4 /dev/sda2
```

У fdisk там створив GPT таблицю (g).
Далі створив два розділи (n), (p), (1), (+250M) і другий на весь обсяг.
Записав (w).


## Посилання

https://github.com/nir9/welcome/tree/master/lnx/very-minimal-shell
