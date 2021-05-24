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

## Deploying react app to AWS - ECS( Amazon Elastic Container Service)

---

### Build container image and push it to ECR - Elastic Container Registry. 


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


### Create cluster ( logical grouping of tasks or services )

Start by creating cluster   
- select networking only.   
- Create VPC or choose existing one.  
- Create the cluster.   

Create Task Definition and select FARGATE ( Serverless compute engine for containers - Application isolation by design ).    
- Select cpu, RAM and container to be used. Also select port : 80.    

Task Definition simply specify container info: how many containers, resources they use, how they are linked and host ports they will use.   



