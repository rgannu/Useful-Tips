# Using cm-client python api to create cloudera manager services

https://gist.github.com/gdgt/d2eb4abe39da05353a58451c103f41e5

(copied to cmxDocker.py)

# Command Line Options: How To Parse In Bash Using “getopt”

http://www.bahmanm.com/blogs/command-line-options-how-to-parse-in-bash-using-getopt
```bash
ARGUMENT_LIST=(
    "arg-one"
    "arg-two"
    "arg-three"
)


# read arguments
opts=$(getopt \
    --longoptions "$(printf "%s:," "${ARGUMENT_LIST[@]}")" \
    --name "$(basename "$0")" \
    --options "" \
    -- "$@"
)

echo $opts

eval set -- "$opts"

while [[ $# -gt 0 ]]; do
    case "$1" in
        --arg-one)
            echo "in arg-one"
            argOne=$2
            shift 2
            ;;
        --arg-two)
            argTwo=$2
            shift 2
            ;;
        --arg-three)
            argThree=$2
            shift 2
            ;;
        *)
            echo $1
            echo "in others"
            break
            ;;
    esac
done

echo "args:$argOne::$argTwo::$argThree"
```

# Remove hdfs files older than 10 days
https://stackoverflow.com/questions/44235019/delete-files-older-than-10days-on-hdfs
```bash
hdfs dfs -ls /file/Path    |   tr -s " "    |    cut -d' ' -f6-8    |     grep "^[0-9]"    |    awk 'BEGIN{ MIN=14400; LAST=60*MIN; "date +%s" | getline NOW } { cmd="date -d'\''"$1" "$2"'\'' +%s"; cmd | getline WHEN; DIFF=NOW-WHEN; if(DIFF > LAST){ print "Deleting: "$3; system("hdfs dfs -rm -r "$3) }}'
```

# Safer bash scripts with 'set -euxo pipefail'
```bash
https://vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/
```

# Kafka useful commands
## To know the # of messages in a kafka topic
```bash
kafka-run-class kafka.tools.GetOffsetShell --broker-list `hostname -f`:9092 --topic LilyItxIngestErrors --time  -1 | while IFS=: read topic_name partition_id number; do echo "$number"; done | paste -sd+ - | bc
```

## Offset checker
```bash
while [ 1 ]; do kafka-consumer-offset-checker --topic LilyItxIngest --zookeeper `hostname -f`:2181/kafka --group  lily-itx-speed-layer-ingest-default | awk -F ' ' '{ print "partition:" $3 " => offset:" $4 " => logsize:" $5 " => lag:" $6}'; sleep 2; done 
```

# Sum all offsets
```bash
while [ 1 ]; date; do kafka-consumer-offset-checker --topic LilyItxIngest --zookeeper `hostname -f`:2181/kafka --group  lily-itx-speed-layer-ingest-default | grep -v "Lag" | awk -F ' ' '{ print  $3 ":" $4 ":" $5 ":" $6}' | while IFS=: read partition offset logsize lag; do echo "$lag"; done | paste -sd+ - | bc; sleep 60; done > /tmp/offset-check.out
```

# Compare the lag
kafka-consumer-offset-checker --topic LilyItxIngest --zookeeper `hostname -f`:2181/kafka --group  lily-itx-speed-layer-ingest-default | awk -F ' ' '{ print "partition:" $3 " => offset:" $4 " => logsize:" $5 " => lag:" $6}' > /tmp/offset1; \
sleep 60; \
kafka-consumer-offset-checker --topic LilyItxIngest --zookeeper `hostname -f`:2181/kafka --group  lily-itx-speed-layer-ingest-default | awk -F ' ' '{ print "partition:" $3 " => offset:" $4 " => logsize:" $5 " => lag:" $6}' > /tmp/offset2
```

# Extract RPM without installing it
```bash
rpm2cpio <rpm file>
rpm2cpio - < <rpm file>
rpm2cpio <rpm file> | cpio -idmv
```

# Copy between GIT repositories

## To copy a branch

```bash
# To add remote repository
git remote add github <url of github repository>

# To get the latest branches, tags from github
git fetch github

# To track changes from github branch to local branch
git checkout -b <branch-local> github/<branch-in-github>

# Retrieve all changes from github branch to local branch
git pull --rebase

# Push the changes to local repository
git push -f origin <branch>
```

# To Delete a remote Git tag

If you have a tag named '12345' then you would just do this:

```bash
git tag -d 12345
git push origin :refs/tags/12345
That will remove '12345' from the remote repository.
```

# Rename a git branch (locally and remote)
https://gist.github.com/lttlrck/9628955

```bash
git branch -m old_branch new_branch         # Rename branch locally    
git push origin :old_branch                 # Delete the old branch
git branch --unset-upstream new_branch      # Unset the tracking info, so it doesn't push with the old name
git push --set-upstream origin new_branch   # Push the new branch, set local branch to track the new remote
```

# To rename dir in GIT

Basic rename (or move):
```
git mv <old name> <new name>
```
Case sensitive rename—eg. from casesensitive to CaseSensitive—you must use a two step:
```
git mv casesensitive tmp
git mv tmp CaseSensitive
```

# To save space in jenkins:
## Remove all the jars from jobs directory !!!
```
cd /var/lib/jenkins/jobs
sudo -u jenkins /bin/find . -name *.jar | sudo xargs rm -f
```

# Delete all un-tagged (or intermediate) images:

## Cleaning up Docker images (local)
```
docker images -q  --filter 'dangling=true' | xargs docker rmi --force
```

## Cleaning up Docker images (AWS)

```
#!/bin/sh

APIS="billing-period card eid integration prerequest product quickview standing-order topup classic-credit-view transaction"

for api in `echo ${APIS}`; do
  REPO="spencr/api/${api}"
  echo
  echo "Deleting UNTAGGED images from amazon ECR repo: ${REPO}..."
  aws ecr batch-delete-image --repository-name ${REPO} --image-ids $(aws ecr list-images --repository-name ${REPO} --filter tagStatus=UNTAGGED --query 'imageIds[*]'| tr -d " \t\n\r")
done
```

# To remove from Jenkins zombie threads:

Go to "Manage Jenkins" -> "Script Console"

```
Jenkins.instance.getItemByFullName("JobName").getBuildByNumber(BuildNumber).finish(hudson.model.Result.ABORTED, new java.io.IOException("Aborting build"));
```
JobName: 
 - For multi-branch pipeline job: <Project-Name>/<branch>
BuildNumber:
 - Retrieve from the Jenkins


To kill a specific thread with the name, use:
```
Thread.getAllStackTraces().keySet().each() {
  t -> println(t.getName()); 
}
```
Then, kill with that thread name:
```
Thread.getAllStackTraces().keySet().each() {
  t -> if (t.getName()=="YOUR THREAD NAME" ) {   t.interrupt();  }
}
```

# Useful GitHub Projects

playground: https://github.com/rgannu/playground

learning-spark-examples: https://github.com/holdenk/learning-spark-examples.git
kafka-junit: https://github.com/charithe/kafka-junit
camelinaction2: https://github.com/camelinaction/camelinaction2
spring-data-examples: https://github.com/spring-projects/spring-data-examples
spring-restbucks: https://github.com/olivergierke/spring-restbucks
spring-restdocs: https://github.com/spring-projects/spring-restdocs
spark-queue-stream: https://github.com/abhinavg6/spark-queue-stream
swagger-maven-example: https://github.com/kongchen/swagger-maven-example
Simple-Meetup-Client: https://github.com/abhinavg6/Simple-Meetup-Client
example-spark: https://github.com/mkuthan/example-spark
java8-custom-collector-example: https://github.com/marad/java8-custom-collector-example
mvc-exceptions: https://github.com/paulc4/mvc-exceptions
spring-rest-exception-handler: https://github.com/jirutka/spring-rest-exception-handler
SparkApps: https://github.com/sujee81/SparkApps
jsondoc: https://github.com/fabiomaffioletti/jsondoc
java8-tutorial: https://github.com/winterbe/java8-tutorial
jdk8-lambda-tour: https://github.com/JosePaumard/jdk8-lambda-tour
Cloudera-Impala-JDBC-Example: https://github.com/onefoursix/Cloudera-Impala-JDBC-Example
generator-reveal: https://github.com/slara/generator-reveal
gs-caching: https://github.com/spring-guides/gs-caching
gs-service-registration-and-discovery: https://github.com/spring-guides/gs-service-registration-and-discovery

hello-samza: https://github.com/jkreps/hello-samza
IgniteSparkIoT: https://github.com/dmagda/IgniteSparkIoT
Neo4j_Hive_Demo: https://github.com/davidfauth/Neo4j_Hive_Demo
project-examples: https://github.com/JFrogDev/project-examples

professional-development-professional-developers: https://github.com/stevegrunwell/professional-development-professional-developers
reveal.js: https://github.com/hakimel/reveal.js
reveal-md: https://github.com/webpro/reveal-md


# Markdown Cheatsheet
https://github.com/adam-p/markdown-here/wiki/Markdown-Here-Cheatsheet#tables

# Spring bean scopes

## 5 types of bean scopes supported :


|Scope	|Description|
|:------------:|:------------:|
|singleton|(Default) Scopes a single bean definition to a single object instance per Spring IoC container.|
|prototype|Scopes a single bean definition to any number of object instances.|
|request|Scopes a single bean definition to the lifecycle of a single HTTP request; that is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring ApplicationContext.|
|session|Scopes a single bean definition to the lifecycle of an HTTP Session. Only valid in the context of a web-aware Spring ApplicationContext.|
|globalSession|Scopes a single bean definition to the lifecycle of a global HTTP Session. Typically only valid when used in a Portlet context. Only valid in the context of a web-aware Spring ApplicationContext.|
|application|Scopes a single bean definition to the lifecycle of a ServletContext. Only valid in the context of a web-aware Spring ApplicationContext.|
|websocket|Scopes a single bean definition to the lifecycle of a WebSocket. Only valid in the context of a web-aware Spring ApplicationContext.|

To set spring bean scopes we can use “scope” attribute in bean element or @Scope annotation for annotation based configurations.

# Deleting a remote GIT branch
```
git push orgin --delete <branch_name>
git branch -d <branch_name>

# When there are unmerged changes which you are confident of deleting:
git branch -D <branch_name>
```

# Useful Unix commands
## Find out stale files
```
lsof | grep -i defunct
```
## Display output as a Table
```
mount | column -t
cat /etc/passwd | column -t -s:
```
## Repeat a Command Until It Runs Successfully
```
while true; do ping -c 1 google.com > /dev/null 2>&1 && break; done
```
## Sort ps output
```
ps axu --sort=vsz
ps axu --sort=pcpu
```
## Sort Processes by Memory Usage
```
ps aux | sort -rnk 4:
```
## Sort Processes by CPU Usage
```
ps aux | sort -nk 3:
```
## Watch Multiple Log Files at the Same Time
```
yum install multitail
```
## Monitor Command Output at Regular Intervals
```
watch df -h
```
## Run Program After Session Killing
```
nohup <command>
```
## Run Program After Session Killing
```
nohup <command>
```
## Automatically Answer Yes or No to Any Command
```
[yes][no] | yum install <package>  OR
yum install -y <package>
```
## Create File With a Specific Size
```
dd if=/dev/zero of=out.txt bs=1M count=10
```
## Run Your Last Command as Root
```
sudo !!
```
## Record Your Command Line Session
```
typescript : script
```
## Convert a File to Upper or Lower Case
```
cat myfile | tr a-z A-Z > output.txt
```
## Use of xargs
### Create the tar file with all *.png files
```
find. -name *.png -type f -print | xargs tar -cvzf images.tar.gz
```
### Remove all files except multiple files 
```
find spec/acceptance/ -type f  -name "*.rb" | grep  -ve "00_" -ve "02_" | xargs rm -rf
```
### Remove all files except war files
```
find . -type f -name "*.war"|awk -F'.' '{print $2}'|awk -F'/' '{print $2}'|xargs rm -rf
```
## Convert a File to Upper or Lower Case
```
cat myfile | tr a-z A-Z > output.txt
```


# Unix grep command to retrieve lines adjacent

```
grep -C 5 "search pattern" <file>
```
Gives 5 lines above and below the "search pattern" 

# Finding Zookeeper version

```
echo ruok | nc zkserver1 2181
echo status | nc zkserver1 2181
replace zkserver1 with the hostname\ip eg. 127.0.0.1

# echo ruok  | nc 127.0.0.1 2181
imok
# echo status | nc 127.0.0.1 2181
Zookeeper version: 3.4.5-cdh5.4.8--1, built on 10/15/2015 16:00 GMT
:
:
```

First line should respond with imok Second line should respond with Zookeeper version + more.

# SVN property set svn:externals

Directory Structure

- TopDir
    * Dir1
        + file1.xml
    * Dir2
        + file2.xml
    * Dir3
        + file3.xml (Link to Dir1/file1.xml)
---
## To create a symlink "Dir3/file3.xml" to point to the file "Dir1/file1.xml": 
```
$ cd TopDir/Dir3
$ svn propset svn:externals 'file3.xml http://svnURL-path/Dir1/file1.xml' .
```
## Commit "Dir3" which will set the property.
```
$ svn commit -m "Property set - symlink from file3.xml to http://svnURL-path/Dir1/file1.xml"
$ cd ..
$ svn update
```
## You should now be able to see the desired result
- The content of "Dir3/file3.xml" is content is "../Dir1/file1.xml"

## Drawback

- The file content will change when changed either from Dir1/file1.xml or Dir3/file3.xml !!!
- This is not a viable solution as the user doesn't know that it might affect someone else !!! 
---
## One can do all these also from TortoiseSVN in windows enviornment !!!

- Right-click on the directory 
- Select "Tortoise SVN.." => "properties" => "New..."
- Select from "Property name combo box" "svn:externals"
- In the "Properties value" give the following and select "OK"
```
file3.xml http://svnURL-path/Dir1/file1.xml
```
- Commit the directory "Dir3"
- Update of the project from any other client will get the file Dir3/file3.xml which is linked to Dir1/file1.xml

 
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

# Find magics
## Remove all files except multiple files 
```
find spec/acceptance/ -type f  -name "*.rb" | grep  -ve "00_" -ve "02_" | xargs rm -rf
```

## Remove all files except war files
```
find . -type f -name "*.war"|awk -F'.' '{print $2}'|awk -F'/' '{print $2}'|xargs rm -rf
```

## Describe all kafka topics
```
for topic in `echo "inventory-ana services-ana xdsl-ana shdsl-ana naports-ana"`; do /opt/kafka/bin/kafka-topics.sh --describe --topic ${topic} --zookeeper localhost:2181; done
```

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

[gist]: https://gist.github.com/gdgt/d2eb4abe39da05353a58451c103f41e5