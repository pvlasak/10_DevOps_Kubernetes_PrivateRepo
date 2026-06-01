# 10_DevOps_Kubernetes_PrivateRepo
this repository is created to deploy application to Kubernetes cluster from a private docker repository

## Docker login from Minikube
- Docker image is available in private AWS ECR repository
- to get authentication token from AWS resitory, following command can be used: *aws ecr get-login-password*. After successful docker login commnad, the authentication token is added to a ~/.docker/config.json file.  
- To ssh to minikube: *minikube ssh*
- Once ssh in minikube, the *docker login --username AWS -p <authentication_token> repositoryURI*
- Content of `config.json` file must be base64 encoded to save credentials inside Secret component: *cat config.json | base64 --wrap-0*
- `config.json` can be also copied from minikube to localhost: *minikube cp minikube:/path localpath* if needed. 

- in `my-app-js-deployment.yaml`, the attribute **imagePullPolicy** forces docker to pull the image from repository everytime the pod gets created. 

- in `my-app-js-deployment.yaml`, attribute **imagePullSecrets** configures the deployment to get access to Secret Component. 

- Approaches how to create a secret component:
   1. apply docker-secret.yaml: *kubectl apply -f docker-secret.yaml*
   2. After copying the config.json from minikube: *kubectl create secret generic my-registry-key --from-file=.dockerconfigjson=.docker/config.json --type=kubernetes.io/dockerconfigjson*
   3. All in one step: *kubectl create secret docker-registry my-registry-key --docker-server=AWS_URI --docker-username=AWS --docker-password=AuthenticationToken*
