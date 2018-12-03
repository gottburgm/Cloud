### OpenShift Cloud

There is few tips/notes that may help during a setup of a cloud using openshift-ansible.
This example refer to the setup that i've made using 3 "slaves" PCs, 1 "master" PC and finnaly my PC from which
i did the different tasks but which is not part of the cloud itselft.


#### Master

   - Master: bela1.gva.redhat.com


#### Salves

  - Slave 1 : bela2.gva.redhat.com
  - Slave 2 : bela3.gva.redhat.com
  - Slave 3 : bela4.gva.redhat.com



#### Inventory File
This is the file containing the main configuration of your cloud.
There is some imporant sections to configure:


  - ##### [masters]
  
    ```
      [masters]
      bela1.gva.redhat.com
    ```
  
  
  - ##### [etcd]
  
    ```
      [etcd]
      bela1.gva.redhat.com
    ```
  
  

  - ##### [nodes]
    
    ```
      [nodes]
      # Master SHOULD be in the nodes
      bela1.gva.redhat.com openshift_node_group_name="node-config-master"
       
      # And all the other slaves also
      bela2.gva.redhat.com openshift_node_group_name="node-config-compute"
      bela3.gva.redhat.com openshift_node_group_name="node-config-compute"
       
      # A node/slave to host some infrastructure pods       
      bela4.gva.redhat.com openshift_node_group_name="node-config-infra"
    ```
 

  - ##### [nfs]
  
    ```
       [nfs]
       bela1.gva.redhat.com
    ```
  
  
  - ##### [OSEv3:vars]
    
    ```
       [OSEv3:vars]
       # Bypass a test against slaves memory ...
       openshift_disable_check=memory_availability
       
       ansible_user=root
       openshift_deployment_type=origin
    ```
#### Deployment (from the pc which is not part of the cloud)
Clone the openshift-ansible directory:

```bash
   git clone https://github.com/openshift/openshift-ansible
   git checkout release-3.10
   git pull
```

Create your inventory file using the example file and following the process described before:

```bash
   cp inventory/hosts.example inventory/<inventory_file>
   nano inventory/<inventory_file>
```

Don't forget to run the uninstall playbook before any retry if there was error(s) during previous deployment/installation:

```bash
   ansible-playbook -i inventory/hosts.cloud.redhat playbooks/adhoc/uninstall.yml
```
  
Otherwhise you can directly run the deployment using the following commands (from the pc which is not part of the cloud) 

```bash
    ansible-playbook -i inventory/<inventory_file> playbooks/prerequisites.yml
    ansible-playbook -i inventory/<inventory_file> playbooks/deploy_cluster.yml
```

If any errors occured it should be good !
