**Q1. Volume**



create instance

yum install httpd -y

rpmquery httpd



cd /var/www/html



cat > index.html



cd



systemctl start httpd



systemctl enable httpd

systemctl status httpd





allow port 80 in inbound rules -> http



copy public ip and paste ip:80



in terminal -lsblk

&nbsp;

go to ebs-->volume-->create 5gb volume-->attach volume



in terminal



run command - lsblk -> to check storage and partition

lsblk -fs

blkid

mkfs.ext4 /dev/nvme1n1

mkdir /dl/

mount /dev/nvme1n1 /dl --temporary mounting

df -h

blkid -- copy the uuid 

vim /etc/fstab 

then permanent mount-- UUID=42874245-49a0-4363-b8c8-b29d8e1a1ed3       /dl     ext4    defaults 0 0



restart the instance

cd dl/

touch kundan.txt{1..10}



**Q2. Copy volume to other region (using snapshot)**



* create instance in NV
* sudo 
* in terminal -lsblk
* go to ebs-->volume-->create 5gb volume-->attached volume
* in terminal
* run command - lsblk -> to check storage and partition
* lsblk -fs
* blkid
* mkfs.ext4 /dev/nvme1n1
* mkdir /dl/
* mount /dev/nvme1n1 /dl --temporary mounting
* df -h
* blkid -- copy the uuid 
* vim /etc/fstab 
* then permanent mount-- UUID=42874245-49a0-4363-b8c8-b29d8e1a1ed3       /dl     ext4    defaults 0 0
* restart the instance
* cd dl/
* touch kundan.txt{1..10}
* .....................
* create snapshot --> snapshot me jake copy snapshot add the region us-east-2(copy krna h snapshot ko ohio region me)
* launch instance in ohio
* fir snapshot se volumecreate krna h--attach volume
* terminal me -- mkdir ohiodr
* lsblk
* mount /dev/nvme1n1 ohiodr/
* cd ohiodr
* ll
* (files NV region k isme aajayega)



**Q3. Copy AMI**



nv server k ami create krna h then copy ami ..go to ohio -> AMI -->create instance form image



**Q4. S3 configuration**



Go to Amazon S3



  create a bucket



  bucket name should be unique



  untick block all access



  disable ACL



  create bucket



  select the bucket



  upload ( add files / folders )



select the file -> copy the link from properties and paste -> access will be denied



FULL ACCESS



Go to S3 bucket -> select it -> permission -> ACL -> enable



CONTENT BASED ACCESS



or enable a single files ACL - by selecting the file and enabling ACL.



IF VERSIONING IS ENABLED IN YOUR S3 BUCKET - YOU CAN RETAIN DATA EVEN AFTER DELETING IT.



SO MAKE SURE TO ALWAYS ENABLE VERSIONING WHILE CREATING A BUCKET



TO EDIT AFTER CREATING ->



Select bucket



Properties



bucket versioning



Enable it -> read, write enable



Go to bucket -> delete some data



Now enable show version to see the data deleted



click on the deleted data -> and you can download it



then upload it



By default the data is stored for 7 days after deleted



create instance



  connect -> yum update -y



**Manash**



  yum install -y automake fuse-devel gcc-c++ git libcurl-devel libxml2-devel make openssl-devel



Git clone -



git clone https://github.com/s3fs-fuse/s3fs-fuse.git



Move to s3fs-fuse



cd s3fs-fuse



./autogen.sh



./configure --prefix=/usr --with-openssl



make



sudo make install



to verify package



which s3fs



then cd



In amazon



Go to IAM - USERS



Attach policies



Give him S3full access right



Create it



Go to Users - select your user



create access key -> select CLI



download the csv file or copy the id and password



Create new file to store the passwd



touch /etc/passwd-s3fs



Paste it in the vim/etc/passwd-s3fs ( id:password)



Change the permission of the file



 sudo chmod 640 /etc/passwd-s3fs



For mounting to s3 bucket



s3fs bucketname /mnt -o passwd\_file=/etc/passwd-s3fs



/mnt is the folder name where the data will be stored



cd /mnt



ls -a (to check the file)



create files in it - it will also reflect in s3



**Shubhraneel**



yum install wget -y



rpmquery mount-s3



install mount-s3 from aws website



wget -url of mount s3



yum install ./mount-s3.rpm



rmpquery mount-s3



yum install unzip



Install aws cli in the instance (website)



paste the command from website and it will install aws-cli



aws configure



put the access key



and secret key from the zip file saved



region : us-east-1



output : table



it is stored in cd .aws (config \\\& credentials)



create a mount point



mkdir /mpt



mount-s3 bucketname /mpt/



df -h



cd /mpt



ll -> you can see all the content



**Q5,6 -> VPC and Peering**



**Q7. EFS**



create 2 instance in different zone

search EFS -> create filesystem -> set-name(my-efs)-> VPC default ->create file system

connect all instances in the terminal



terminal on machine1

rpmquery nfs-utils -> which is by default on amazon Linux



systemctl start nfs-utils

systemctl start nfs-server.service



systemctl status nfs-server



create folder in all instance -- mkdir /name

create a security group in AWS and allow port 2049 in inbound rules NFS



Go to instance -> security group -> Inbound rules NFS -> 2049 -> save



Go to EFS - > network and in the zone you have deployed your machines -> remove their default security group and choose your own created.



Now go to file system ( search for EFS )



select my-efs and attach it



Mount via DNS

Come to the instance and paste the DNS client command(In the EFS) in the instances with /foldername --> remove the efs thing from link



create files in the folder of all 3 instances



and check ll -> all the files will be reflected in all  machines



**Q8.Load balancing**



create 2 instances in different zones - same security group

Go to load balancing



Create Target group

choose instances name it next and add the machines - include in pending create target group



Now go to - Load Balancer Create application load balancer

Name it Internet facing (for databse it should be internal) IPv4

select the zones in which you had made the instances (a,b,c,d)

select the same security group as instance



select http and target group then create target group



Now go to load balancer



wait for it to get active Now select the load balancer created Scroll and copy the DNS name and paste it on website



Now to check - go on any machine terminal and systemctl stop httpd



Now refresh the webpage and you will see bad gateway - later in the target group amazon will put the machine to unhealthy and stop diverting requests to it.

TO ENABLE SESSION STICKINESS



Go to target groups - select your target group Go to actions - edit target group attributes



Target selection configuration - stickiness - turn on - duration 1 - days save changes

.

Now refresh the webpage the server wont change -

