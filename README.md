# xray-matryoshka

Automated Ansible deployment for a 3-node cascaded Xray proxy (`Entry` ➔ `Transit` ➔ `Exit`) with VLESS + REALITY.

## ✨ Highlights

- **3-Node Cascade:** Obfuscated inter-server communication, domainless (no TLS certs needed).
- **Selective WARP Outbound:** Route specific AI / geo-restricted services via Cloudflare WARP on the Exit node.
- **Auto Subscription:** Automatic sync of base64 VLESS links to a secret GitHub Gist.

## 🚀 Quick Start

### 1. Requirements
- 3 Debian/Ubuntu servers with root SSH key access.
- Ansible installed on your control machine.

### 2. Setup Config & Inventory

```bash
git clone https://github.com/your-username/xray-matryoshka.git && cd xray-matryoshka
cp examples/xray.yml group_vars/matryoshka/xray.yml
cp inventory.ini.example inventory.ini
```

### 3. Generate Keys & Fill `group_vars/matryoshka/xray.yml`

```bash
# 3x UUIDs (Entry, Transit, Exit)
uuidgen

# 2x REALITY Key Pairs (Entry, Transit)
docker run --rm teddysun/xray xray x25519

# 2x Short IDs
openssl rand -hex 8

# Install wgcf (Cloudflare WARP CLI)
curl -fsSL git.io/wgcf.sh | sudo bash

# Register and generate profile
wgcf register --accept-tos
wgcf generate
cat wgcf-profile.conf
```

### 4. Deploy

```bash
ansible-playbook -i inventory.ini matryoshka.yml
```

## 📬 Optional: GitHub Gist Subscription Setup

1. Create a secret Gist at [gist.github.com](https://gist.github.com) with a file named `sub.txt`.
2. Generate a GitHub PAT (Fine-grained) with `gist` scope.
3. Fill `github_gist_token`, `github_gist_id`, and `github_gist_user` in `xray.yml`.
4. Subscription URL format for clients:
   `https://gist.githubusercontent.com/{user}/{gist_id}/raw/sub.txt`

To update the subscription link only:

```bash
ansible-playbook -i inventory.ini subscription.yml
```

## 📜 License

MIT — see [LICENSE](LICENSE)
