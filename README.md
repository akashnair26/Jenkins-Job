# Jenkins-Job

# Report of the Task Performed.
As per the scenario, a Jenkins job was to be made which takes the aws region as a parameter and give the html report about the resource utilization like CPU Network I/O of all the
instance in the provided region furthermore it should take some more parameters like ENV, SERVER_TYPE, APPLICATION from the available tags.
The job should take the AWS region as a parameter and print the resource utilization in a tabular form with the heads Instance Name, Instance Id, CPU, Network.
The data in the CPU and Network field should be in the relative form. If the CPU or network usage is between 0 to 30 it should print low. In the same way if the cpu or network usage is between 30 to 70 it should print Moderate and if the usage is between 70 to 100 it should give high.


# Steps followed to complete the task
 1) Installed and configured Jenkins on a ec2 instance.
 2) Attached an IAM role to Jenkins instance with ec2 full access.
 3) Created a parameterized job with AWS_REGION, ENV, SERVER_TYPE, APPLICATION as the parameters.
 4) Provided the Parameterized job with a groovy script to generate the report of the instances of the region specified in the Parameters of the job.
 5) Used aws-cli in groovy script to generate the report.
 6) Used aws-cloudwatch cli to generate the CPU usage and monitoring the network. 
  
 # Following were the cmds used to test and check the conditions.
 
 # 1) aws-cli script to display instances in various regions.

* aws ec2 describe-instances  --region ${AWS_REGION} --query "Reservations[*].Instances[*].{PublicIP:PublicIpAddress,ENV:Tags[?Key==\'ENV\']|[0].Value, Name:Tags[?Key==\'Name\']|[0].Value, APPLICATION:Tags[?Key==\'APPLICATION\']|[0].Value, SERVER_TYPE:Tags[?Key==\'SERVER_TYPE\']|[0].Value,Status:State.Name}" --output table
 
aws ec2 describe-instances  --region ${AWS_REGION} --query "Reservations[*].Instances[*].{PublicIP:PublicIpAddress,ENV:Tags[?Key==\'ENV\']|[0].Value, Name:Tags[?Key==\'Name\']|[0].Value, APPLICATION:Tags[?Key==\'APPLICATION\']|[0].Value, SERVER_TYPE:Tags[?Key==\'SERVER_TYPE\']|[0].Value,Status:State.Name}" --filters "Name=tag:ENV,Values=${ENV}" "Name=tag:SERVER_TYPE,Values=${SERVER_TYPE}"  "Name=tag:APPLICATION,Values=${APPLICATION}" --output table

# 2) aws-cli cloudwatch script to display metrices of instances.

 * aws cloudwatch get-metric-statistics --metric-name CPUUtilization --start-time 2021-12-13T03:18:00Z --end-time 2021-12-13T23:18:00Z --period 3600 --namespace AWS/EC2 --statistics Maximum --dimensions Name=InstanceId,Value=i-0c04d83ff15d676d1 --output table
 * aws cloudwatch get-metric-statistics --metric-name NetworkOut --start-time 2021-12-13T03:18:00Z --end-time 2021-12-13T23:18:00Z --period 3600 --namespace AWS/EC2 --statistics Maximum --dimensions Name=InstanceId,Value=i-0c04d83ff15d676d1 --output table
 * aws cloudwatch get-metric-statistics --cli-input-json file://metrics.json --output table (the json file contains the metrics that need to be passed)     
