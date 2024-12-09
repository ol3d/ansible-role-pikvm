# ol3d.pikvm

The goal of this role is to configure an official PiKVM device running on top of a Raspberry Pi.

## PiKVM Version Support

Not all versions of the PiKVM are currently supported in this role due to limited testing availability.

Due to physical requirements of Raspberry Pi PiKVM, testing needs to be completed on a physical device to ensure full compatibility. With this in mind, additional versions of PiKVM may be added in the future.

*Note that not all features are supported between different PiKVM versions. Verify your specific devices capabilities on the [official PiKVM documentation](https://docs.pikvm.org/).*

### Supported PiKVM Versions

This role has been tested with the following version(s) of PiKVM:

- PiKVM V3 HAT

### Unsupported PiKVM Versions

The following PiKVM version(s) are currently unsupported and may not work with this role:

- DIY PiKVM V1
- DIY PiKVM V2
- PiKVM V4 Mini
- PiKVM V4 Plus

## Role Variables

`oled_enabled` By setting this value to **true**, it enables the physical OLED screen on the PiKVM device. By default this is set to **false** since not all PiKVM devices utilize an OLED screen.

`oled_rotate` By setting this value to **true**, the PiKVM OLED screen will be rotated. This is useful if the screen is the incorrect orientation from which you want displayed. For this setting to be applied, `oled_enabled` must be set to **true**. By default this value is set to **false**.

`oled_fahrenheit` By setting this value to **true**, the PiKVM OLED screen will display temperature in Fahrenheit units instead of Celcius. For this setting to be applied, `oled_enabled` must be set to **true**. By default this value is set to **false**.

`fan_enabled` By setting this value to **true**, the PiKVM will activate the FAN header pins allowing power to a physical fan. By default this is set to **true** since the PiKVM is recommended to have active cooling.

`web_terminal_enabled` By setting this value to **true**, the PiKVM WebUI will enable terminal access. If set to false, no terminal actions can be performed from within the WebUI and must be performed by the root user using SSH. By default this is set to **true** to allow WebUI admin terminal access. ***Warning - All WebUI users have the same access rights. This means that any additional users besides the default admin account created for the WebUI will also have full access to the terminal.***

`pikvm_os_update_output_logs` By setting this value to **true**, during execution of this role, the system update output will be displayed within the ansible logs. This can be useful to see what packages are updated during role execution. By default this is set to **true**.

`hdmi_edid` If set, a custom HDMI EDID value will override the PiKVM default. By default, the EDID value will be set based on the PiKVM OS version.

`hdmi_passthrough_enabled` This option is only available for PiKVM V4 Plus. By setting this value to **false**, the OUT2 port on the back side of the PiKVM V4 Plus will be disabled. By default this option is set to **true**. To enable this setting, `kvmd_override_enabled` must also be set to **true**.

`mouse_jiggler_enabled` By setting this value to **true**, the virtual mouse jiggler will be available for use within the WebUI. By default this value is set to **false**. This setting does not turn ON the mouse jiggler, instead, it allows the user to activate it manually. To enable this setting, `kvmd_override_enabled` must also be set to **true**.

`mouse_jiggler_on_after_reboot` By setting this value to **true**, the virtual mouse jiggler will become active after reboot. By default this value is set to **false**. This setting turns ON the mouse jiggler by default after reboot. If you wish to have manual control over the activation of this setting after reboot, do not set this value to true. `mouse_jiggler_enabled` must be set to **true** for this setting to be applied. To enable this setting, `kvmd_override_enabled` must also be set to **true**.

`kvmd_override_enabled` By setting this value to **true**, the current override file within /etc/kvmd/override.yaml will be overwritten to match this roles config. By default this value is set to **false**.

`serial_over_usb_enabled` By setting this value to **true**, the PiKVM will be configured to allow serial connections over USB. This can be used for terminal access from the managed server to the PiKVM, or for any other purpose that requires a serial connection. This is only available for PiKVM V2+.

### Example Variable Usage

```yaml
oled_enabled: true
oled_rotate: false
oled_fahrenheit: false
fan_enabled: true
web_terminal_enabled: false
pikvm_os_update_output_logs: true
hdmi_edid: |
  00FFFFFFFFFFFF005262888800888888
  1C150103800000780AEE91A3544C9926
  0F505425400001000100010001000100
  010001010101D32C80A070381A403020
  350040442100001E7E1D00A050001940
  3020370080001000001E000000FC0050
  492D4B564D20566964656F0A000000FD
  00323D0F2E0F000000000000000001C4
  02030400DE0D20A03058122030203400
  F0B400000018E01500A0400016303020
  3400000000000018B41400A050D01120
  3020350080D810000018AB22A0A05084
  1A3030203600B00E1100001800000000
  00000000000000000000000000000000
  00000000000000000000000000000000
  00000000000000000000000000000045
hdmi_passthrough_enabled: false
mouse_jiggler_enabled: true
mouse_jiggler_on_after_reboot: false
kvmd_override_enabled: true
serial_over_usb_enabled: false
```

## Example Playbook

```yaml
---
- name: Playbook to configure a PiKVM
  hosts:
    - pikvm
  gather_facts: true
  any_errors_fatal: true

  pre_tasks:
    - name: Pausing for 2 seconds...
      ansible.builtin.pause:
        seconds: 2

  tasks:
    - name: Configure PiKVM
      ansible.builtin.include_role:
        name: ol3d.pikvm
```

## HDMI

### EDID

This role can be used to modify the EDID on the Raspberry Pi. The default action for this role is to utilize the default preset EDID based on the PiKVM OS version. When modifying the EDID, a custom value must be provided as a role variable. There are a list of available EDID file presets located either on the PiKVM official documentation [here](https://docs.pikvm.org/edid/), or a more comprehensive list of EDID files can be found [here](https://github.com/linuxhw/EDID/tree/master).

### Passthrough

On a PiKVM V4 Plus, there is an option to use the PiKVM as a HDMI Video Passthrough. By connecting the display to the OUT2 port on the back side of the PiKVM, it allows you to gap between the target host and physical display. This prevents PiKVM from interfereing with the normal operation of the display and passes video signal through itself until you need remote access. PiKVM directs the video stream to the WebUI or VNC.

## Disclaimer

This role will update system packages if available to the most recent version. This action cannot currently be disabled.

This role will cause the PiKVM to reboot if required. This action cannot currently be disabled.

If `kvmd_override_enabled` is set to **true**, the current /etc/kvmd/override.yaml file will be overwritten and cannot be recovered. Please ensure that you have a backup of the current override.yaml file.

**Warning**: By using this role, you acknowledge that it may impact any existing installations or configurations on your PiKVM device. This role and its contributors are not responsible for any data loss, configuration changes, or other issues that may arise. Use at your own risk and ensure you have appropriate backups and safeguards in place before running this role on a production device.

## Contributors

Adam Delo (@ol3d) - Main developer

[Full list of contributers](https://github.com/ol3d/ansible-role-pikvm/graphs/contributors)
