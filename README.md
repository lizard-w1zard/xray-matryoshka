# xray-matryoshka

Automated Ansible deployment for a multi-node Xray proxy cascade (VLESS+REALITY). 

Sets up a secure Entry-Transit-Exit routing scheme to protect internal traffic flow.

 No TLS certificates or domains required. Perfect for maintaining stable and private self-hosted infrastructure.

📌 **About**

This project automates the deployment of a 3-node network topology (Entry ➔ Transit ➔ Exit) using [Xray-core](https://github.com/XTLS/Xray-core) under the hood. 

It leverages the REALITY protocol to obfuscate server-to-server communication, ensuring high resilience and data privacy across your infrastructure without the need for external domains.

✨ **Features**

✅ **3-Node Cascaded Routing:** Segmented traffic flow.

✅ **Xray (VLESS + REALITY):** Secure, obfuscated internal tunnels.

✅ **Fully Automated:** Deploy to multiple servers in minutes via Ansible.

✅ **Domainless:** No TLS certificates or registered domains required.

📦 **Requirements**

* Control machine with **Ansible** installed.
* 3 target servers running **Debian/Ubuntu**.
* **Root SSH access** to all target servers.

🔑 **Prerequisite: SSH Key Setup**

Before running the playbook, your control machine's public SSH key must be added to the `~/.ssh/authorized_keys` file of the `root` user on all three target servers. 

> *If you are new to SSH keys, follow this simple public guide:*
> [**How to Set Up SSH Keys (DigitalOcean)**](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2)

🚀 **Quick Start**

**1. Clone the repository**
```bash
git clone https://github.com/your-username/xray-matryoshka.git
cd xray-matryoshka
```

**2. Configure Variables**
Copy the example variables file to the `group_vars` directory:
```bash
cp examples/xray.yml group_vars/matryoshka/xray.yml
```

**3. Generate Cryptographic Keys**
You need to generate unique keys and IDs. You can do this on any machine with Docker or natively installed tools.

*Generate 3 UUIDs (for Entry, Transit, and Exit nodes):*
```bash
uuidgen
# or
cat /proc/sys/kernel/random/uuid
```

*Generate 2 REALITY Key Pairs (Entry->Transit and Transit->Exit):*
```bash
# Using Docker (no local Xray installation required)
docker run --rm teddysun/xray xray x25519
```

*Generate 2 Short IDs (SID):*
```bash
openssl rand -hex 8
```

**4. Apply Your Data**
Open `group_vars/matryoshka/xray.yml` and carefully fill in the variables with your newly generated UUIDs, Keys, and SIDs.

**5. Run the Playbook**
Deploy the infrastructure by running:
```bash
ansible-playbook -i inventory.ini matryoshka.yml
```

📜 **License**

MIT — see [LICENSE](LICENSE)

🛡️ **Disclaimer**

This project is provided for educational, testing, and private infrastructure management purposes only. Use at your own risk. Respect the terms of service of your hosting providers and the laws of your jurisdiction.