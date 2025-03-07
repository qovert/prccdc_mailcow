# PRCCDC Mailserver

This project provides Ansible playbooks to join an server to an Active Directory domain and to set up Dockerized Mailcow. It automates the process of installing necessary packages, discovering the domain, joining the domain, configuring the System Security Services Daemon (SSSD), and allowing domain users to use sudo privileges. After all that, it sets up Mailcow using Docker.

## But why though? 
The goal is to create a standardized, reproducible environment for pentesting and blue teaming. The participants will need to tear down and recreate the environment multiple times during the event. 

That being said, this is a **TEST** environment. Everything in here is terrible and insecure. There are passwords and the like just all over the place. Don't use this without heavy modification for anything production. I take no responsibility for the use of this repo, so you have been warned! 

## Project Structure

I've tested this against a fresh **Debian 12** VM running on Proxmox. Primary considerations for this to function are to ensure that it's a minimal install and doesn't have ufw/firewalld running (per Mailcow's docs). I haven't tried other distros or anything downlevel, but it should work as long as you can install docker on it.

```
prccdc-mailcow
├── files
│   ├── docker-compose.override.yml       # Docker compose addition for https://github.com/Programmierus/ldap-mailcow
│   ├── docker-compose.yml                # Dockerized mailcow docker-compose file created via their docs
│   └── mailcow.conf                      # Mailcow config created via generate_config.sh. Might need to create your own.
├── tasks
│   ├── domain_join.yml                   # Playbook to join the server to the Active Directory domain
│   ├── dockerized_mailcow.yml            # Playbook to set up Dockerized Mailcow
│   └── docker_install.yml                # Playbook to make sure docker / docker compose is installed.
├── vars
│   ├── ad_auth.yml           # Some PLAINTEXT AD credentials and info
│   └── ad_auth.yml           # Variables file for Active Directory authentication
├── inventory.ini             # Inventory file with target hosts
└── README.md                 # Documentation for the project
```

## Usage

1. **Prerequisites**: 
   - Ensure that Ansible is installed on your control machine and that you have access to the target server.
   - Sudo at least needs to be installed on the remote system. 

2. **Configuration**: 
   - Modify the `ad_auth.yml` file in the `vars` directory to set the appropriate values for your Active Directory domain, administrator username, and password.
   - Modify the `mailcow.yml` file in the `tasks` directory to set the appropriate values for your Mailcow configuration files.
   - Ensure that the `docker-compose.override.yml` file is modified to reflect your AD info. 
   - Update the `inventory.ini` file with the target host and username.
   - The `mailcow.conf` file contains a pre-set API key and specifies the mailcow-network as allowed. If you need to make changes, that's where to do it.

3. **Run the Playbook**: Execute the playbook using the following command:
   ```
   ansible-playbook -i inventory.ini main.yml
   ```

This will run the tasks defined in the respective playbooks and join the server to the specified Active Directory domain or set up Dockerized Mailcow.