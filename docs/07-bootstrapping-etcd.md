# Bootstrapping the etcd Cluster

Kubernetes components are stateless and store cluster state in [etcd](https://github.com/etcd-io/etcd). In this lab you will bootstrap a three node etcd cluster and configure it for high availability and secure remote access.

## Prerequisites

Copy `etcd` binaries and systemd unit files to the `server` instance:

```bash
scp \
  -i ~/.ssh/server_key.pem \
  downloads/etcd-v3.4.34-linux-amd64.tar.gz \
  units/etcd.service \
  azureuser@server:~/
```

The commands in this lab must be run on the `server` machine. Login to the `server` machine using the `ssh` command. Example:

```bash
ssh azureuser@server
```

## Bootstrapping an etcd Cluster

### Install the etcd Binaries

Extract and install the `etcd` server and the `etcdctl` command line utility:

```bash
{
  tar -xvf etcd-v3.4.34-linux-amd64.tar.gz
  sudo mv etcd-v3.4.34-linux-amd64/etcd* /usr/local/bin/
}
```

### Configure the etcd Server

```bash
{
  sudo mkdir -p /etc/etcd /var/lib/etcd
  sudo chmod 700 /var/lib/etcd
  sudo cp ca.crt kube-api-server.key kube-api-server.crt \
    /etc/etcd/
}
```

Each etcd member must have a unique name within an etcd cluster. Set the etcd name to match the hostname of the current compute instance:

Create the `etcd.service` systemd unit file:

```bash
sudo mv etcd.service /etc/systemd/system/
```

### Start the etcd Server

```bash
{
  ssh -i ~/.ssh/server_key.pem azureuser@server "sudo systemctl daemon-reload"
  ssh -i ~/.ssh/server_key.pem azureuser@server "sudo systemctl enable etcd"
  ssh -i ~/.ssh/server_key.pem azureuser@server "sudo systemctl start etcd"
}
```

## Verification

List the etcd cluster members:

```bash
etcdctl member list
```

```text
6702b0a34e2cfd39, started, controller, http://127.0.0.1:2380, http://127.0.0.1:2379, false
```

Next: [Bootstrapping the Kubernetes Control Plane](08-bootstrapping-kubernetes-controllers.md)
