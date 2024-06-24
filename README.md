# cfl-summit-lab

## Lab objectives:

In this lab session we will aim to complete three exercises: 

1. Install Rancher on an RKE2 cluster
2. Provision a downstream RKE2 cluster using Rancher
3. Deploy an application using Fleet

Bonus rounds:
4. Take an etcd snapshot of the downstream cluster
5. Deploy an application using the HelmChart controller
6. Restore the etcd snapshot taken in 4. to revert the app deployment

## Lab steps

### Pre-work

- Obtain your node IPs and private SSH keys from the instructor, these will be your nodes to use throughout the lab session
  - There are two node IPs and private key files:
    - The node with `r` is the Rancher node used in exercise 1
    - The node with `ds` is the downstream node used in exercise 2
- Clone this repo to either your laptop or the Rancher node (`r`): `git clone https://github.com/rancherlabs/cfl-summit-lab.git`

### 1 - Install Rancher

Navigate to the Rancher install guide in the link below:
  * https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/install-upgrade-on-a-kubernetes-cluster#install-the-rancher-helm-chart

1. SSH into the node (with `r` in the name), *note* you can use `sudo` once logged in
2. Follow the steps to add the helm repo and create the `cattle-system` namespace. Choose the `latest` helm repository
3. From step 3 onwards, in this lab the "Rancher-generated TLS certificate" approach is recommended, where cert-manager will need to be installed (step 4)
4. For step 5, a hostname is needed for Rancher. You can use your own custom domain name, however for lab purposes, it is recommended to generate a quick hostname with `.nip.io`, just append this to your Rancher node Public IP: eg, `5.6.7.8.nip.io`
   1. If you are unsure about the Public IP, use: `curl ifconfig.io` from the command line on the node
   2. Use this hostname for the `--set hostname` flag when running `helm install`, eg: `--set hostname=5.6.7.8.nip.io`
   3. Supply a bootstrap password to use when logging into Rancher for the first time with the `--set bootstrapPassword` flag
   4. Replace the `<CHART_REPO>` portion of the `helm install` command with `latest`, if latest was the repo chosen in the first step of this exercise
   5. Add the `--set replicas 1` flag, to use just one Rancher pod, as we're running a single node for this lab
   
   Example command:
   ```
   helm install rancher rancher-latest/rancher --namespace cattle-system --set hostname=35.88.67.40.nip.io --set replicas 1
   ```

### 2 - Create a downstream cluster (Rancher Dashboard)

Navigate to the Downstream cluster configuration guide in the link below:
  * https://ranchermanager.docs.rancher.com/reference-guides/cluster-configuration/rancher-server-configuration/use-existing-nodes

1. SSH into the node (with `ds` in the name), *note* you can use `sudo` once logged in
2. Follow the steps in the configuration guide (link above) from step 2 (Create the **Custom Cluster**)
3. At the end of step 2 you will use this node (with `ds` in the name) to run the provisioning command
4. Step 3 (Tag Resources) can be ignored

### 2a - Create a downstream cluster (terraform)

Change directory to the 2a-downstream-cluster directory of the cloned repo

1. Update the terraform.tfvars file to update the required variables (aws credentials, region, url, token, vpc details etc.)
2. Initialise the terraform modules: `terraform init`
3. Create the resources: `terraform apply`

Clean up: `terraform destroy`

### 3 - Take an etcd snapshot

Take an etcd snapshot of the downstream cluster(s). Details in the link below:
  * https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/backup-restore-and-disaster-recovery/back-up-rancher-launched-kubernetes-clusters#one-time-snapshots

### 4 - Deploy an application using Fleet

- Details TBA

### 5 - Deploy an application using a HelmChart (Add-On Config)

Navigate to Cluster Management in the Rancher dashboard

1. Click Edit Config on a downstream cluster
2. Navigate to Add-On Config under Cluster Configuration
3. Scroll down to Additional Manifest
4. Paste in a HelmChart, or Kubernetes manifest

Example: https://docs.rke2.io/helm#using-the-helm-crd

---

# Instructors only

See `pre-work` directory for preparation of the initial lab environment
