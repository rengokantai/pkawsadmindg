# pkawsadmindg

- cp6
create a cloudwatch role, create a ec2 instance, attach the role to instance. 
(A role is only assigned to an instance during its launch phase. You cannot assign roles to instances that are already running.)

Then install scripts:
```
sudo yum install perl-DateTime perl-Sys-Syslog perl-LWP-Protocol-https
wget http://aws-cloudwatch.s3.amazonaws.com/downloads/CloudWatchMonitoringScripts-1.2.1.zip
unzip CloudWatchMonitoringScripts-1.2.1.zip
cd aws-scripts-mon
```
usage:
```
./mon-put-instance-data.pl --mem-util --verify --verbose  
./mon-put-instance-data.pl --mem-util --mem-used --mem-avail
```

and create a cronjob
```
vi /etc/cron.d/Monitor_MEM
```
and add content
```
*/5 * * * * ~/aws-scripts-mon/mon-put-instance-data.pl --mem-util --mem-used --mem-avail --from-cron
sudo chmod +x /etc/cron.d/Monitor_MEM
```

other example: (check whether a partition is filled up)
```
*/5 * * * * ~/aws-scripts-mon/mon-put-instance-data.pl --disk-space-avail --disk-path=/ --disk-path=/var --from-cron
```


create a cloudwatch log group and log stream.  
Then config:
```
sudo yum install awslogs
```
add in `vi /etc/awslogs/awscli.conf`
```
[default]
region=us-west-2
```


- cp9 s3
s3cmd(only support python 2.x)
```
wget http://sourceforge.net/projects/s3tools/files/s3cmd/1.6.0/s3cmd-1.6.0.tar.gz  (current version is 1.6.1 as of 2/15/2016)
tar -xvfs3cmd-1.6.0.tar.gz
cd s3cmd-1.6.0
python setup.py install
```

some commands:
```
s3cmd put -r /opt s3://<BUCKET_NAME> 
```
No trailing slash! like /opt/. Trailing slashes after the /opt directory would have copied only the directory's content over to the bucket, but not the directory itself.


finally, enable cross region replication. first, create a bucket in another region, then enable it
```
{
"Version":"2012-10-17",
"Statement":[
      {
"Effect":"Allow",
"Principal":{
"Service":"s3.amazonaws.com"
         },
"Action":"sts:AssumeRole"
      }
   ]
}
```
