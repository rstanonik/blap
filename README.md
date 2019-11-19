# Starlight Local Tasks

The two files, `local-post-tasks.yml` and `local-pre-tasks.yml` need to be
symlinked into [the Project Surfliner monorepo][SL]'s `ansible` directory for
a successful deployment on UC San Diego infrastructure.

=============== From Soup to Nuts ==============
create rhel7 vm
  clone vm from template
  create infoblox with MAC
  create AD host entry
  git clone git@github.com:ucsdlib/ops-ansible-playbooks.git
  cd ops-ansible-playbooks/playbooks
  copy vault password from LastPass into .vaultpassword
  add to environments/kickstart/hosts (fifteen character hostname limit)
  ansible-playbook kickstart.yml -i environments/kickstart
  reboot VM

spotlight
  git clone git@gitlab.com:surfliner/surfliner.git
  cd surfliner/ansible
  create symlinks
  ln -s ../../ops-ansible-playbooks/playbooks/starlight/local-pre-tasks.yml
  ln -s ../../ops-ansible-playbooks/playbooks/starlight/local-post-tasks.yml
  ansible-galaxy install --roles-path roles -r roles/requirements.yml

  cd ops-ansible-playbooks/playbooks
  ./deploy -D ../../surfliner/ansible/spotlight.yml  -i environments/staging/ -l exhibits-staging -u ansible -b

Add other roles (eg, ssh_keys)
  ansible-galaxy roles.yml

Add "my" keys to starlight
  ansible-playbook -D -i environments/staging/ -l exhibits-staging ssh-key.yml -e 'ssh_keys_ad_usernames=["rstanonik"]' -e ssh_keys_user=starlight -u ansible -b

add deploy.sh and environment variables to user starlight
  ./deploy -D -i environments/staging/ -l exhibits-staging APP_STARLIGHT.yml

jenkins app deploy
  increase ssh timeout in jenkins (5 minute default in ssh step)

To create admin user
login to app https://exhibits-staging.ucsd.edu/
Then as user starlight on the server
cd /opt/starlight/current
rake spotlight:admin

[SL]: https://gitlab.com/surfliner/surfliner 
