tinyoverflow.qbittorrent
=========

This role is here to make your life easier by automating the installation and configuration of the qBittorrent headless client (`qbittorrent-nox`) on Debian-based systems like Ubuntu. Whether you're setting up a personal seedbox or managing a larger deployment, this role ensures that qBittorrent is installed, configured, and running smoothly with minimal effort on your part.

With this role, you can customize how qBittorrent is installed and operates. You can define where the application is installed, where its data is stored, and even configure all properties of qBittorrent to suit your needs. The role takes care of creating the necessary system user and group, setting up directories, and configuring the systemd service to ensure qBittorrent starts automatically and integrates seamlessly with your system.

This role is perfect for anyone who wants a quick, reliable, and repeatable way to deploy qBittorrent. By simply tweaking a few variables, you can tailor the installation to your specific requirements. Whether you're a homelab enthusiast or a system administrator, this role has you covered. So, dive in, customize the variables, and let this role handle the heavy lifting for you!

Requirements
------------

This role is tailored for Debian-based systems like Ubuntu, where it feels right at home. It assumes you're using `apt` as your package manager and relies on `systemd` to keep things running smoothly. If you're venturing into uncharted territory with other distributions, proceed with caution - compatibility isn't guaranteed, but hey, who doesn't love a good adventure?

Role Variables
--------------

The following variables can be configured to customize the role's behavior:

### Instance Variables
| Variable                               | Description                                                                                | Default Value                                |
| -------------------------------------- | ------------------------------------------------------------------------------------------ | -------------------------------------------- |
| `qbittorrent_user`                     | The system user that will own the application files. Will be created if it doesn't exist.  | `qbittorrent`                                |
| `qbittorrent_group`                    | The system group that will own the application files. Will be created if it doesn't exist. | `qbittorrent`                                |
| `qbittorrent_data_dir`                 | The directory where qBittorrent data will be stored.                                       | `/var/lib/qbittorrent`                       |
| `qbittorrent_webui_port`               | The port on which the qBittorrent Web UI will be accessible.                               | `8080`                                       |
| `qbittorrent_systemd_name`             | The name of the systemd service for qBittorrent.                                           | `qbittorrent-nox`                            |
| `qbittorrent_systemd_unit_description` | Description for the systemd service.                                                       | `qBittorrent Headless Client`                |
| `qbittorrent_systemd_unit_wants`       | Dependencies for the systemd service.                                                      | `[network-online.target]`                    |
| `qbittorrent_systemd_unit_after`       | Services that must start before qBittorrent.                                               | `[network-online.target, nss-lookup.target]` |
| `qbittorrent_systemd_unit_requires`    | Additional required services for the systemd unit.                                         | `[]`                                         |
| `qbittorrent_config`                   | A dictionary of configuration options to be written to `qBittorrent.conf`.                 | `[]`                                         |

### `qbittorrent_config` Variable

The `qbittorrent_config` variable allows you to define custom configuration options for qBittorrent by specifying them as a dictionary. These options are written to the `qBittorrent.conf` file, which is used by qBittorrent to store its settings. This variable provides flexibility to customize qBittorrent's behavior according to your needs.

#### How It Works
The role compiles the `qbittorrent_config` dictionary with some default settings to ensure that qBittorrent operates correctly out of the box. These defaults include critical settings such as the Web UI port and legal notice acceptance. If you provide your own configuration options, they will be merged with the defaults; however, the role's default values for these critical settings will always take precedence to maintain stability and proper operation.

#### Default Settings
The role sets the following defaults in the `qBittorrent.conf` file:
- **LegalNotice.Accepted**: `"true"`  
  Ensures that the legal notice is accepted automatically.
- **Preferences.WebUI\Port**: The value of `qbittorrent_webui_port` (default: `8080`)  
  Configures the port on which the Web UI is accessible.

These defaults are essential for the role to function properly and cannot be overridden. If you attempt to override these settings in the `qbittorrent_config` variable, the role will prioritize its defaults to maintain stability and ensure proper operation.

#### Example Usage
Here’s an example of how you can use the `qbittorrent_config` variable to customize additional settings:

```yaml
qbittorrent_config:
  Preferences:
    WebUI\Username: "admin"
    WebUI\Password_PBKDF2: "..."
  BitTorrent:
    Session\DefaultSavePath: "/mnt/torrent/files"
    Session\TempPath: "/mnt/torrent/downloading"
```

Dependencies
------------

To run this role successfully, you need to have the following dependencies installed:

- `community.general`: It's very likely that this is already installed, as it should come bundled with Ansible.

Example Playbook
----------------

```yaml
- name: Install and configure qBittorrent
  become: true
  roles:
    - role: tinyoverflow.qbittorrent
      vars:
        qbittorrent_group: media
        qbittorrent_systemd_unit_requires: [mnt-media.mount]
        qbittorrent_webui_port: 8000
        qbittorrent_config:
          BitTorrent:
            Session\DefaultSavePath: "/mnt/media/torrent"

```

License
-------

GNU GPLv3
