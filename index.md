## DockerHub Registry
---------------------

* A Registry is used to store the docker images.
* Docker images are OCI (Open Container initiative) compliant

![Preview](/imgs/docker_registry.png)

* There are two types of registries

1. Public Registry: Open to all for usage 
   * Example: Docker Hub

2. Private Registry: Registry for internal usage with in the organization
   * Examples: 
     * Docker Hub (private)
     * Azure Container Registry (ACR)
     * AWS Container Registry (ECR)
     * JFROG/Artifcatory
  * [Refer Here](https://hub.docker.com/_/registry) for docker registry image

>## I. Let's build a Student Course registry for Docker Hub

### Pre-requisites:
1. Install docker on any Linux distribution (Ubuntu/CentOS)
2. Find the docker installation [steps here](https://get.docker.com/) 
3. _[StudentCoursesRestAPI](https://github.com/Prozects/StudentCoursesRestAPI.git)_ GitHub

### Configuration:

1. Clone the repository into your local m/c and enter the repository directory

2. Build the 'StudentCoursesRestAPI' image with the following command
```
docker image build -t scr:v1.0 .
```

3. Generally, registries will have repositories, and each repository represents an application

4. The naming conventions slightly differ from registry to registry (cloud to cloud as well)

5. Docker Hub: Naming convention is ```<account-name>/<image-name>:<tag>```

6. Now let's tag the already built image scr:v1.0
```
docker image tag scr:v1.0 aegonn/scr:1.0
```
![Preview](/imgs/tags.png)

7. Next, we need to login into Docker Hub using ```docker login```
![Preview](/imgs/docker_lgn.png)

8. After successful login, let's push the image to DockerHub
```
docker image push aegonn/scr:1.0
```
![Preview](/imgs/docker_push.png)
![Preview](/imgs/dockerhub_img.png)

9. Let's use this image in docker playground
![Preview](/imgs/docker_playground.png)

10. Since, the Docker Hub registry is a public registry, we were able to pull the image from dockerhub directly

11. But, if we want to restrict these images to be used only in our organization or only by particular users in the team; It can be achieved while pushing these images to our private registry

>## II. Pushing docker images to Private Registries (ACR/ECR)

## Azure Container Registry (ACR) [^1]

1. Logout of any other registry if logged in using ```docker logout```

2. Install _**[Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt)**_
```
sudo apt install 
```
```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

3. Next, execute ```az login``` and authenticate the credentials using code

4. Create the registry from the portal as mentioned here _[Create a container registry](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal?tabs=azure-cli#create-a-container-registry)_
![Preview](/imgs/containerregistry.png)

5. Login into Azure Registry
```
az acr login --name dockercontaineregistry
```

6. Next, tag the already built image scr:v1.0 to dockercontaineregistry.azurecr.io/scr:1.0 as this is how azure recognizes the naming convention of images to be stored into ACR
```
docker image tag scr:v1.0 dockercontaineregistry.azurecr.io/scr:1.0
```

7. And push the image to ACR

![Preview](/imgs/acr_logs.png)

![Preview](/imgs/acr_portal.png)


## Elastic Container Registry (ECR) [^2]

1. Refer Here for the _[documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-console.html)_

2. Maually create a repository from the AWS console (portal)
![Preview](/imgs/ecr_console1.png)
![Preview](/imgs/ecr_console2.png)

3. Here (AWS), we can directly create a repository, but in Azure, we created a registry. Registry is a collection of repositories. Each repository represents an application
![Preview](/imgs/ecr_repositry.png)

4. Once we create a repository, then we can view push commands

5. Install Unzip
```
sudo apt update
sudo apt install unzip -y
```

6. Then execute the following commands as provided in _[AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#getting-started-install-instructions)_
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
```
unzip awscliv2.zip
```
```
sudo ./aws/install
```

7. In AWS, we need to create an IAM user to whom we need to attach necessary policies. In this case, we'll be providing 'AdministratorAccess' policy to a user called as 'docker'

8. Create an _[IAM user](https://sst.dev/chapters/create-an-iam-user.html)_
![Preview](/imgs/IAM_1.png)
![Preview](/imgs/IAM_2.png)
![Preview](/imgs/IAM_3.png)
![Preview](/imgs/IAM_4.png)

9. After successful creation of IAM role, navigate to 'Security credentials' so as to create & download 'Access keys'
![Preview](/imgs/IAM_5.png)
![Preview](/imgs/IAM_6.png)
![Preview](/imgs/IAM_7.png)

10. Now configure AWS Cli with credentials (Access keys) and enter the Default regions according to your requirement (ap-south-2)
```
aws configure
```

11. After configuring CLI, enter the push commands from 'View push commands' (Amazon ECR > Repositories > dockerrepository > View push commands)
```
aws ecr get-login-password --region ap-south-2 | docker login --username AWS --password-stdin 890865287737.dkr.ecr.ap-south-2.amazonaws.com
```
![Preview](/imgs/View_push_commands.png)

12. Rename the tag 
```
docker tag scr:v1.0 890865287737.dkr.ecr.ap-south-2.amazonaws.com/dockerrepository:1.0
```
![Preview](/imgs/ecr_push.png)

13. Push to ECR Repository
```
docker push 890865287737.dkr.ecr.ap-south-2.amazonaws.com/dockerrepository:1.0
```
![Preview](/imgs/ecr_artifact.png)


_**Note: After completion, ensure to deactivate/delete Access keys from the IAM console in order to avoid any misuse of your AWS account**_


## References

[^1]: _**[Quickstart: Create an Azure container registry using the Azure portal](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal?tabs=azure-cli){:target="_blank"}**_

[^2]: _**[Getting started with Amazon ECR using the AWS Management Console](https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-console.html){:target="_blank"}**_

