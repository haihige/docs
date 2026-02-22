# How to use Wii and GameCube controllers with Dolphin on FreeBSD

Tested with FreeBSD 15.0-RELEASE.

In the following examples, the `operator` group will get access to these devices. Ensure the user that lauches Dolphin is in this group.

The following commands must be run as `root`.


## Official GameCube Controller Adapter for Wii U

```console
vim /usr/local/etc/devd/gc_adapter.conf
```

```
notify 100 {
        match "system"      "USB";
        match "subsystem"   "DEVICE";
        match "type"        "ATTACH";
        match "vendor"      "0x057e";
        match "product"     "0x0337";
        action              "chown root:operator /dev/$cdev; chmod 0660 /dev/$cdev";
};
```

```console
vim /boot/loader.conf
```

```
# GameCube USB adapter
hw.usb.quirk.0="0x057e 0x0337 0x0000 0xffff UQ_HID_IGNORE"
```

Reboot, and Dolphin will now be able to access the adapter.


## Bluetooth Passthrough for Wii Controllers (Wiimotes)

Works with both WML-C43 and J27H002 modules.

```console
vim /usr/local/etc/devd/wii_adapter.conf
```

```
notify 100 {
        match "system"      "USB";
        match "subsystem"   "DEVICE";
        match "type"        "ATTACH";
        match "vendor"      "0x057e";
        match "product"     "0x0305";
        action              "chown root:operator /dev/$cdev; chmod 0660 /dev/$cdev";
};
```

Find which device is the Bluetooth adapter with `usbconfig`. Output will look like this:

```
ubt0 on uhub0
ubt0: <Broadcom Corp BCM2045A, class 224/1, rev 2.00/1.00, addr 10> on usbus0
```

```console
vim /boot/loader.conf
```

```
# Wii Bluetooth adapter
hint.ubt.0.disabled="1"
```

Reboot, and Dolphin will now be able to access and control the adapter.

This assumes that the bluetooth adapter is always at the same unit. Otherwise, plug in the adapter and manually detach your adapter:

```console
devctl detach ubt0
```
