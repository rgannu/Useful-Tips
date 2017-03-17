# How to add trusted certificates to the Server

http://kb.kerio.com/product/kerio-connect/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html

## Linux (CentOs 6)

### Add
Install the ca-certificates package:
```
yum install ca-certificates
```

Enable the dynamic CA configuration feature:
```
update-ca-trust force-enable
```
Add it as a new file to /etc/pki/ca-trust/source/anchors/:
```
cp foo.crt /etc/pki/ca-trust/source/anchors/
```
Use command:
```
update-ca-trust extract
```

# To disable proxy during npm install, do the following:
In command prompt
```
npm config edit
```
Comment out the "proxy" tag and then use
```
npm install
```


for topic in `echo "inventory-ana services-ana xdsl-ana shdsl-ana naports-ana"`; do /opt/kafka/bin/kafka-topics.sh --describe --topic ${topic} --zookeeper localhost:2181; done

find spec/acceptance/ -type f  -name "*.rb" | grep  -v "02_sbi_spec" | xargs rm -rf


# Git Checkout by a separate private key
In ~/.ssh/config, add:
```
host github.com
 HostName github.com
 IdentityFile ~/.ssh/id_rsa_github
 User git
```
Now you can do 
```
git clone git@github.com:username/repo.git
```

NOTE: Verify that the permissions on IdentityFile are 400.
SSH will reject, in a not clearly explicit manner, SSH keys that are too readable. 
It will just look like a credential rejection. The solution, in this case, is:

    chmod 400 ~/.ssh/id_rsa_github

# GIT information

git pull just merges the fetched commits in your working directory and will not restore your deleted directory ( but might warn you about conflicts.)

If you have deleted the folders in your working directory, you have to do:
```
git checkout -- test
to get it back.
```
Or you can do 
```
git reset --hard
``` 
to completely bring your working directory to HEAD state.


# Google Calendar Quick Add

Only the "with <email>" doesn't add according to documenation the guest
```
Badminton Class 7pm-8:30pm on 06-Dec-2016 at Vogelzanglaan 6,2020 Antwerpen with <email>
```

# Export/Import GIT repository across machines

Before you start, make sure you know both clone URLs for the repos you are trying to export/import.

For example, to import it on GitHub, make sure that the repository you’re trying to push to already exist on GitHub and is empty.

## Step-By-step

Make a “bare” clone of the repository (i.e. a full copy of the data, but without a working directory for editing files) using the external clone URL. This ensures a clean, fresh export of all the old data.
Push the local cloned repository to GitHub using the “mirror” option, which ensures that all references (i.e. branches, tags, etc.) are copied to the imported repository.

In this example, we use an external account named extuser and
a GitHub account named ghuser to transfer repo.git

Make a bare clone of the external repository to a local directory
```bash
git clone --bare https://githost.org/extuser/repo.git
```
Push mirror to new GitHub repository
```bash
cd repo.git
git push --mirror https://github.com/ghuser/repo.git
```

In case you get the error **"SSL certificate problem: unable to get local issuer certificate"**, follow the procedure as described in
 
[Microsoft Blog](https://blogs.msdn.microsoft.com/phkelley/2014/01/20/adding-a-corporate-or-self-signed-certificate-authority-to-git-exes-store/)

Remove temporary local repository
```bash
cd ..
rm -rf repo.git
```
Your newly imported repository should be ready to go on GitHub!