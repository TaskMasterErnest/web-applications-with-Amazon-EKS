# Deploying on an AWS Fargate Cluster
Fargate is a serverless compute engine that works with the Elastic Container Service and Elastic Kubernetes Service.

Fargate removes the headache of provisioning and managing servers, implements a pay-per-use service for resources used to run applications. It adds a security measure by isolating applications.


## Why Use Fargate?
If you are familiar with AWS Services, you must be as confused as I was initially as to why we are using Fargate to host a frontend application.

I was protesting silently that we could easily opt for an S3 bucket and host the frontend statically but here are the following reasons that convinced me to go with the recommended Fargate option.
- Fargate can be used to automatically scale our applications on demand.
- Fargate can be used to start our failed frontend applications automatically.
- Fargate can be used to load-balance between multiple instances of a frontend application to ensure they handle traffic efficiently.
- We can reduce the cost by running our frontend application on a need-to-use basis.


## Deploying Frontend Pod with Fargate
1. To deploy pods to Fargate in a cluster, you must define a Fargate profile that the Frontend pod will use when it runs.
- A Fargate profile is a configuration that defines which pods run on Fargate and how they run. Fargate profiles are used to control which pods are eligible to run on Fargate, and to specify the resources that are allocated to those pods.
- We will create a manifest to house the Fargate profile creation YAML.

2. Deploy Fargate profile
```bash
eksctl create fargateprofile -f eks-demo-fargate-profile.yaml
```

3. Check the status Fargate profile to ascertain if it was successfully deployed
```bash
eksctl get fargateprofile --cluster eks-demo -o json
```

4. Delete the frontend Deployment. We will be porting the frontend Deployment to the Fargate type.
```bash
kubectl delete -f 05-microservices/frontend-manifests/frontend-deployment.yaml
```

5. Modify the frontend-deployment file. In this new Deployment, the label is changed to suit the Label specified in the Fargate profile ie `app:frontend-fargate`.
- Modify the frontend-service YAMl manifest file to have the same label for the Fargate profile too.

6. Deploy the manifest files with these commands:
```bash
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-service.yaml # this will cause a configuration change.
```

7. Check where the new Deployment has been provisioned with either of these commands:
```bash
# the demo-frontend Pods are provisioned at a nominated node
kubectl get pod -o wide

# check the list of Fargate worker nodes
kubectl get nodes -l eks.amazonaws.com/compute-type=fargate
```

8. Check the Frontend to see if it is serving with the result of this command.
```bash
echo http://$(kubectl get ingress/frontend-ingress -o jsonpath='{.status.loadBalancer.ingress[*].hostname}')
```