# Ansible Role: NetBird (trevorijones.netbird)

[![CI](https://github.com/trevorijones/ansible-netbird-cli/workflows/CI/badge.svg?event=push)](https://github.com/trevorijones/ansible-netbird-cli/actions?query=workflow%3ACI)

An Ansible role that installs and configures NetBird agent (cli only) on Linux and macOS systems.

## Requirements

- Ansible 2.9 or higher

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

### Installation Configuration

    netbird_install_method: 'package'

Installation method to use. Options are:
- `script`: Use the official NetBird install script 
- `package`: Use distribution package manager (requires OS-specific configuration)
- `binary`: Download and install binary directly (OS-agnostic)

    netbird_package_state: 'present'

State of the NetBird package when using package installation method.

    netbird_version: 'latest'

Version to install when using binary installation method. Use 'latest' or specify a version like '0.28.8'.

### Service Configuration

    netbird_service_enabled: true
    netbird_service_state: 'started'

Controls the NetBird service state and whether it should start at boot.

### Authentication Configuration

    netbird_setup_key: ''

Setup key for NetBird authentication. Leave empty to use SSO authentication instead.

    netbird_management_url: ''

Custom management server URL. Leave empty to use the default NetBird cloud management server.

### Connection Settings

    netbird_auto_connect: true

Whether to automatically connect NetBird after installation when a setup key is provided.

    netbird_check_status: true

Whether to check and display NetBird connection status after role execution.

### Advanced Configuration

    netbird_config_dir: '/etc/netbird'
    netbird_log_level: 'info'
    netbird_interface_name: 'wt0'

NetBird configuration directory, logging level, and network interface name.

    netbird_config:
      LogLevel: "{{ netbird_log_level }}"
      InterfaceName: "{{ netbird_interface_name }}"

Additional NetBird configuration options. These will be written to the config.json file.

## Dependencies

None.

## Example Playbook

### Basic Installation with Script

    - hosts: servers
      become: true
      roles:
        - role: trevorijones.netbird
          vars:
            netbird_install_method: 'script'
            netbird_setup_key: 'your-setup-key-here'

### Package Installation

    - hosts: servers
      become: true
      roles:
        - role: trevorijones.netbird
          vars:
            netbird_install_method: 'package'
            netbird_setup_key: 'your-setup-key-here'

### Binary Installation with Custom Configuration

    - hosts: servers
      become: true
      roles:
        - role: trevorijones.netbird
          vars:
            netbird_install_method: 'binary'
            netbird_version: '0.28.8'
            netbird_setup_key: 'your-setup-key-here'
            netbird_log_level: 'debug'
            netbird_config:
              LogLevel: 'debug'
              InterfaceName: 'nb0'
              DisableAutoConnect: false

### Self-hosted NetBird Server

    - hosts: clients
      become: true
      roles:
        - role: trevorijones.netbird
          vars:
            netbird_setup_key: 'your-setup-key'
            netbird_management_url: 'https://netbird.example.com:33073'

### macOS Installation with Homebrew

    - hosts: macos_clients
      roles:
        - role: trevorijones.netbird
          vars:
            netbird_install_method: 'package'  # Uses Homebrew
            netbird_setup_key: 'macos-clients-setup-key'

**macOS Configuration Notes:**
- Use the ansible -K for user password
- Automatically uninstalls and reinstalls service if already present for clean setup.
- Automatically creates symlink from Homebrew install location to `/usr/local/bin/netbird` for global access.


## Supported Platforms

- Ubuntu 20.04+
- Debian 11+
- RHEL/CentOS 8+
- Fedora 38+
- openSUSE Leap 15.4+
- macOS 10.15+ (via Homebrew)

## Tags

The following tags are available for selective execution:

- `netbird` - All NetBird tasks
- `netbird-install` - Installation tasks only
- `netbird-config` - Configuration tasks only
- `netbird-service` - Service management tasks only
- `netbird-connect` - Connection/authentication tasks only

Example usage:

    ansible-playbook playbook.yml --tags "netbird-install,netbird-config"

## Security Considerations

- Setup keys should be treated as sensitive data and stored securely (consider using Ansible Vault)
- The role uses `no_log: true` for tasks handling sensitive authentication data


## License

MIT

## Author Information

This role was created for homelab automation and NetBird VPN management.

## Contributing

1. Follow the project's coding standards and conventions
2. Run `ansible-lint` before submitting changes
3. Test on supported platforms
4. Update documentation as needed