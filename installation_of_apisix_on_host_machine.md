### Installation of Apache APISIX Directly on a Host Machine

Follow these steps to install Apache APISIX on a host machine without using Docker.

### Step 1: SSH into the APISIX VM

Access your VM via SSH to begin the installation process.

### Step 2: Install etcd on the VM

From the home directory (`~`), run the following commands to download and install etcd:

```bash
ETCD_VERSION='3.5.4'

wget https://github.com/etcd-io/etcd/releases/download/v${ETCD_VERSION}/etcd-v${ETCD_VERSION}-linux-amd64.tar.gz

tar -xvf etcd-v${ETCD_VERSION}-linux-amd64.tar.gz && \
cd etcd-v${ETCD_VERSION}-linux-amd64 && \
sudo cp -a etcd etcdctl /usr/bin/

nohup etcd >/tmp/etcd.log 2>&1 &
```

### Step 3: Install Apache APISIX

From the home directory (`~`), run the following commands to install APISIX:

```bash
wget -O - http://repos.apiseven.com/pubkey.gpg | sudo apt-key add -

echo "deb http://repos.apiseven.com/packages/debian bullseye main" | sudo tee /etc/apt/sources.list.d/apisix.list

sudo apt update

sudo apt install -y apisix=3.8.0-0
```

### Step 4: Initialize and Start APISIX

Run the following command to initialize APISIX:

```bash
sudo apisix init
```

Start APISIX with:

```bash
sudo apisix start
```

---

This guide provides the detailed steps needed to install and configure Apache APISIX directly on a host machine.
