# AWS-Kubernetes-Cluster-MasterNWorker-Nods-t3a-medium

AWS Kubernetes Cluster (Master & Worker Nodes t3a.medium)
This project sets up a Kubernetes cluster on AWS using EC2 instances of type t3a.medium for both master and worker nodes. This setup is ideal for small to medium-sized workloads and leverages AWS services for cloud-based infrastructure.

Overview
This repository provides an automated way to deploy a Kubernetes cluster with a master node and worker nodes on AWS. The master node manages the cluster, while the worker nodes run the containers that form your application. We use t3a.medium instances for cost-effective performance.

Technologies Used:
AWS EC2 for provisioning virtual machines.
Kubernetes for container orchestration.
t3a.medium instances for optimal balance between price and performance.
Prerequisites
Before getting started, ensure you have the following:

AWS Account with IAM permissions to create EC2 instances, VPCs, security groups, etc.
AWS CLI installed and configured with your credentials.
kubectl installed to interact with your Kubernetes cluster.
Terraform or CloudFormation (optional) if automating infrastructure setup.
SSH Access to the EC2 instances (key pair must be created beforehand).
Setup Instructions


Step 1: Configure AWS CLI
Make sure the AWS CLI is configured with the appropriate credentials:


aws configure


Step 2: Launch EC2 Instances
Provision t3a.medium instances for the Kubernetes master and worker nodes using the AWS Console or through automation (e.g., Terraform).

Master Node: Ensure this instance is set up with sufficient storage and networking capabilities.
Worker Nodes: Launch multiple instances, depending on your cluster size needs.


Step 3: Install Kubernetes on EC2 Instances
SSH into the master node:


ssh -i /path/to/your-key.pem ec2-user@<master-node-ip>
Install Kubernetes packages (kubeadm, kubelet, kubectl):


sudo apt update
sudo apt install -y kubelet kubeadm kubectl
Initialize Kubernetes on the master node:


sudo kubeadm init --pod-network-cidr=192.168.0.0/16
Set up the cluster configuration on the master node:


mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
Install a pod network (e.g., Flannel or Calico):


kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml


Step 4: Join Worker Nodes
SSH into each worker node and install Kubernetes:


ssh -i /path/to/your-key.pem ec2-user@<worker-node-ip>
sudo apt install -y kubelet kubeadm kubectl
Join the worker nodes to the master node using the kubeadm join command obtained from the master node during initialization.

Step 5: Verify Cluster
Ensure that all nodes are connected and running:

bash
Copy code
kubectl get nodes
You should see the master and worker nodes listed in the output.

Scaling the Cluster
To scale the cluster up or down, you can modify the number of worker nodes by adding or terminating EC2 instances. After launching new worker nodes, simply run the kubeadm join command to attach them to the cluster.

Cleanup
To tear down the cluster:

Drain and remove the worker nodes from the cluster:

bash
Copy code
kubectl drain <worker-node-name> --delete-local-data --force --ignore-daemonsets
kubectl delete node <worker-node-name>
Terminate the EC2 instances from the AWS console or CLI.

Future Improvements
Auto-scaling: Implement AWS auto-scaling for worker nodes to dynamically handle workloads.
Load Balancer Integration: Add a load balancer (e.g., AWS ELB) to distribute traffic between nodes.
Monitoring and Logging: Integrate tools like Prometheus and Grafana for monitoring, and ELK Stack for logging.
Contributing
Feel free to submit pull requests to improve the setup or suggest new features!

License
This project is licensed under the MIT License.
 
