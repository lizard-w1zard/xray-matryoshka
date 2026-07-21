# xray-matryoshka

Automated Ansible deployment for a multi-node Xray proxy cascade (VLESS+REALITY).

Sets up a secure Entry→Transit→Exit routing scheme with gRPC transport to protect against DPI-based connection counting.

No TLS certificates or domains required. Perfect for maintaining stable and private self-hosted infrastructure.

## 📌 About

This project automates the deployment of a 3-node network topology (Entry ➔ Transit ➔ Exit) using [Xray-core](https://github.com/XTLS/Xray-core) under the hood.

It leverages the REALITY protocol to obfuscate server-to-server communication, ensuring high resilience and data privacy across your infrastructure without the need for external domains.

## ✨ Features

✅ **3-Node Cascaded Routing:** Entry → Transit → Exit segmented traffic flow.

✅ **HAProxy connection pooler:** The Entry node has its own connection pooler for flexible connection serving.

✅ **Xray (VLESS + REALITY + gRPC):** Secure, obfuscated tunnels with HTTP/2 multiplexing — one TLS handshake per session instead of one per browser connection.

✅ **Fully Automated:** Deploy to multiple servers in minutes via Ansible.

✅ **Domainless:** No TLS certificates or registered domains required.

✅ **GitHub Gist Subscription:** Client connection links are automatically pushed to a secret GitHub Gist on every deploy. Add the URL once to any client — updates propagate automatically.

## 📦 Requirements

- Control machine with **Ansible** installed.
- 3 target servers running **Debian/Ubuntu**.
- **Root SSH access** to all target servers.
- **GitHub account** for subscription delivery.

## 🔑 Prerequisite: SSH Key Setup

Before running the playbook, your control machine's public SSH key must be added to the `~/.ssh/authorized_keys` file of the `root` user on all three target servers.

> *If you are new to SSH keys, follow this simple public guide:*
> [**How to Set Up SSH Keys (DigitalOcean)**](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2)

## 🚀 Quick Start

**1. Clone the repository**

```bash
git clone https://github.com/your-username/xray-matryoshka.git
cd xray-matryoshka
```

**2. Configure Xray variables**

```bash
cp examples/xray.yml group_vars/matryoshka/xray.yml
```

**3. Generate cryptographic keys**

*3 UUIDs (for Entry, Transit, and Exit nodes):*
```bash
uuidgen
```

*2 REALITY key pairs (Entry↔Transit and Transit↔Exit):*
```bash
docker run --rm teddysun/xray xray x25519
```

*2 Short IDs:*
```bash
openssl rand -hex 8
```

Fill in `group_vars/matryoshka/xray.yml` with the generated values.

**4. Configure inventory**

```bash
cp inventory.ini.example inventory.ini
```

Fill in `ansible_host` with your server IP addresses.

**5. Deploy**

```bash
ansible-playbook -i inventory.ini matryoshka.yml
```

## 📬 Subscription

Client connection links are delivered via a **secret GitHub Gist** — HTTPS by default, no extra infrastructure required.

**Setup (one time):**

1. Create a secret Gist at [gist.github.com](https://gist.github.com) with a single file named `sub.txt`.
2. Copy the Gist ID from the URL: `gist.github.com/{user}/{ID}`.
3. Create a GitHub Personal Access Token with only the `gist` scope:
   GitHub → Settings → Developer settings → Personal access tokens.
4. Configure the subscription variables:

```bash
cp examples/gist.yml group_vars/matryoshka/gist.yml
```

Fill in `group_vars/matryoshka/gist.yml` with your token, gist ID, and username. This file is gitignored — never commit real values.

**How it works:**

The `update-subscription` role pushes the current VLESS link (base64-encoded) to your Gist via the GitHub API. Clients that have the raw Gist URL as a subscription source receive the update automatically on their next refresh.

Subscription URL format:
```
https://gist.githubusercontent.com/{user}/{gist_id}/raw/sub.txt
```

To run only the subscription update without redeploying servers:
```bash
ansible-playbook -i inventory.ini subscription.yml
```

## 📜 License

MIT — see [LICENSE](LICENSE)

## 🛡️ Disclaimer

This project is provided for educational, testing, and private infrastructure management purposes only. Use at your own risk. Respect the terms of service of your hosting providers and the laws of your jurisdiction.
