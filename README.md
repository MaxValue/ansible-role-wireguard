# Ansible Role: wireguard

An Ansible Role that installs wireguard and configures tunnels.

[TOC]

## Requirements

* [netaddr>=0.10.1](https://github.com/netaddr/netaddr)
* [the ansible.utils collection](https://galaxy.ansible.com/ui/repo/published/ansible/utils/)
   * install it via `ansible-galaxy collection install --force ansible.utils`
* enable the Jinja extension for loop controls in your ansible.cfg:
  ```ini
  [defaults]
  jinja2_extensions=jinja2.ext.loopcontrols
  ```

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

### Main Variables

    wireguard_dep_packages:
      - wireguard

The optional variable `wireguard_dep_packages` sets the list of OS packages which will be installed before configuration.

    wireguard_config_dir: "/etc/wireguard"

The optional variable `wireguard_config_dir` sets the root directory for the wireguard configuration files. It makes no sense to change this because this role uses wg-quick which always uses `/etc/wireguard`.

    wireguard_save_public_keys: true

The optional variable `wireguard_save_public_keys` sets if the public keys of defined tunnels should be downloaded to the controller.

    wireguard_hostdatadir_path: "host_data"

The optional variable `wireguard_hostdatadir_path` sets the path on the controller where underneath the public keys will be saved.

    wireguard_tunnels:

The optional variable `wireguard_tunnels` defines the tunnels to be configured.

    wireguard_print_peer_configuration: true

The optional variable `wireguard_print_peer_configuration` defines whether to print the needed configuration changes to permanently enable the new peer. Be aware that this can leak key information (the PresharedKey to be precise). Turn this off to hide any secrets from the logs.

### Internal variables

These are variables internally created by the role.

    _wireguard_tunnel_item

The internal variable `_wireguard_tunnel_item` stores the config for the current tunnel while looping through the defined tunnels of `wireguard_tunnels`.

    _wireguard_tunnel_PrivateKey

The internal variable `_wireguard_tunnel_PrivateKey` stores the newly generated private key of the tunnel when the tunnel did not exist before. The variable gets blanked after use.

    _wireguard_tunnel_PublicKey

The internal variable `_wireguard_tunnel_PublicKey` stores the generated public key of the tunnel.

    _wireguard_Peer_Address

The internal variable `_wireguard_Peer_Address` stores the newly generated IP address of the new peer for the tunnel.

    result_wireguard_find_tunnelconfig

The internal variable `result_wireguard_find_tunnelconfig` stores the result of the task for finding an existing config for the current tunnel.

    result_wireguard_peers

The internal variable `result_wireguard_peers` stores the result of the task for defining allowed peers for the current tunnel. The variable is mainly used to check if anything has changed and act accordingly.

    result_wireguard_strippedconfig

The internal variable `result_wireguard_strippedconfig` stores the cleaned wireguard config for the current tunnel, ready to be passed to `wg syncconf` when the peers information of the current tunnel has changed.

    result_wireguard_tmpfile

The internal variable `result_wireguard_tmpfile` stores the path to the temporary file for storing the cleaned wireguard config.

    result_wireguard_peer_PrivateKey

The internal variable `result_wireguard_peer_PrivateKey` stores the newly generated private key of the peer for the tunnel. The variable gets blanked after use.

    result_wireguard_peer_PublicKey

The internal variable `result_wireguard_peer_PublicKey` stores the newly generated public key of the peer for the tunnel.

    result_wireguard_Peer_PresharedKey

The internal variable `result_wireguard_Peer_PresharedKey` stores the newly generated preshared key of the peer for the tunnel. The variable gets blanked after use.

## Dependencies

* The ansible.utils collection: `ansible-galaxy collection install --force ansible.utils`

## Example Playbooks

### Minimal example

    ---
    # This configures a single tunnel, wg0, and adds a single peer to it
    - hosts: localhost
      roles:
        - role:
            name: maxvalue.wireguard
          vars:
            wireguard_tunnels:
              wg0:
                Address: 192.168.0.1/24
                Peers:
                  - name: Nickname
                    AllowedIPs:
                      - 192.168.0.2/24
                    PublicKey: "QLGVfS9r+jwqkl0iEfdyfqGIMhitmcMqsarUabvMXSY="
    ...

### Explicitely defining private keys

    ---
    # This configures a single tunnel, wg0, and adds a single peer to it
    - hosts: localhost
      roles:
        - role:
            name: maxvalue.wireguard
          vars:
            wireguard_tunnels:
              wg0:
                state: started
                ListenPort: 51820
                Address: 192.168.0.1/24
                enabled: true
                PrivateKey: "SCR6qWVDYiwJ2dXpaqK6ByHFJHeqaMmH4MsjeApGfk4="
                Peers:
                  - name: Nickname
                    AllowedIPs:
                      - 192.168.0.2/24
                    PublicKey: "QLGVfS9r+jwqkl0iEfdyfqGIMhitmcMqsarUabvMXSY="
    ...

### Normal usage

    ---
    # This configures a single tunnel, wg0, and adds a single peer to it
    - hosts: localhost
      roles:
        - role:
            name: maxvalue.wireguard
          vars:
            wireguard_tunnels:
              wg0:
                state: started
                ListenPort: 51820
                Address: 192.168.0.1/24
                Endpoint: my.domain.here:51820
                enabled: true
                Peers:
                  - name: Nickname
                    AllowedIPs:
                      - 192.168.0.2/24
                    PublicKey: "QLGVfS9r+jwqkl0iEfdyfqGIMhitmcMqsarUabvMXSY="
                    PresharedKey: "h8pxIv1mJOG0IC89/ZbPHnOneZOVdtMyia8E1wuCrVE="
    ...

## License

[MIT](LICENSE.txt)

## Sponsors

You can support the development of this role and other similar roles by donating to one of the accounts below.

* [bymeacoffee](https://www.buymeacoffee.com/publicbetamax)
* [liberapay](https://de.liberapay.com/maxvalue/)
* [ko-fi](https://ko-fi.com/publicbetamax)
* [Patreon](patreon.com/publicbetamax)

## Author Information

This role was created in 2025 by Max Fuxjäger:

* Mastodon: @maxvalue@chaos.social
* Matrix: @ypsilon:matrix.org
