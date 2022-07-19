# Kubernetes Cluster & Dashboard Task
## Installing Kubernetes Cluster using Ansible


Installing Ansible on the Provisioning node/master node, provided these two requirements modules are already installed,

-  Python3
-  Openssh


To install Ansible,
```sh
sudo apt install ansible
```

Then since we are doing it locally, we can use DNS resolvers to manage the servers rather remembering the IP’s 

```sh
sudo nano /etc/hosts
```

update the below entries,
```sh
<IP_address of the provision machine> provision.server.com
<IP_address of the master machine> master.server.com
<IP_address of the node machine> node.server1.com
```

![My Remote Image](https://drive.google.com/uc?id=1tJVqewdmJh3LPXwYrw0xokO8cmoQgv8I)

Next to configure all the nodes to connect via SSH using key authentication,

To generate keygen run the following command,


```sh
ssh-keygen
```


To copy the id to the machines via ssh,

```sh
ssh-copy-id master.server.com
ssh-copy-id node.server1.com
```


To begin the orchestration using ###### Ansible, Modify the inventory file to the path where this folder is downloaded,

```sh
[defaults]
inventory = PATH of Downloaded folder
```

Then in Hosts file modify the names according to your host file names,

```sh
[masters]
master.server.com
[workers]
node.server1.com
```

To run a playbook, use the following syntax,

```sh
ansible-playbook <playbook_name>
```

### Pre-requisites.yml:
It has the below requisites that needs to be done and installed to create a Kubernetes cluster

##### Prerequisites:

-	Disabling Firewall
-	Disabling Swap
-	Updating Sysctl for Kubernetes Networking

Proceed with Installation Below Tools,

-	Docker
-	Kubernetes


### Master_Conf.yml:

It does the configuration related to the master server that includes


- Initializing the Kubernetes Cluster
- Creating the kube directory
- Copying admin configuration to user kube configuration
- Installing Calico Pod network

##### Note: 
> Note: `if kubeadm initiation Failed,` kindly follow the below link to resolve the issue,
[Initialization Issue Fix by Sobyte](https://www.sobyte.net/post/2022-03/connect-connection-refused/) 


### Workers_Conf.yml:

Once master server is successfully configured and pod is initialized, we need to configure the workers to be group as cluster, this takes care of,

- 	Creating a Kubeadm Token 
- 	Joining the workers to the master node

Once the above playbooks are successfully completed, Verify the installation using the below commands, (Give some time to get the clusters ready!)

To get Nodes status

```sh
kubectl get nodes
```

To get Components status

```sh
kubectl get cs
```

![My Remote Image](https://drive.google.com/uc?id=1Mhxgs5TPebQNUW2ZpK_ktx-h8NC6f_rk)


##### Deploying Instana- Dashboard and Load Generator Tool:

First, we need to sign up to get Instana agents for Kubernetes, using below link

##### https://www.instana.com/trial/
Then install Helm using the below command (If it’s not already installed),

```sh
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

```

##### Then login in to your Instana Account--> Deploy Agent-->Kubernetes

![My Remote Image](https://drive.google.com/uc?id=1H0-lGaNLodqhiCP5rsm63Db_hrUf4T2_)


To get the below snippet to insert into the master to install Instana Agent,

![My Remote Image](https://drive.google.com/uc?id=1P9Ls1uF1p_CgT_QbAqY6eOSffmu4D_bs)


Using IBM’s Instana Robot-shop sample microservice we can use that to deploy into our cluster,
To get the Installation instruction,

##### Sample Microservice installation using Instana 
 [Sample Microservice installation using Instana](https://www.instana.com/blog/sample-microservices-application-kubernetes/) 
- 	Download the Git Repo
-  Using the above instructions, we can deploy a microservice to the Kubernetes
-  Or we can load-generator shell file to generate the load in Load-generator directory


### Management Dashboard:

![My Remote Image](https://drive.google.com/uc?id=1dTzAf2ptVtGMsLMQvMb_QVKngh_zVZhw)

### Installing Kubernetes Dashboard for CPU and Memory management:

If one wants the default Kubernetes Dashboard, it can be installed by following the installation instructions from [Ikarus.sg](https://ikarus.sg/k8s-dashboard/) 

First Downloading the deployments files using below command

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.4/aio/deploy/recommended.yaml
```

Copying the Admin_user.yml to local machine and running it on the machine using the below command

```sh
kubectl apply -f ./dashboard-adminuser.yml
```


Once the service account is created the token for the admin-user is obtained using the below command,
```sh
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```

> Note: `Copy the Token and save it for later use` 


### Accessing the Dashboard:

To access the dashboard securely, one must initiate a proxy server,
```sh
kubectl proxy
```


To Access the dashboard, Use the Following link on browser,
```sh
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```


#### Paste the copied token,

![My Remote Image](https://drive.google.com/uc?id=1vGB7tZeMnBV1LDZ77DVf6IZmPdMai-dz)


#### Master Host Dashboard:

![My Remote Image](https://drive.google.com/uc?id=1-W84f7qHpvczNwRodSmS3-5gFHRAehRx)


#### Node Machine:


![My Remote Image](https://drive.google.com/uc?id=10QVUnUl8g5xCAmk3AvhGsysTZC-zdTc8)



### References:
- [Installing Kubernetes Cluster using kubeadm](https://github.com/justmeandopensource/kubernetes/blob/master/docs/install-cluster-ubuntu-20.md) 
- [NetworkNuts Training Videos](https://www.youtube.com/results?search_query=Network+nuts) 
- [Sample Microservice installation using Instana](https://www.instana.com/blog/sample-microservices-application-kubernetes/)
- [Perfromance Test Microservices on Kubernetes](https://developers.redhat.com/articles/2021/12/16/elegant-way-performance-test-microservices-kubernetes) 
- [Ansible Documentation](https://docs.ansible.com/) 
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
