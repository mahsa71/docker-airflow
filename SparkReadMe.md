## Docke Airflow supporting spark_operator
After following the instructions in this document, our new puckle/docker-airflow image has a  fully functioning spark_operator.
Ps: This image is enabled with persistent postgres and redis storage.

#Set your spark Ip/hostname
change the hostname:YourIp in docker-compose-CeleryExecutor.yml with the External spark's node Information.

#Add the essential spark binaries 
To add the essential spark binaries as a volume docker-compose-CeleryExecutor.yml , first go to the directory of git clone of this repository
and copy the spark and hadoop directory . If you are using cloudera or hortonworks distribution it is important to copy the directories 
with -rL flag, because these directories arem consisted of many symlink and with these flag the actual files are fetched and copied instead
of the symlinks. 

#Set environment variables in Dockerfile
The default is /opt directory which you can change it.

ENV SPARK_HOME=/opt/spark
ENV HADOOP_HOME=/opt/hadoop

#Set spark home
Set your spark home inside the volume in your local system to your path in container.
 
Sudo nano ~/home/ghanavti/docker-airflow/spark/conf/ spark-env.sh
Edit the spark home to the path of spark home in container
export SPARK_HOME=/opt/spark

#rebuild the image 
sudo docker build --rm -t puckel/docker-airflow:withSpark .

#Edit the docker-compose-CeleryExecutor.yml
Edit following directories as volume to worker services if you have made changes in environment variables in Dockerfile:
volumes:
            - ./spark:/opt/spark
            - ./hadoop:/opt/hadoop
I added the ip address of node 1 to all containers in order to prevent changing node1 configuration in spark and hadoop config files:
extra_hosts:
            - "node1:YourIP"
			
#create spark connection
Go to airflow ui (node1:8080) > Admin > connections 
You can create a new spark connection or change the spark-default
 
Conn id : spark_default
Conn Type: Docker Registry
Registry URL: yarn 
Extra: {“queue”:”default”,”deploy-mode”:”cluster”,”spark-home”:”/opt/spark”,”park-binary”:”spark-submit”,”namespace”:”default”}

#bring up the container
sudo docker-compose -f docker-compose-CeleryExecutor.yml up -d



 



