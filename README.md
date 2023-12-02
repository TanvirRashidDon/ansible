# ansible
Learning Ansible 


Install Ansible in Linux.

We’re installing Ansible using a Python module called pip. First let’s check if the pip module is available or not.

python3 -m pip -v


If pip is not available, use below command to install the module.

curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py –user


Install Ansible.
python3 -m pip install -user ansible 


Add the ansible command to sh. First we have to find the binary file. Best way to find the binary is…
cd ~
Find . | grep “bin/ansible”
<omitted lines>
<*>/bin/ansible
<omitted lines>


Edit .bash_profile or .zshrc file and concat the “<*>/bin” in $PATH variable. In my case it was
export PATH=$HOME/.local/bin:$PATH



Configure Ansible.

Ansible will look for the config file in below order and only use the first found file ignoring the rest.
Environment Variable
Current working directory
User’s home directory
System wide configuration file i.e. “/etc/ansible”

Here I’m using the 4th option.
sudo mkdir /etc/ansible
sudo mkdir /etc/ansible/roles


 By default non root users don't have write permission to this directory.
ls -ld .

# change ownership
sudo chown -R /etc/ansible


Init the config file with all plugins. Any config file can be used with -i flag in ansible.
ansible-config init --disabled -t all > /etc/ansible/ansible.cfg


Ansible controllers communicate via ssh. There are 2 options to make the communication possible. 
User pass based authentication
SSL certification based authentication. 

To use user pass based authentication, we need sshpass. We also need to set “host_key_checking=False”
apt-get install sshpass

nano ansible.cfg
# set host_key_checking=True -> host_key_checking=False


Add a dedicated user for each managed nod.
sudo useradd ansible
sudo passwd ansible


Add root privilege to the newly created user.
cat << EOF > /etc/sudoers.d/ansible
ansible ALL = (root) NOPASSWD:ALL
EOF


Give correct file permission.
chmod 0440 /etc/sudoers.d/ansible



For Red Hat Enterprise Linux (and open source release CentOS) we need to enable ssh login as root.
echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config.d/01-permitrootlogin.conf

# Restart the ssh service
systemctl restart network


Basic commands.

ansible linux -m ping # -m -> module
ansible -i ./inventory/hosts linux -m ping


ansible linux -a "cat /etc/os-release"


ansible-playbook ./playbooks/playbook-to-play.yml -i ./inventory/hosts


