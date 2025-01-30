## Tiny Linux

Побачив оце
(https://www.youtube.com/watch?v=u2Juz5sQyYQ).

І мене не покидає мета повторити це для LuckFox Pico Mini.

Ще буду дивитись на Sipeed M1s.


## Буду просто нотувати шо я робив.

```sh
brew install riscv64-elf-gcc
```

Бляха, який поставився?

riscv64-unknown-elf-gcc --version
riscv64-unknown-elf-gcc (gc891d8dc2) 13.2.0

riscv64-elf-gcc --version
riscv64-elf-gcc (GCC) 14.2.0
/opt/homebrew/bin/riscv64-elf-gcc
/opt/homebrew/bin/riscv64-unknown-elf-gcc

Мабуть другий. Перший вже якось встановлено раніше.


```sh
git clone --depth 1 --branch v6.13 https://github.com/torvalds/linux.git
```

## Посилання

https://github.com/nir9/welcome/tree/master/lnx/very-minimal-shell
