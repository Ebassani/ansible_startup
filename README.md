# Ansible Installation and Usage on Fedora Workstation

## Installing Ansible

To install Ansible on your Fedora workstation, follow these steps:

1. **Update your system:**
    ```bash
    sudo dnf update -y
    ```

2. **Install Ansible:**
    ```bash
    sudo dnf install ansible -y
    ```

3. **Verify the installation:**
    ```bash
    ansible --version
    ```

## Running Ansible on Localhost

To run Ansible on your local machine, you need to configure the inventory and create a playbook.

1. **Run the playbook:**
    ```bash
    ansible-playbook -i inventory.ini playbook.yaml
    ```

This will run the playbook on your local machine, ensuring that your system is updated.
