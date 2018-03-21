# imx-snd-pwm

A simple sound card based on PWM for i.mx6x processors.

## How to add this driver in a kernel

* Copy the imx-snd-pwm.c file to the linux-xyz/sound/drivers directory

* Edit linux-xyz/sound/drivers/Kconfig

```
if SND_DRIVERS

config SND_PWM_IMX
        tristate "i.MX PWM Audio support"
        depends on ARCH_MXC
        select SND_PCM
        help
          PWM Audio for i.MX.
```

* Edit linux-xyz/sound/drivers/Makefile

```
#
# Makefile for ALSA
# Copyright (c) 2001 by Jaroslav Kysela <perex@perex.cz>
#

snd-pwm-imx-objs := imx-snd-pwm.o
...

# Toplevel Module Dependency
obj-$(CONFIG_SND_PWM_IMX) += snd-pwm-imx.o
```

* Then, enable the driver in menuconfig and re-build the kernel

```
 Symbol: SND_PWM_IMX [=y]
 Type  : tristate
 Prompt: i.MX PWM Audio support
   Location:
     -> Device Drivers
       -> Sound card support (SOUND [=y])
         -> Advanced Linux Sound Architecture (SND [=y])
           -> Generic sound devices (SND_DRIVERS [=y])
   Defined at sound/drivers/Kconfig:35
   Depends on: SOUND [=y] && !M68K && !UML && SND [=y] && SND_DRIVERS [=y] && ARCH_MXC [=y]
   Selects: SND_PCM [=y]

```

## Example description in DTS

The driver is tested on imx6ull, but it should work on any processor with the same PWM IP block

```
		pinctrl_pwm7: pwm7grp {
			fsl,pins = <
				MX6UL_PAD_JTAG_TCK__PWM7_OUT   0x110b0
			>;
		};
...

&pwm7 {
	compatible = "fsl,imx-snd-pwm";
	pinctrl-names = "default";
	pinctrl-0 = <&pinctrl_pwm7>;
	clocks = <&clks IMX6UL_CLK_PWM7>,
		 <&clks IMX6UL_CLK_PWM7>;
	status = "okay";
};

```

This property fixes an error in imx6ull.dtsi

https://community.nxp.com/message/969650

```
clocks = <&clks IMX6UL_CLK_PWM7>, <&clks IMX6UL_CLK_PWM7>;
```

and in general it is not necessary.
