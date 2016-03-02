# sbs-deploy
ANSIBLE BASED DEPLOYMENT STEPS FOR SBS:

Install ansible:
$ git clone git://github.com/ansible/ansible.git --recursive
$ cd ./ansible
$ source ./hacking/env-setup
$ sudo easy_install pip

We can set export ANSIBLE_HOST_KEY_CHECKING=false where we will run ansible so that it will be able to do SSH without any prompt.

SBS deployment steps:
	Create a directory for deployment say deploy and “git clone” the following in that directory:
	https://github.com/JioCloud/sbs-config/
	https://github.com/JioCloud/sbs-deploy/
	https://github.com/JioCloud/sbs-deploy-vars/

One shot deployment: 
	1. Specify all the variable files in sbs-deploy-vars directory.
	2. Go to sbs-deploy directory and run the following:
	   ansible-playbook -v deploy_sbs.yaml -i inventory -u block_team

Individual service deployment:

	1. Deploy ceph(go to sbs-deploy/ceph):
	   a. Specify the required variables in vars/ceph_info.yml and other var files (self-explanatory) and add public IPs in [admin] and [nodes] in inventory file.
	   b. deploy ceph:
              ansible-playbook -v deploy_ceph.yaml -i vars/inventory -u block_team
	
   	   c. Manual steps required:
	      add rack info if required

	2. Deploy keepalived(go to sbs-deploy/keepalived/):
	   a. Specify keepalived_VIP in vars/keepalived_info.yml and adding the public IPs of all nodes in [hostList] in inventory file
	   b. deploy keepalived on all nodes:
	      ansible-playbook -v deploy_keepalived.yaml -i vars/inventory -u block_team
		
	3. Deploy galera(go to sbs-deploy/galera/):
	   a. Specify the required variables in vars/galera_info.yml (self-explanatory)..
	   b. To deploy galera on all nodes, add in inventory for each ip, hostname and my_ip and use extra_arg to define --wsrep-new-cluster in case of master host.
	      Run the following command:
	      ansible-playbook -v deploy_galera.yaml -i inventory -u ubuntu
	
	4. Deploy haproxy(go to sbs-deploy/haproxy/):
	   a. Specify cinder,mysql hosts and haproxy_vip in var file and add all the public IPs in [hostList] in inventory file
           b. also define sbs_haproxy_ssl_pem in var file.
	   c. deploy haproxy on all nodes:
 	      ansible-playbook -v deploy_haproxy.yaml -i vars/inventory -u block_team

	5. Deploy cinder and zeromq(go to sbs-deploy/cinder/):

	   Requirement:
		a. it needs ca-certificates, clients, ec2, cinder packages, 
		b. it also need cinder.conf, ceph.conf, api-paste.ini for cinder and cinder-ec2-api.conf, api-paste.ini and ec2api.conf for cinder-ec2-api.
		c. It will deploy cinder and zeromq.

	   Deployment steps:
		a. copy the required things as mentioned above in sbs-config directory.
		b. Specify the required variables in vars/cinder_info.yml and vars/proxy_info.yml (self-explanatory)..
		c. Run to deploy cinder:
		   ansible-playbook -v deploy_cinder.yaml -i vars/inventory -u block_team

