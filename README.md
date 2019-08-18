[![asciicast](https://asciinema.org/a/6qAJ7eDSPknB45uqdfDaFH9x8.png)](https://asciinema.org/a/6qAJ7eDSPknB45uqdfDaFH9x8?speed=1.5&autoplay=1)

# Network Mock

`network_mock` is a simple ssh server that can respond to network device `show` commands. It was authored as a debugging tool used during local development.

## Getting started

The previously retrieved command output needs to be stored in a directory, one per device. The file needs to be named as the exact command.

```
├── eos101
│   └── show run.txt
├── ios101
│   └── show run.txt
├── nxos101
│   └── show run.txt
└── vyos101
    └── show config.txt
```

## Starting the server

Update the server.py to reflect the base port and number of servers.
The base port is by default 3000.

A key needs to be provided:

```
python server.py test_rsa.key
```

## Using with ansible

At the beginning of the play, the ssh server needs to be informed of the name of the network device.  This is used as the folder name from which the command output will be retrieved.

Issue the command in the format `#hostname=xxxx` or `!hostname=xxxx`, where xxx is the name of the device for which `show` command output will be retrieved.

By using the comment character specific to the network device os, switching between network_mock and the real devices can be done without playbook modifications.

```
platform_comment:
  eos: '!'
  ios: '#'
  nxos: '#'
  vyos: '#'
```

```
tasks:
- cli_command:
    command: "#hostname={{ inventory_hostname }}"
```

The ansible invnetory should be updated such that each host uses a unique port on the SSH mock server and the `ansible_host` set to where the server was started.

```
vars:
  ansible_port: "{{ 2200 + play_hosts.index(inventory_hostname) }}"
  ansible_host: localhost
```
## Examples

See the examples directory for a few examples

## Note

- Disabling ssh key checking during development can be done in the ansible.cfg
