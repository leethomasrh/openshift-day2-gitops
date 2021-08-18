# Openshift-Ansible-Automation

This playbook can be used to perform an ipi installation and configure day2 tasks via the openshift-gitops operator.  
It is possible to use these playbooks on many clusters by creating a {repo folder}/vars/{cluster_name} directory to
contain the vars.yml and vault.yml for each cluster.  

## GENERAL
These playbooks use the following python3 modules.  They need to be installed prior to running the automation.
	
* urllib3
* requests
* requests-oauthlib
* openshift (version greater than 0.6)
* PyYAML (version greater than 3.11)
        
To install these modules you can use:
  pip3 install <module> --user

The playbooks use the vars.yml and vault.yml files within a directory under vars matching the name 
of each openshift cluster you want to administer using these playbooks.

If you wish to create a vault-pass.txt file in the playbook directory that can be used to prevent prompts for the vault
password when the playbook adds the kubeadmin password for the newly built cluster using the ipi_install role.

**Do not commit an unencrypted vault.yml file to Github.**  The example-vault.yml file is just an example and should not be filled in.
**A .gitignore file has been created to prevent any files with vault or vlt in the name from being commited to github.  Update this
file if your naming convention differs.

If a {repo folder}/vars/{cluster_name}/vault.yml doesn't exist create one with
	
	ansible-vault create vault.yml 

If a {repo folder}/vars/{cluster_name}/vault.yml exists edit it using: 
	
	ansible-vault edit vault.yml

The values for the vsphere variables can be found in the install-config.yaml spec.workspace stanza 
	
or by running the following command if you already have a cluster built:
  
	oc get machines <master machine name> -o yaml

Recommend running the openshift-install create install-config --dir=<installation dir> command prior to running the ipi install role.
This will allow you to ensure all the options presented during the ipi install are selected for the build you wish to do and will 
provide the information you need to fill in the variables in the {repo folder}/vars/{cluster_name}/vars.yml file.
If you select an option that isn't in the {repo folder}/roles/ipi_install/templates/install-config.j2 file you can add it 
along with the cooresponding variable in the {repo folder}/vars/{cluster_name}/vars.yml file.

### USAGE:
** prompting for vault password (no vault-pass.txt file)
	./playbook.yml --ask-vault-pass -e cluster_name={cluster_name} -t {role name}
** using the vault-pass.txt file 
	./playbook.yml --vault-password-file vault-pass.txt -e cluster_name={cluster_name} -t {role name}

**Example:**
	
	Perform IPI install:
	./playbook.yml --vault-password-file vault-pass.txt -e cluster_name={cluster_name} -t ipi_install
        Install Openshift Gitops operator
        ./playbook.yml --vault-password-file vault-pass.txt -e cluster_name={cluster_name} -t gitops_operator
