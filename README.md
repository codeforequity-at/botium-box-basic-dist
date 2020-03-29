# Botium Box - Community Edition

![Docker Pulls](https://img.shields.io/docker/pulls/botium/botium-box-ce)

Chatbots are driving the industry. With Botium we are driving chatbots. Botium is a suite of open source software components that support chatbot makers in training and quality assurance.

Botium Box is running on standard components available for free. You can install it on your own server (on premise), or use cloud providers for serverless installation, or even a mixture of those approaches.

**_IF YOU LIKE WHAT YOU SEE, PLEASE CONSIDER GIVING US A STAR ON GITHUB!_**

## Release Notes

Check them out [in the Botium Wiki](https://botium.atlassian.net/wiki/spaces/BOTIUM/pages/20807681/Botium+Box+Release+Notes)

## Getting Botium Box

All you need is part of this Github repository. You can download via _git_ or via _curl_ or manual download, your choice.

### Using git

    > git clone https://github.com/codeforequity-at/botium-box-basic-dist.git
    > cd botium-box-basic-dist

If you already have installed it before and just doing an update, a _git pull_ is enough:

    > cd botium-box-basic-dist
    > git pull

_In case of file conflicts, if you changed the files locally, you will have to merge the changes._

### Using curl

    > curl -L --output botium-box-basic-dist.zip https://github.com/codeforequity-at/botium-box-basic-dist/archive/master.zip
    > unzip botium-box-basic-dist.zip
    > cd botium-box-basic-dist-master

_If you already have installed it before and just doing an update, you have to download to another location and merge the new files to this directory._

### Manual download

Just click on the _Clone or download_ button above and use _Download ZIP_, unpack the file locally.

## Getting Community Edition License

Point your browser to https://registration.botiumbox.com to register and apply for a community license. You will receive it as email attachment named _license.key_.

## Botium Box Installation with AWS CloudFormation

You can install Botium Box with the included AWS CloudFormation template.

**You need a working AWS account. There will be costs involved for the created EC2 instance**

1. Open the Cloud Formation service in the AWS Console and create a new CloudFormation stack
2. Upload the CloudFormation template file _botium-box-ce-single-instance.template_ from the _cloudformation_ folder of this repository.
3. Enter the Botium Box License key you received earlier (Copy & Paste file content from _license.key_) and select values for other parameters.
   * Use **at least** a _t2.small_ instance type for first steps
   * Use **at least** a _t2.medium_ instance type for production usages
4. After some minutes, the CloudFormation stack is ready, you can see progress in the _Events_ and _Resources_ tabs of the stack
5. In the _Outputs_ tab, the URL for accessing Botium Box is shown

## Botium Box Installation on Kubernetes

You can run Botium Box on your Kubernetes environment, locally in [Minikube](https://kubernetes.io/de/docs/setup/minikube/), or on a cloud server as [Amazon Elastic Kubernetes Service](https://aws.amazon.com/de/eks/).

Persistent data storage is not part of the Kubernetes definitions in this repository. You can [install MySQL in your Kubernetes environment as well](https://kubernetes.io/docs/tasks/run-application/run-single-instance-stateful-application/). All major cloud providers support hosted MySQL installations, for example
* [Amazon RDS for MySQL](https://aws.amazon.com/de/rds/mysql/)
* [Azure Database for MySQL](https://azure.microsoft.com/de-de/services/mysql/)

### Prerequisites

* A working Kubernetes environment
* [kubectl](https://kubernetes.io/de/docs/tasks/tools/install-kubectl/) connected to your Kubernetes environment
* A MySQL-compatible database, installed locally or hosted in the cloud
* MySQL connectivity (check Firewalls etc)
    - MySQL hostname
    - MySQL username
    - MySQL password

Download this Git repository to your local workstation.

### Adapting Configuration

Copy the file _kubernetes/02-configmap.yml.template_ to _kubernetes/02-configmap.yml_.

In the file _kubernetes/02-configmap.yml_, enter your MySQL connection information for the placeholders:
* {{ my-mysql-host-name }}
* {{ my-mysql-user-name }}
* {{ my-mysql-password }}

In the file _kubernetes/02-configmap.yml_, enter the **contents** of the license key file your received:
* {{ my-botium-box-license-key }}

**IMPORTANT: the double curly brackets are part of the placeholders, so be sure to remove them!**

### Deploy to Kubernetes

	> kubectl apply -f ./kubernetes

Wait several minutes for the cluster to come up, then list the status of the services. The *EXTERNAL_IP* column in the line starting with *box* is the **URL to point your browser to**.

```
> kubectl get svc -n botium-box-ce
NAME     TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)        AGE
box      LoadBalancer   xxx.xxx.xxx.xxx some-ip-adress    80:32588/TCP   92m
prisma   ClusterIP      xxx.xxx.xxx.xxx <none>            4466/TCP       92m
redis    ClusterIP      xxx.xxx.xxx.xxx <none>            6379/TCP       92m
```

See the log output of Botium Box:

	> kubectl logs -l name=box -n botium-box-ce

### Use Kubernetes Dashboard (optional)

Optionally you can use Kubernetes web dashboard for basic management operations for [Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/dashboard-tutorial.html) and for [Azure AKS](https://docs.microsoft.com/en-us/azure/aks/kubernetes-dashboard).

### Changing Configuration and Restarting

When changing the configuration (for example after changing the license key), run this command again:

   > kubectl apply -f ./kubernetes

To restart the Botium Box, scale down to 0 replicas and scale up again:

   > kubectl scale --replicas=0 deployment box -n botium-box-ce
   > kubectl scale --replicas=1 deployment box -n botium-box-ce

## Botium Box Installation with Docker

You can run Botium Box on your own server on-premise, or on a cloud server - for example, see [here](https://acloudxpert.com/how-to-install-docker-compose-on-amazon-linux-ami/) for instructions how to set up docker and docker-compose on an Amazon EC2 instance.

### Prerequisites

* [docker](https://www.docker.com/get-started)
* [docker-compose](https://docs.docker.com/compose/install/)

_If you have a firewall, you have to make sure that it allows inbound connections to port 4000 (default Botium Box listen port), or any other port if you are not using the default port_

### Adapting Configuration

In case you are using your own MySQL database, adapt the configuration in _docker-compose.yml_.

Place the license key file _license.key_ in the _resources_ directory to make it available to Botium Box.

### Run Botium Box

The Docker-Compose file contains all prerequisites for running Botium Box and is the recommended and most easy way to run Botium Box.

1. Start Botium Box by running docker-compose:
	> docker-compose up -d

2. Show log output from docker-compose (optional):
	> docker-compose logs -f

3. Point your browser to http://127.0.0.1:4000 (or the IP address of your cloud server)

_To make your Botium testsets, resources and working directory point to a folder of your choice, you have to edit the docker-compose.yml file!_

### Update Botium Box

If you already have installed Botium Box before and just want to update to the latest Botium Box build, first get the new files from this repository (see _Getting Botium Box_ above), and then use docker-compose to pull the new images:

```
> docker-compose stop
> docker-compose rm web
> docker-compose pull
> docker-compose up -d
```

## Data Migration from Previous Release

In most cases, the data will be migrated automatically. Under rare circumstances it will happen that you have to kick of the data migration manually (for example, with Botium Box Release 2.2).

See [Botium Wiki](https://botium.atlassian.net/wiki/spaces/BOTIUM/pages/33224/Botium+Box+Migration+Steps) for instructions.

## Login to Botium Box

Default usernames: 
* admin
* testmanager
* tester
* guest

Default password: "nooneknows"

## First Steps

See the [Botium Wiki](https://botium.atlassian.net/wiki/spaces/BOTIUM/pages/68845585/Tutorial+First+Steps+in+Botium+Box) for your first steps in Botium Box

## Install Samples

Botium Box comes with some sample projects installed. You can find more samples to import into Botium Box [here](https://botium.atlassian.net/wiki/spaces/BOTIUM/pages/57671681/Howto+Import+Botium+Samples).

Have fun.

## Frequently Asked Questions

**Can you help me install Kubernetes?**

No.

**Can you help me setup my Kubernetes cluster?**

No.

**Can you help me with my Kubernetes problems?**

No.

**Can you help me install Docker?**

No.

**Can you help me with my Docker problems?**

No.

**Can you help me setting up MySQL?**

No.

**While these answers may sound rude, we are by no means experts in any of these technologies, and there are better places to ask for help on these topics!**
