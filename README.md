# zephyr-st25r

This repo provides a Zephyr module with a driver for the ST25R NFC reader IC. 
In particular, the ST25R3916 and ST25R3916B ICs are supported.

This driver supports at most one instance of the ST25R on the SPI or I2C bus.

ST's RFAL library is included for driving the IC, with Kconfig variables
defined to allow configuration and feature selection.

## Add module `zephyr-st25r` to you `west.yml` file

In your workspace, add the following entry to your manifest file west.yml :

```
    - name: zephyr-st25r
      url: https://github.com/ptournoux/zephyr-st25r.git
      revision: main  # Or the specific branch or commit hash you want
      path: modules/lib/zephyr-st25r

```

If you followed the [getting started](https://docs.zephyrproject.org/latest/develop/getting_started/index.html) procedure of zephyr, the manifest file is in `~/zephyrproject/zephyr/west.yml`.

Update west to download the module :

```bash
west update
```

## Add a st,st25r SPI to your device configuration


To use, after incorporating the module into your build, add an 
`st,st25r` compatible SPI or I2C configuration to your device tree

```
&spi3 {
    status = "okay";

    cs-gpios = <&gpio1 12 GPIO_ACTIVE_LOW>;

    nfc0: st25r@0 {
        compatible = "st,st25r";
        reg = <0x0>;
        spi-max-frequency = <4000000>;
        irq-gpios = <&gpio0 3 GPIO_ACTIVE_HIGH>;
    };
};
```

and configure the device in your `prj.conf` file as desired

```
CONFIG_ST25R=y
CONFIG_ST25R3916B=y
CONFIG_ST25R_TRIGGER_GLOBAL_THREAD=y

CONFIG_RFAL_FEATURE_NFCA=y
CONFIG_RFAL_FEATURE_ISO_DEP=y
CONFIG_RFAL_FEATURE_ISO_DEP_POLL=y
```

After that you can use RFAL to drive the IC

```c
#include "rfal_nfc.h"

void init() {
   ReturnCode err = rfalNfcInitialize();
   /* Use RFAL to poll NFC. */
}
```


## Test using a complete project

You can now download a complete example. E.g. in your workspace `~/zephyrproject/zephyr` run :

```bash
git clone https://github.com/ptournoux/zephyr-app-nfc08a1
```

Then run :

```
west build --board=nucleo_l073rz -p always zephyr-app-nfc08a1
```


Both the module and sample application comes from https://github.com/vouch-opensource/zephyr-nfc08a1 with some minor modifications to make them compatible with Zephyr 4.