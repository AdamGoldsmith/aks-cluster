aks-cluster
===========

Deploy an Azure AKS cluster from a Jenkins pipeline build by doing the following
* Run Jenkinsfile pipeline from SCM (store Azure credentials in Jenkins)
* Run ansible playbook aks-cluster.yml (--extra-vars "input_name" defines the RG & cluster names [defaults to "demo"])
  * Create **RG-<input_name>** resource group
  * Create **AKS-cluster-<input_name>** cluster using the above RG

Currently tested on these Operating Systems
* RHEL/CentOS

Requirements
------------

* Azure subscription
* ansible[azure]

Store the following information in Jenkins credentials

* az_subscription_id
* az_client_id
* az_secret
* az_tenant

Variables
---------

```
input_name          # Suffix for RG & cluster names
resource_group      # Azure Resource Group name
location            # Azure location
vm_count            # Number of nodes per cluster
vm_size             # Azure VM sie offering
```

Dependencies
------------

None

License
-------

BSD

Author Information
------------------

Adam Goldsmith (adam.goldsmith75@gmail.com)



