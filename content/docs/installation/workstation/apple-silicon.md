---
bookToc: false
---
# Apple Silicon

On MacOS, particularly on Apple Silicon (M1, M2, M3, ...), BurmillaOS can be tested using [UTM](https://github.com/utmapp/UTM).
To run BurmillaOS on UTM, create a new virtual machine using "Emulate" CPU,
for a "Linux" operating system, and set the CPU architecture to "x86_64".
Make sure to disable `UEFI Boot` and use the `virtio-vga` display card emulation in the settings.
