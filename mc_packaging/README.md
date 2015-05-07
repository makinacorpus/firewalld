Notes
=======
```
sudo apt-get install dh-systemd  bzr python-paramiko dh-autoreconf pbuilder build-essential
```

cat ~/.dput.cf
```
[firewalld]
fqdn = ppa.launchpad.net
method = sftp
incoming = ~makinacorpus/firewalld
login = kiorky
allow_unsigned_uploads = 0
```


git checkout origin/upstream || git checkout upstream
# MERGE UPSTREAM
git checkout master
git rebase -i upstream
./mc_packages/sync_debian.sh


