


## Init Playbook

```sh
ansible-galaxy init splunk
```

## Intall collections form command line to playbook collections folder

```sh
ansible-galaxy collection install tar/community-general-10.5.0.tar.gz -p collections/
ansible-galaxy collection install tar/community-crypto-2.26.0.tar.gz -p collections/
```


```sh
.
├── ansible.cfg
├── environments/         # Parent directory for our environment-specific directories
│   │
│   ├── dev/              # Contains all files specific to the dev environment
│   │   ├── group_vars/   # dev specific group_vars files
│   │   │   ├── all
│   │   │   ├── db
│   │   │   └── web
│   │   └── hosts         # Contains only the hosts in the dev environment
│   │
│   ├── prod/             # Contains all files specific to the prod environment
│   │   ├── group_vars/   # prod specific group_vars files
│   │   │   ├── all
│   │   │   ├── db
│   │   │   └── web
│   │   └── hosts         # Contains only the hosts in the prod environment
│   │
│   └── stage/            # Contains all files specific to the stage environment
│       ├── group_vars/   # stage specific group_vars files
│       │   ├── all
│       │   ├── db
│       │   └── web
│       └── hosts         # Contains only the hosts in the stage environment
│
├── playbook.yml
│
└── . . .

```



## Solution:
- You need to specify the target host or group from your inventory file when running the ansible command. For example:
```sh
ansible all -m ping
```

### Explanation:
- all: This is the "pattern" that matches all hosts in your inventory.
- -m ping: This specifies the ping module, which is used to test connectivity to the hosts.
If you want to target a specific group or host, replace all with the name of the group or host defined in your inventory file. For example:

```sh
ansible webservers -m ping
```

## Additional Notes:
- Ensure your inventory file (defined in ansible.cfg as inventory=hosts/prod) contains the correct host or group definitions.
If you are unsure about the groups or hosts in your inventory, you can list them using:

```sh
ansible-inventory --list
```

```sh
main.yml
prereqs.yml
remove_splunk.yml
install_splunk.yml
splunk_stop.yml
upgrade_splunk.yml
splunk_start.yml
splunk_restart.yml
splunk_offline.yml
splunk_license_accept.yml
set_upgrade_state.yml
download_and_unarchive.yml
check_splunk_status.yml
check_splunk_version.yml
check_splunk.yml
configure_facl.yml
configure_systemd.yml
configure_thp.yml
configure_splunk_secret.yml
configure_bash.yml
configure_os.yml
configure_dmesg.yml
configure_firewall.yml
configure_deploymentclient.yml
configure_user-seed.yml
configure_authentication.yml
configure_custom_mgmt_port.yml
configure_disable_mgmt_port.yml
configure_splunk_boot.yml
post_install.yml
add_crashlog_script.yml
add_diag_script.yml
add_pstack_script.yml
install_utilities.yml
configure_license.yml
```


```sh
ansible-vault encrypt hosts/dev/group_vars/stigman/vault.yml --vault-id dev@~/.vault_pass_dev --encrypt-vault-id dev
ansible-vault encrypt hosts/prod/group_vars/stigman/vault.yml --vault-id prod@~/.vault_pass_prod --encrypt-vault-id prod
```

```sh
ansible-vault decrypt hosts/dev/group_vars/stigman/vault.yml --vault-id dev@~/.vault_pass_dev
ansible-vault decrypt hosts/prod/group_vars/stigman/vault.yml --vault-id prod@~/.vault_pass_prod


# Find exact match
```sh
grep -Hrnw 'stigman.offline_install' .
```

# Find and replace
```sh
find . -type f -name "*.yml" -exec sed -i 's/stigman.offline_install/stigman_offline_install/g' {} \;

```

## Vault Password

The `vault_password_file` configuration in your `ansible.cfg` file specifies the file that contains the password for decrypting Ansible Vault-encrypted files. Here's how to use it:

### Steps to Use `vault_password_file`:

1. **Create the Vault Password File**:
   - Create a file at the path specified in your `ansible.cfg` (e.g., `~/.ansible_vault_password`).
   - Add the password for your Ansible Vault to this file. For example:
     ```sh
     echo "your_vault_password" > ~/.ansible_vault_password
     ```
   - Ensure the file is secure by restricting its permissions:
     ```sh
     chmod 600 ~/.ansible_vault_password
     ```

2. **Encrypt Variables or Files with Ansible Vault**:
   - Use the `ansible-vault encrypt` command to encrypt files or variables. For example:
     ```sh
     ansible-vault encrypt hosts/prod/group_vars/all.yml
     ```
   - The `vault_password_file` will automatically be used to decrypt these files during playbook execution.

3. **Run Playbooks with Encrypted Files**:
   - When running a playbook that uses encrypted files, Ansible will automatically use the password from the `vault_password_file` to decrypt them. For example:
     ```sh
     ansible-playbook playbooks/playbook.yml 
     ```

4. **Edit Encrypted Files**:
   - To edit an encrypted file, use the `ansible-vault edit` command:
     ```sh
     ansible-vault edit hosts/prod/group_vars/all.yml
     ```
   - The `vault_password_file` will be used to decrypt the file for editing.

5. **Rekey the Vault Password (Optional)**:
   - If you need to change the vault password, use the `ansible-vault rekey` command:
     ```sh
     ansible-vault rekey hosts/prod/group_vars/all.yml
     ```

### Notes:
- Ensure the `vault_password_file` is not included in version control (e.g., add it to `.gitignore`).
- The `vault_password_file` simplifies automation by avoiding manual password entry but must be secured to prevent unauthorized access.

## YML Linting

```sh
pip3 install --upgrade --user yamllint
```

```sh
echo 'export PATH=$PATH:~/.local/bin' >> ~/.bashrc
source ~/.bashrc
```

```sh
yamllint .
```


## Encrypt string
Here is the updated all.yml file with sensitive information (like `splunk_admin_password`) encrypted using Ansible Vault:

```yaml
---
splunk_uri_lm: https://rh8-ds-1:8089
ansible_user: root
ansible_ssh_private_key_file: ~/.ssh/id_ed25519
git_server: ssh://git@github.com:Zephynyah/splunk-ansible-apps.git
git_key: ~/.ssh/ansible
git_project: SOMEPROJECT
splunk_admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          <encrypted_password_here>
```

### Steps to Encrypt the Password:
1. Use the `ansible-vault encrypt_string` command to encrypt the `splunk_admin_password`:
   ```bash
   ansible-vault encrypt_string --ask-vault-pass 'Nosw3R&dAcukoqaS*SW=' --name 'splunk_admin_password'
   ```
   This will output the encrypted string, which you can replace in the all.yml file.

2. Replace `<encrypted_password_here>` with the encrypted string output from the above command.

3. Save the updated file.

### Notes:
- Ensure your `vault_password_file` is configured in ansible.cfg to avoid being prompted for the vault password during playbook execution.
- Do not commit unencrypted sensitive information to version control.

Let me know if you need further assistance!