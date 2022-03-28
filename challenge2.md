1. Create an AKS cluster

```
az aks create -n myAKSCluster -g teamResources --generate-ssh-keys --attach-acr registryjzg0978
```

2. Get the access credentials for your kubernetes cluster into your cli

```
az aks get-credentials --resource-group teamResources --name myAKSCluster
```

3. Create your deployment file/yaml file. Sample:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: poi-deployment
  labels:
    app: poi-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: poi
  template:
    metadata:
      labels:
        app: poi
    spec:
      containers:
        - name: poi
          image: registryjzg0978.azurecr.io/poi:v1
          ports:
            - containerPort: 80
```

4. To that yaml file we need to also expose our API so it can be used, this can be done by creating a Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: poi-service
  labels:
    app: poi-service
spec:
  ports:
    - port: 6378
    selector:
      app: poi-service
```

Note: Kubernetes supports different types of services. In production applications you generally don't use 'LoadBalancer' instead, you use an Ingres controller (e.x. NGINX Ingress or Azure Application Gateway Ingress Controller) that controls inbound traffic. Cluster internal services would be of type NodePort or ClusterIP

5. We then need to add a loadbalancer which makes our application publicly accessible. In this case we just want to expose the tripviewer code

```yaml
apiVersion: v1
kind: Service
metadata:
  name: tripviewer
  labels:
    app: tripviewer
spec:
  type: LoadBalancer
  ports:
    -port: 80
  selector:
    app: tripviewer
```

NOTE:
Services can have one of the following types

- ClusterIP (default) - IP address internal to the cluster
- NodePort - Port on the parent node's internal cluster IP address. Can be exposed outside the cluster
- LoadBalancer - PRovides access through a load balancer's IP address
- ExternalName -Maps to the external DNS name in the externalName field

Refer to the tripviwer_nginx.yml file to see the complete deployment file

6. Test the application, we need to find the external ip the application is running on e.x.

```
kubectl get service tripviewer --watch
```

and use the external ip that gets returned

7. Upgrade the AKS cluster with Azure Key Vault Provider for Secrets Store CSI Driver Support
(Reference: docs.microsoft.com/en-us/azure/aks/csi-secrets-store-driver)
```
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group teamResources
```
This creates a user-assigned managed identity that you can use to authenticate to your Azure Key vault.

8. Verify the Azure Key Vault Provider for Secrets Store CSI Driver installation
```
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'
```
You need to make sure that a Secrets Store CSI Driver pod and an Azure Key Vault provider pod are running on each node inyour cluster's node pods.