
# Splunk CA Role

This Ansible role is designed to manage the creation and deployment of SSL certificates for Splunk instances. It includes tasks for generating a Root CA, signing server certificates, and deploying them to Splunk instances to enable secure communication.

## Features

- Generate a Root CA (Certificate Authority).
- Create private keys and Certificate Signing Requests (CSRs) for Splunk instances.
- Sign server certificates using the Root CA.
- Deploy SSL certificates and private keys to Splunk instances.
- Configure Splunk to use SSL for secure communication.

## Requirements

- Ansible 2.3 or later.
- The `community.crypto` collection for OpenSSL-related tasks. From the playbook's **root** directory, install it using:
  ```bash
  ansible-galaxy collection install tar/community-crypto-2.26.0.tar.gz -p collections/
  ```

## Role Variables

The following variables can be configured in your playbook or inventory:

| Variable                     | Description                                                                    | Default Value                     |
|------------------------------|--------------------------------------------------------------------------------|-----------------------------------|
| `splunkca_directory`         | Directory where the Root CA files are stored. (on ansible controller)          | `/etc/splunk`                     |
| `splunkca_ownca_dir`         | Directory where the ownca files are stored.                                    | `<splunkca_directory>/ca`         |
| `splunkca_certs_dir`         | Directory where the sever files are stored.                                    | `<splunkca_directory>/certs`      |
| `splunkca_passphrase`        | Passphrase for the Root CA private key.                                        | `""`                              |
| `splunkca_privatekey`        | Name of the Root CA private key file.                                          | `ownca.key`                       |
| `splunkca_certificate`       | Name of the Root CA certificate file.                                          | `ownca.pem`                       |
| `splunkca_certificate_path`  | Full path to the Root CA certificate file. (on ansible controller)             | `<splunkca_ownca_dir>/ownca.pem`  |
| `splunkca_privatekey_path`   | Full path to the Root CA private key file. (on ansible controller)             | `<splunkca_ownca_dir>/ownca.key`  |
| `server_certificate`         | Name of the server certificate file for Splunk instances.                      | `<hostname>.server.pem`           |
| `server_privatekey`          | Name of the server private key file for Splunk instances.                      | `<hostname>.server.key`           |
| `splunk_home`                | Path to the Splunk installation directory.                                     | `/opt/splunk`                     |
| `splunk_nix_user`            | Path to the Splunk installation directory.                                     | `splunk`                          |
| `splunk_nix_group`           | Path to the Splunk installation directory.                                     | `splunk`                          |
| `systemd_unit_full`          | Path to the Splunk installation directory.                                     | `Splunkd`                         |
| `splunk_http_port`           | Port for Splunk Web (HTTP/HTTPS).                                              | `8443`                            |
| `splunk_webcert_dir`            | Name of directory on Splunk instances where certificates will be deployed.     | `webcerts`                        |


**Tips:** 

1. The `splunkca_directory` must be loacted on the **ansible controller** wher **ansible** is installed.
2. the `splunk_webcert_dir` is on the sever: `$SPLUNK_HOME/etc/auth/webcerts`

## Playbook
The following example playbooks have been included in this project for your reference:

- **splunkca_generate_ownca.yml** - Generate the CA for the Splunk hosts using the `generate_ownca.yml` task in the splunkca role. 
- **splunkca_generate_server_certs.yml** - Generates the server certificates for each Splunk Enterprise instances in the `full` section of the inventory using the `generate_server_certificates.yml` task in the splunkca role.
- **splunkca_deploy_server_certs.yml** - Deploys server certificates and configures each Splunk Enterprise instances in the `full` section of the inventory using the `deploy_server_certificates.yml` task in the splunkca role. See [Configure Splunk for SSL](#Configure-Splunk-for-SSL)


## Example Playbook

Here’s an example of how to use this role in a playbook:

```yaml
- name: Configure SSL for Splunk Instances
  hosts: all
  become: true
  vars:
    splunkca_directory: "/etc/splunk/ca"
    splunkca_passphrase: "your_secure_passphrase"
    splunkca_privatekey: "ca.key"
    splunkca_certificate: "ca.pem"
    splunk_cert_dir: "custom_certs"
    splunk_home: "/opt/splunk"
    splunk_http_port: 8000
  roles:
    - splunk_ca
```

## Tasks Overview

### 1. Validate Parameters
Ensures all required variables are defined and valid.

### 2. Generate Root CA
Creates a Root CA private key and self-signed certificate. Default Path: `/etc/splunk/ca`

### 3. Generate Server Certificates
Creates private keys and CSRs for Splunk instances and signs them using the Root CA. Default Path: `/etc/splunk/certs`

### 4. Deploy Certificates
Copies the signed certificates and private keys to the appropriate directories on Splunk instances. `$SPLUNK_HOME/etc/auth/webcerts`

### 5. Configure Splunk for SSL
Updates Splunk configuration files (e.g., `web.conf`) to enable SSL.

```
[settings]
... 
enableSplunkWebSSL = 1
httpport = 8443
serverCert = etc/auth/webcerts/<hostname>.server.pem
privKeyPath = etc/auth/webcerts/<hostname>.server.key
```

## Dependencies

This role depends on the `community.crypto` collection for OpenSSL-related tasks. From the playbook's **root** directory, install it using:
```bash
ansible-galaxy collection install tar/community-crypto-2.26.0.tar.gz -p collections/
```

## License

This role is licensed under the MIT License. See the `LICENSE` file for details.

## Author Information

This role was created by Jason Hickey. For questions or support, please contact [your_email@example.com].