# Docker container with react app for Production

## Add react app to docker container - ready for production

Create a new react app
```
npx create-react-app my-app
```
Add nginx as production-grade web server - nginx/nginx.conf.   
Add Dockerfile.prod and .dockerignore for building multi-stage docker image.   
Build image 
```
docker build -f Dockerfile.prod -t my-first-image:latest .
```
Run the image 
```
docker run -it -p 80:80 --rm my-first-image:latest
```
Check in browser http://localhost:80 to see app being served from docker container.   

---

## Deploying react app to AWS - ECS( Amazon Elastic Container Service)

---

### Build container image and push it to ECR - Elastic Container Registry. 

---

**Amazon ECR service** is fully managed container registry for storing, managing, sharing and deploying container images and artifacts (similiar to dockerhub).

Steps:

1. In the AWS - ECR web console, create repository.

2. Create access key in MY SECURITY CREDENTIAL SECTION

3. Run:

    ```
    aws configure
    ```
    and enter those keys.

3. In AWS ECR -  **View push commands**  'for created repo will displays commands that need to be run inside localhost AWS CLI to push image to the registry. Run these commands (check if Dockerfile name is correct in the command).

---

### Create cluster ( logical grouping of tasks or services )

Start by creating cluster

- select networking only.
- Create VPC or choose existing one.  
- Create the cluster.

Create Task Definition and select FARGATE ( Serverless compute engine for containers - Application isolation by design ).

- Select cpu, RAM and container to be used. Also select port : 80.

Task Definition simply specify container info: how many containers, resources they use, how they are linked and host ports they will use.

---

### Set ELB - Elastic Load Balancer

Elastic load balancer automatically distributes incoming traffic across multiple targets (such as EC2 instances, containers, addresses to one or more Availability zones ).   
Using load balancer in the app increases app fault tolerance and its availability.

- In EC section, find and create a load balancer 
- Select HTTP/HTTPS option
- Create new Security Group / Configure Routing
- Select VPC - Availability zones
- Register targets and create ELB now
- Once created, we can create service that will run the task-definition created in the cluster

---

### Create service to run the task

- In Clusters/Services-tab select create new and select FARGATE as launch type
- Select VPC , container and add to load balancer option
- Review and create service.
- Once done, status active should appear. The app is now served on the IP-Dns address provided in load balancer section.

---

### Attach domain to load balancer

Unlike EC2, Lightsail, etc.,static IP can not be attached with an Elastic Load balancer.   
This can be done either with using CNAME record or using NS record. Hovewer, CNAME can be added only for subdomains, not for top level domains.

For NS record :

- Go to AWS Route 53 and create hosted zone - Public Hosted Zone
- Create record set - A - IPv4 -Alias -yes and select attached container as Alias Target - Create
- Copy created records and add them to the domain registrar - nameservers. Wait for propagation.

---

### Attach SSL Certificate to the load balancer and enable https

- In AWS console, Certificate manager, select Provision Certificate - Request Public Certificate
- Enter domain or subdomains - Review and confirm request. If Route 53 is used , follow instructions and add CNAME records in the Hosted Zone for the domain in question
- When certificate is not pending validation but Issued, attach https listener to the load balancer
- Edit inbound rules and add https rule in Load Balancer / Security Groups / Edit inbound rules
- Thats all, visit https:// version of the site and if everything went well, the site should now be live and served securely via https

### Set up CI/CD using AWS CodeBuild & AWS CodePipeline with GitHub

In the AWS console, create project with CodeBuild

- Connect it with github and add required repository
- Select 'Managed image', with new service role and select Buildspec file. Buildspec is basically file that tells the provider info about build.
- Allow CodeBuild access AWS ECR repository
- Create policy that allows codebuilder access ECR and attach it to it

AWS CodePipelane

- From the console, create Pipeline
- Add github as a source
- Select "AWS CodeBuild" as the Build provider
- Select "Amazon ECS" as the Deploy provider
- After creating the pipeline it will start to build & deploy automatically (the first time). Now, whenever you push to the master branch (or branch you provided earlier) pipeline will be triggered automatically
- You can set up notification rules using AWS Chatbot with Slack or use SNS with any other service.

Continous Integration

CI comes into play when we want to merge branches/PRs etc, and that requires you to write test cases that are executed before merging.

To implement CI, we will use Github Workflows

- From the project root dir:

```
mkdir .github
cd .github
mkdir workflows
touch react.yml
```

- Commit the changes and push to Github
- Now, whenever I make changes to the code and create new PRs it will run test cases automatically. I can check my workflow by going to the "Actions" tab on Github.

Project Sources:

- [Continuous Delivery and Continuous Deployment](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment)
- [React - Docker -AWS - CI/CD app](https://dev.to/mubbashir10/containerize-react-app-with-docker-for-production-572b)
