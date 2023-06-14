# This package contains the Enclosure LED Utilities, version 0.97

Copyright (C) 2009-2023 Intel Corporation.

All files in this package can be freely distributed and used according
to the terms of the GNU General Public License, version 2.
See http://www.gnu.org/ for details.

-------------------------

## 1. Dependencies

-------------------------

Following packages are required to compile:

|RHEL|SLES|Debian/Ubuntu|
|:---:|:---:|:---:|
| `pkgconf`, `RHEL7: pkgconfig`  | `pkg-config` | `pkg-config` |
| `automake` | `automake`   | `automake`   |
| `autoconf` | `autoconf`   | `autoconf`   |
| `gcc` | `gcc` | `gcc` |
| `make` | `make` | `make` |
| `sg3_utils-devel`| `libsgutils-devel`  | `libsgutils2-dev` |
| `systemd-devel`  | `libudev-devel`     | `libudev-dev`     |
| `pciutils-devel` | `pciutils-devel`    | `libpci-dev`      |

## 2. Configure package

-------------------------

Run `autogen.sh` to generate compiling configurations:
   `./autogen.sh`
   `./configure`

Run ./configure with:
    `--enable-systemd` to configure with systemd service.

Run ./configure with:
    `--disable-library` to disable building and installing the ledmon shared library.

## 3. Compiling the package

-------------------------

Run `make` command to compile the package.

## 4. (Un)installing the package

-------------------------

Run following commands to install package:
   `make install`

Run following commands to uninstall package:
   `make uninstall`

## 5. Using the library
   A library is built by default and can be used to add led control to your existing applications.  Assuming the library
   is installed do the following:

1. Make edits to your application, this simple source file shows the key things needed to use the library.


```c
#include <stdlib.h>
#include <stdio.h>

// Include the library header
#include <led/libled.h>


// Use the slot getter functions to retrieve information about a slot
static inline void print_slot(struct led_slot_list_entry *s)
{
    printf("cntrl type: %d: slot: %-15s led state: %-15d device: %-15s\n",
        led_slot_cntrl(s), led_slot_id(s), led_slot_state(s), led_slot_device(s));
}

int main() {
    struct led_slot_list_entry *slot = NULL;
    struct led_ctx *ctx = NULL;
    struct led_slot_list *slot_list = NULL;
    led_status_t status;

    status = led_new(&ctx);
    if (LED_STATUS_SUCCESS != status){
        printf("Failed to initialize library\n");
        return 1;
    }

    status = led_scan(ctx);
    if (LED_STATUS_SUCCESS != status){
        printf("led_scan failed %d\n", status);
        return 1;
    }

    status = led_slots_get(ctx, &slot_list);
    if (LED_STATUS_SUCCESS != status) {
        printf("led_slots_get failed %d\n", status);
        return 1;
    }

    printf("Printing slots in order returned\n");
    while ((slot = led_slot_next(slot_list))) {
        print_slot(slot);
    }

    led_slot_list_free(slot_list);

    led_free(ctx);
    return 0;
}
```

2. Compile and link

```bash
$ gcc -Wall `pkg-config --cflags --libs ledmon` list_slots.c -o list_slots
```

3. Run

```bash
$ ./list_slots
Printing slots in order returned
cntrl type: 3: slot: /dev/sg43-0     led state: 2               device: /dev/sdw
cntrl type: 3: slot: /dev/sg43-1     led state: 2               device: /dev/sdx
cntrl type: 3: slot: /dev/sg43-2     led state: 2               device: /dev/sdy
cntrl type: 3: slot: /dev/sg43-3     led state: 2               device: /dev/sdz
cntrl type: 3: slot: /dev/sg43-4     led state: 2               device: /dev/sdaa
cntrl type: 3: slot: /dev/sg43-5     led state: 2               device: /dev/sdab
cntrl type: 3: slot: /dev/sg43-6     led state: 2               device: /dev/sdac
cntrl type: 3: slot: /dev/sg43-7     led state: 2               device: /dev/sdad
cntrl type: 3: slot: /dev/sg43-8     led state: 2               device: /dev/sdae
cntrl type: 3: slot: /dev/sg43-9     led state: 2               device: /dev/sdaf
cntrl type: 3: slot: /dev/sg43-10    led state: 2               device: /dev/sdag
cntrl type: 3: slot: /dev/sg43-11    led state: 2               device: /dev/sdah
cntrl type: 3: slot: /dev/sg43-12    led state: 2               device: /dev/sdai
cntrl type: 3: slot: /dev/sg43-13    led state: 2               device: /dev/sdaj
cntrl type: 3: slot: /dev/sg43-14    led state: 2               device: /dev/sdak
cntrl type: 3: slot: /dev/sg43-15    led state: 2               device: /dev/sdal
$
```

4. For more information on API, please review the `led/libled.h` header file.

## 6. Release notes

-------------------------

a. Enclosure LED Utilities is meant as a part of RHEL, SLES and Debian/Ubuntu linux
   distributions.

b. For backplane enclosures attached to ISCI controller support is limited to
   Intel(R) Intelligent Backplane.
