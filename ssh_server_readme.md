# Install SSH Server
```
sudo apt-get install openssh-server
```

# Run SSH Persistently
First remove run levels for SSH
```
sudo update-rc.d -f ssh remove
```
Add default levels 
```
sudo update-rc.d -f ssh defaults
```

# Update SSH keys
Move old keys into another directory
```
cd /etc/ssh/
mkdir old_keys
mv ssh_host_* old_keys
```
Create new keys
```
sudo dpkg-reconfigure openssh-server
```

# Configure SSH
edit /etc/ssh/sshd_config

Disable password authentication
```
PasswordAuthentication no
```

To Allow/Deny Users
```
AllowUsers User
DenyUsers root
```

Display Banner
```
Banner /etc/issue
```

# Restart SSH
```
sudo service ssh restart
update-rc.d -f ssh enable 2 3 4 5
```

# Generating Private/Public Keys
```
ssh-keygen -t rsa -b 4096
```

# Copy over SSH keys
```
ssh-copy-id [username]@[host]
```

Manual 
```
cat id_rsa.pub >> ~/.ssh/authorized_keys
```
