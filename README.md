# DevStack vs PackStack - Quick Notes

## Key Differences

| Aspect | DevStack | PackStack |
|--------|----------|-----------|
| **Purpose** | Development & Testing | POC & Small Production |
| **OS** | Ubuntu/Debian | RHEL/CentOS/Fedora |
| **Method** | Bash Scripts | Puppet Modules |
| **Config File** | local.conf | Answer file |
| **Installation Time** | 15-30 minutes | 30-60 minutes |
| **Persistence** | Temporary (lost on reboot) | Permanent |
| **Production Use** | No | Yes (limited scale) |
| **Best For** | Developers, CI/CD | Admins, Demos |

---

## DevStack Installation

### Setup Stack User
```bash
# Create stack user
sudo useradd -s /bin/bash -d /opt/stack -m stack
echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
sudo su - stack
```

### Clone & Configure
```bash
# Clone repository
git clone https://opendev.org/openstack/devstack
cd devstack

# Create local.conf
cat > local.conf << EOF
[[local|localrc]]
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=\$ADMIN_PASSWORD
RABBIT_PASSWORD=\$ADMIN_PASSWORD
SERVICE_PASSWORD=\$ADMIN_PASSWORD
HOST_IP=<YOUR_IP>
EOF
```

### Install
```bash
# Run installation
./stack.sh

# Source credentials after install
source openrc admin admin
```

### Management Commands
```bash
./stack.sh      # Install/Start
./unstack.sh    # Stop services
./clean.sh      # Remove everything
```

---

## PackStack Installation

### Prerequisites
```bash
# Update system (RHEL/CentOS)
sudo yum update -y

# Install RDO repository
sudo yum install -y centos-release-openstack-yoga

# Disable NetworkManager
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
```

### Install PackStack
```bash
# Install package
sudo yum install -y openstack-packstack

# Generate answer file (optional)
packstack --gen-answer-file=/root/packstack-answers.txt
```

### Run Installation
```bash
# All-in-one quick install
sudo packstack --allinone

# OR with answer file
sudo packstack --answer-file=/root/packstack-answers.txt

# Source credentials after install
source /root/keystonerc_admin
```

---

## PuTTY/SSH Connection

```bash
# Basic SSH connection
ssh user@<ip-address>

# Root access
ssh root@<ip-address>

# With private key
ssh -i /path/to/key.pem user@<ip-address>
```

### System Requirements Check
```bash
nproc          # CPU cores (min 4)
free -h        # RAM (min 8GB)
df -h          # Disk space (min 50GB)
cat /etc/os-release  # OS version
```

---

## When to Use What?

**Use DevStack:**
- Developing OpenStack features
- Quick testing environments
- Latest unreleased code
- Temporary setups

**Use PackStack:**
- Demo environments
- Persistent installations
- Red Hat ecosystem
- Small production deployments
