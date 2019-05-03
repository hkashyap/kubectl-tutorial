# Hands On Kubernetes Jobs Tutorial

Before start with this tutorial, it's assumed that you have been familiar with the use of pods. To look into the use of pods, you can refer the readme document.

## Difference between pods and jobs
A pod is a container or a group of containers. It's interactive and provides terminals so that you can send commands to it. It's very convenient and user-friendly. It's just like you have a remote computer. However, if the users forget to delete the pods after the their work is done, the computing resources will still be withheld by the idle pods which is a big waste.

So the goal of job is to use the computing resources efficiently. The job also relies on pods to work. So in the config file of a job, one big component is about the pod. Besides that, since the job is not interactive, it means that you need to specify the commands that you want to run. Once the job is done, the state of the pod will become 'completed' and the gpu, memory and cpu resources will be released. Your data should be kept on a persistent volume which is the topic of the next section. 

## Data Storage and Download
### Store sdata in jobs
If you have an interactive pod, you can use 'kubectl cp' to copy the data from the pod to your local computer. However, if you use jobs, the method cannot work because the pod in the job is not interactive.   
In this case, you should use the persistent volume. There are 4 steps for that.   
1. request a persistent volume
2. mount this volume to your pod
3. store the data to the persistent volume when you run the job (implemented in the commands specification)
4. after the job is done, create an interactive pod to download the data


### Download data to local computers
Once you store the data in the persistent volume, you need to create an interactive pod and mount the persistent volume to this pod too. As for downloading the data, you have two choices. 

#### kubectl cp
This command can help you download the data from the pod to your local computer. For example:

        kubectl cp carl-uci/pod1:/runs/  ~/Desktop/

In this command, carl-uci is the namespace and pod1 is the name of the pod. It will copy directory '/runs/' in pod1 to the desktop of your computer. 

It's convenient but could bring heavy workload to the apiserver of the cluster. It should be used when the file is small. Otherwise, S3 is a better choice.

#### S3
S3 represents 'Simple Storage Service'. You can create a storage bucket as a transshipment center. So the data flow is like below:

        Pods <----> S3 Bucket <----> Local Computer
        
To download the data from pods to local computer, you should push it to the S3 bucket first and then download it from bucket to your local computer. To use this, there are also several steps for that. 

##### 1. Install AWS CLI
This is a tool for you to create the bucket, upload or download the data, .etc. It's very similar to scp when you upload/download/delete/list files. 

    pip3 install awscli
    
##### 2. Request the credentials from the administrator and create a bucket.  
The first step is requesting a credential from the administrator. Store the key in the config file for awscli ('~/.aws/config'). The details could be found from [http://ucsd-prp.gitlab.io/userdocs/storage/ceph/#ceph_s3](http://ucsd-prp.gitlab.io/userdocs/storage/ceph/#ceph_s3). 

##### 3. Use AWC CLI to upload/download data via bucket
As for the usage of commands, you can also find examples from [http://ucsd-prp.gitlab.io/userdocs/storage/ceph/#ceph_s3](http://ucsd-prp.gitlab.io/userdocs/storage/ceph/#ceph_s3). 
For example

    $ aws s3 cp s3://jinwei-bucket/runs.zip  ~/Desktop/runs.zip
    
In this command, there's a bucket called 'jinwei-bucket' and the command will copy 'runs.zip' file to your Desktop. 

### Examples and Tips
#### Config example
One example of the config file for a job is like below 

![Screen Shot 2019-05-02 at 10.52.44 P](media/15568620284316/Screen%20Shot%202019-05-02%20at%2010.52.44%20PM.png)

#### One persistent volume for one pod
One persistent volume could only be mounted to one pod. It means you cannot mount the same persistent volume to multiple pods. When your job is done, your pod will be still be there and the persistent volume is also mounted. So you need to delete the job or the pod first, release the use of the persistent volume and then create another interactive pod mounted by this volume for further data transfer. If you hope this volume could be shared, you can look at [http://ucsd-prp.gitlab.io/userdocs/storage/ceph/#ceph_shared](http://ucsd-prp.gitlab.io/userdocs/storage/ceph/#ceph_shared). 

#### Multiple commands in the job
Normally, you may need to run multiple commands in one pod. If there are not many commands, you can refer to [command: ["/bin/bash","-c"]](https://stackoverflow.com/questions/33979501/kubernetes-passing-multiple-commands-to-the-container). Otherwise, you can also use a shell script. You can also see this in the 'Config example'


#### Others
1. Use zip when transferring data to save time
2. Store the credential for S3 in your image so that you don't need to add it every time you create a new pod
3. Use ['awscli_plugin_endpoint' ](http://ucsd-prp.gitlab.io/userdocs/storage/ceph/#ceph_s3_3) so you don't need to type the endpoint for data transfer each time. 

