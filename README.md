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

`oled.enabled` By setting this value to **true**, it enables the physical OLED screen on the PiKVM device. By default this is set to **false** since not all PiKVM devices utilize an OLED screen.

`oled.rotate` By setting this value to **true**, the PiKVM OLED screen will be rotated. This is useful if the screen is the incorrect orientation from which you want displayed. For this setting to be applied, `oled.enabled` must be set to **true**. By default this value is set to **false**.

`oled.fahrenheit` By setting this value to **true**, the PiKVM OLED screen will display temperature in Fahrenheit units instead of Celcius. For this setting to be applied, `oled.enabled` must be set to **true**. By default this value is set to **false**.

`fan.enabled` By setting this value to **true**, the PiKVM will activate the FAN header pins allowing power to a physical fan. By default this is set to **true** since the PiKVM is recommended to have active cooling.

`web_terminal.enabled` By setting this value to **true**, the PiKVM WebUI will enable terminal access. If set to false, no terminal actions can be performed from within the WebUI and must be performed by the root user using SSH. By default this is set to **true** to allow WebUI admin terminal access. ***Warning - All WebUI users have the same access rights. This means that any additional users besides the default admin account created for the WebUI will also have full access to the terminal.***

`pikvm_os_update_output_logs` By setting this value to **true**, during execution of this role, the system update output will be displayed within the ansible logs. This can be useful to see what packages are updated during role execution. By default this is set to **true**.

`hdmi.edid.custom` If set, a custom HDMI EDID value will override the PiKVM default. By default, the EDID value will be set based on the PiKVM OS version.

`validate.validators.system` By setting this value to **true**, this role will validate the PiKVM required files and system packages before configuring anything. When using an official PiKVM OS provided by PiKVM, this should pass without issue, however, by setting this value to **true**, it ensures that any custom OS is able to be properly configured. By default this is set to **true**.

`validate.validators.prometheus` By default PiKVM allows Prometheus scraping by exposing metrics on the `/api/export/prometheus/metrics` endpoint. By setting this value to **true**, this role will validate that the metrics endpoint is available and correctly configured. By default this is set to **true**.

`validate.fail_on_validate` By setting this value to **true**, any validators that fail will stop the role from continuing execution. This is helpful to prevent unknown issues from occuring during role execution if a validator fails. By default this is set to **true**.

### Example Variable Usage

```yaml
oled:
  enabled: true
  rotate: false
  fahrenheit: false
fan:
  enabled: true
web_terminal:
  enabled: false
pikvm_os_update_output_logs: true
hdmi:
  edid:
    custom: |
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
validate:
  validators:
    system: true
    prometheus: true
  fail_on_validate: false
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

## HDMI EDID

This role can be used to modify the EDID on the Raspberry Pi. The default action for this role is to utilize the default preset EDID based on the PiKVM OS version. When modifying the EDID, a custom value must be provided as a role variable. There are a list of available EDID file presets located either on the PiKVM official documentation [here](https://docs.pikvm.org/edid/), or a more comprehensive list of EDID files can be found [here](https://github.com/linuxhw/EDID/tree/master).

## Disclaimer

This role will update system packages if available to the most recent version. This action cannot currently be disabled.

This role will cause the PiKVM to reboot if required. This action cannot currently be disabled.

**Warning**: By using this role, you acknowledge that it may impact any existing installations or configurations on your PiKVM device. This role and its contributors are not responsible for any data loss, configuration changes, or other issues that may arise. Use at your own risk and ensure you have appropriate backups and safeguards in place before running this role on a production device.

## Contributors

Adam Delo (@ol3d) - Main developer

[Full list of contributers](https://github.com/ol3d/ansible-role-pikvm/graphs/contributors)
