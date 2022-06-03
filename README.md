# Hadoop-implementation
## Basic Setup
`` sudo apt update ``
### Install JDK
``sudo apt install openjdk-11-jdk``
### Check the jdk Version
``java -version``
### Make new user
``sudo adduser hadoopuser``
### Switch new User
``su - hadoopuser``
### Generating private and public key pairs
``ssh-keygen -t rsa``
### Add these key pairs to the ssh authorized_keys
``cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys``
### Read and Write permissions
``chmod 777 ~/.ssh/authorized_keys``
### authenticate the localhost
``ssh localhost``


## References
[hadoop-tutorial](https://www.projectpro.io/hadoop-tutorial/big-data-hadoop-tutorial)

[hadoop-tutorial 2](https://docs.google.com/document/d/1-BKY9iBpkm2dSbO7OKc33JBa4CZymOCiwl1EWaFqeBQ/edit)

[hadoop-tutorial 3](https://linuxhint.com/install-apache-hadoop-ubuntu/))
