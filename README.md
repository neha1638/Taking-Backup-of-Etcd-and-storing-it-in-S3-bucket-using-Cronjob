# Taking Backup of Etcd and storing it in S3 bucket using Cronjob


## Prerequisite:
- AWS CLI
- Docker
- Crontab installed on Ubuntu
- Kubernetes Cluster
## To configure Aws cli
 https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html
- Install Aws cli 

      apt install awscli
- Then configure Aws
 
      aws configure
- Create S3 bucket using command
       
      aws s3 mb s3://<bucket name>
  ( Enable Bucket Versioning )     
## To Create Docker image 

- Create Dockerfile and then build Docker image and push it to Docker hub.

      export ETCD_VERSION=v3.5.4
- Build an image from a Dockerfile
      
      docker build --build-arg=$ETCD_VERSION -t etcd-backup:$ETCD_VERSION .
- List images

      docker images | grep -i "etcd-backup"
- to check image is present or not

      docker images
 - Log in to a Docker registry
 
       docker login

 - Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

       docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
 - List images

       docker images
 - Push an image or a repository to a registry

       docker push [OPTIONS] NAME[:TAG]

## To Create Cron job in kubernetes cluster :
- Create directory data/etcd-backup

      mkdir data

      cd data

      mkdir etcd-backup

      cd ..

      ls -l

 - Create cronjob with this command in kubernetes:

       kubectl apply -f cronjob.yaml
 - To check jobs in kubernetes cluster

       kubectl get jobs     
 - To check cronjobs in kubernetes

       kubectl get cj

 - To check pod is running or not

       kubectl get po      


## To setup cron jobs in Ubuntu

- Check directory
      
      ls -al
- Chenge directory with this command 

      cd /data/etcd-backup
- If cron is not installed, install the cron package on Ubuntu:
   One can Install the cron package with package Manager using the following command-
  
      apt-get install cron
- Verify if cron service is running:

      systemctl status cron
- The file can be customized with this command:

      crontab -e

- Put this data in crontab-e file it will automatically copy snapshots of etcd-backup to s3 bucket after every 1 minute:
     
      * * * * * aws s3 sync /data/etcd-backup s3://<bucket-name>

- Restart crontab service
    
      systemctl restart cron.service

Now check your s3 bucket the snapshots of etcd are stored in it .