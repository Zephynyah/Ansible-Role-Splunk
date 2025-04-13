
# Ansible Playbooks for Splunk Administration

This directory contains Ansible playbooks designed to manage, install, upgrade, and configure Splunk deployments. These playbooks leverage the `ansible-role-splunk` role to automate tasks for various Splunk components, including Deployment Servers, Search Heads, and Indexers.

## Playbooks Overview

### 1. `1_splunk_install_or_upgrade.yml`
This playbook is used to perform either a Splunk installation or an upgrade on all specified hosts.

#### Features:
- Installs or upgrades Splunk to the specified version.
- Uses the `splunk` role to manage the installation or upgrade process.
- Supports specifying the Splunk package version and build ID.

#### Variables:
- `splunk_package_version`: The version of Splunk to install or upgrade to (e.g., `9.3.0`).
- `build_id`: The build ID for the specified Splunk version (e.g., `51ccf43db5bd`).

#### Example Usage:
```bash
ansible-playbook -i inventory.yml 1_splunk_install_or_upgrade.yml
```

---

### 2. `99_splunk_upgrade_full.yml`
This playbook performs a full upgrade of a Splunk deployment, including the Deployment Server, Search Heads, and Indexers.

#### Features:
- Sequential upgrades for Search Heads and Indexers to minimize downtime.
- Uses the `splunk` role to manage Splunk configurations and tasks.
- Ensures safe upgrades with `serial` execution and `any_errors_fatal` to stop on critical errors.

#### Hosts:
- **`deploymentserver`**: Upgrades the Deployment Server.
- **`search`**: Upgrades Search Heads one at a time.
- **`indexer`**: Upgrades Indexers one at a time.

#### Example Usage:
```bash
ansible-playbook -i inventory.yml 99_splunk_upgrade_full.yml
```

---

## Directory Structure

- **`1_splunk_install_or_upgrade.yml`**: Playbook for installing or upgrading Splunk.
- **`99_splunk_upgrade_full.yml`**: Playbook for upgrading a full Splunk deployment.

---

## Inventory File

The playbooks require an inventory file (`inventory.yml`) to define the Splunk environment. Below is an example inventory file:

```ini
[deploymentserver]
deploymentserver1 ansible_host=192.168.1.10

[search]
searchhead1 ansible_host=192.168.1.20
searchhead2 ansible_host=192.168.1.21

[indexer]
indexer1 ansible_host=192.168.1.30
indexer2 ansible_host=192.168.1.31
```

---

## Variables

The playbooks use the following variables to control behavior:

| Variable               | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| `deployment_task`      | Specifies the task file to include for the Splunk role.                    |
| `splunk_package_version` | The version of Splunk to install or upgrade to (e.g., `9.3.0`).           |
| `build_id`             | The build ID for the specified Splunk version (e.g., `51ccf43db5bd`).      |
| `serial`               | Controls the number of hosts upgraded at a time (e.g., `1` for sequential).|
| `any_errors_fatal`     | Ensures the playbook stops execution if a critical error occurs.           |

---

## Prerequisites

1. **Ansible Installed**:
   Ensure Ansible is installed on the control node. You can install it using:
   ```bash
   sudo apt install ansible  # For Debian/Ubuntu
   sudo yum install ansible  # For CentOS/RedHat
   ```

2. **Splunk Role**:
   Ensure the `splunk` role is installed and configured in your Ansible roles directory.

3. **Inventory File**:
   Define your inventory file (`inventory.yml`) with the appropriate groups (`deploymentserver`, `search`, `indexer`, etc.).

---

## How to Run

1. **Run the Installation or Upgrade Playbook**:
   Execute the playbook using the following command:
   ```bash
   ansible-playbook -i inventory.yml 1_splunk_install_or_upgrade.yml
   ```

2. **Run the Full Upgrade Playbook**:
   Execute the playbook using the following command:
   ```bash
   ansible-playbook -i inventory.yml 99_splunk_upgrade_full.yml
   ```

3. **Monitor Execution**:
   - The playbooks will sequentially upgrade or install Splunk on the specified hosts.
   - If any critical error occurs, the playbook will stop execution.

---

## Notes

- **Serial Execution**:
  The `serial` directive ensures that Search Heads and Indexers are upgraded or installed one at a time to minimize downtime and avoid cluster disruptions.

- **Error Handling**:
  The `any_errors_fatal: true` directive ensures that the playbook stops execution if a critical error occurs during the process.

- **Custom Tasks**:
  You can customize the `deployment_task` variable to include specific tasks for each Splunk component.

---

## License

This project is licensed under the Apache 2.0 License. See the `LICENSE` file for details.

---

## Author Information

This playbook was created by [Your Name]. For questions or support, please contact [your_email@example.com].
```

### How to Use:
1. Save this content as README.md in the playbooks directory.
2. Update the `Author Information` section with your details.
3. Add any additional playbooks to the directory and update the README accordingly.

Let me know if you need further adjustments!