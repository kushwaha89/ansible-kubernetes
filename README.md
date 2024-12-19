# Ansible Kubernetes

An Ansible playbook designed to automate the deployment of a Kubernetes cluster, featuring a single master node and multiple worker nodes. It is highly scalable, allowing you to specify and add as many worker nodes as required in the inventory file. Deployment can be completed within 5-7 minutes for a basic setup with one master and worker node.

---

## Features

- Deploy a Kubernetes cluster with:
  - One master node
  - Configurable number of worker nodes
- Configures prerequisites:
  - Disables swap
  - Configures kernel parameters for Kubernetes
- Installs essential Kubernetes components:
  - Containerd runtime
  - Kubeadm, Kubectl, Kubelet
- Initializes the Kubernetes cluster on the master node.
- Automatically joins worker nodes to the Kubernetes cluster.
- Applies Calico network plugin for pod networking.

---

## Prerequisites

1. **Supported(Verified) OS**: Ubuntu 22.04. However, It should work on later version of Ubuntu
2. **Ansible**: Ensure Ansible is installed on the control node.
3. **Access**: Passwordless SSH access to all target nodes (master and workers).
4. **Internet Connectivity**: Required for downloading necessary packages and Kubernetes components.

---

## Inventory File

Update the `inventory.ini` file to define the master and worker nodes:

```ini
[master-nodes]
master-node1 ansible_host=192.168.122.151  # Replace with your master node IP or hostname

[worker-nodes]
worker-node1 ansible_host=192.168.122.217 # Replace with your worker node IP or hostname


## Usage

### 1. Clone the Repository
```bash
https://github.com/kushwaha89/ansible-kubernetes.git
cd ansible-kubernetes
```


### Run the Playbook
Execute the playbook to set up the Kubernetes cluster:
```bash
ansible-playbook -i inventory.ini k8s-cluster.yaml
```

---

## Workflow

1. **Disable Swap**: Disables swap and updates `/etc/fstab` to ensure it remains disabled after reboot.
2. **Kernel Parameters**: Configures necessary kernel parameters for Kubernetes.
3. **Install Containerd Runtime**: Installs and configures the containerd runtime.
4. **Install Kubernetes Components**: Installs Kubeadm, Kubectl, and Kubelet.
5. **Initialize Kubernetes Cluster** (Master Node):
   - Initializes the Kubernetes control plane.
   - Sets up `kubeconfig` for accessing the cluster.
   - Applies the Calico network plugin.
6. **Join Worker Nodes**: Automatically joins worker nodes to the master node.

---

## Example Output

After the playbook completes, verify the cluster status:
```bash
kubectl get nodes
```

Expected output:
```plaintext
NAME             STATUS   ROLES           AGE   VERSION
master-node1     Ready    control-plane   10m   v1.32.x
worker-node1     Ready    <none>          5m    v1.32.x
worker-node2     Ready    <none>          5m    v1.32.x
```

---

## Customization

### Add More Worker Nodes
1. Update the `inventory.ini` file with additional worker nodes.
2. Re-run the playbook to configure the new nodes and join them to the cluster.

### Change Network Plugin
To use a different network plugin, update the plugin manifest URL in the playbook:
```yaml
 - name: Apply Calico networking plugin
   shell: |
     kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

```

---

## Troubleshooting

- **Kubelet Fails to Start**: Ensure swap is disabled and `containerd` is running.
- **Worker Node Fails to Join**: Verify the token and CA certificate hash. Recreate the join command if necessary:
  ```bash
  kubeadm token create --print-join-command
  ```

---

## Contributing

Contributions are welcome! Feel free to open issues or submit pull requests to enhance the playbook.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

