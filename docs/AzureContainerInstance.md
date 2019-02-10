
## How to deploy to Azure Container Instances

### Create container image with docker build
From Visual Studio 2017 build the Ordering.API project in Release mode

### Create container registry
Check this [link](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-tutorial-prepare-acr) for general guidelines

- create resource group

```sh
az group create --name eShopResourceGroup --location eastus
```

- create Azure container registry

```sh
az acr create --resource-group eShopResourceGroup --name ACREShop --sku Free --admin-enabled true
```

 - log in to container registry
 
 ```sh
 az acr login --name ACREShop
 ```

 - tag container image. First get full Azure container registry name, tag the image and check it was properly created
 
 ```sh
 az acr show --name ACREShop --query loginServer --output table
 docker tag orderingapi acreshop.azurecr.io/orderingapi:v1
 docker images
 ```

 - push image to Azure Container Registry
  
  ```sh
  docker push acreshop.azurecr.io/orderingapi:v1
  ```

  - list images or the tags of an image in Azure Container Registry

  ```sh
  az acr repository list --name ACREShop --output table
  az acr repository show-tags --name ACREShop --repository orderingapi --output table
  ```

 ### Deploy the container
 Check this [link](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-tutorial-deploy-app) for general guidelines

 - get registry credentials

 ```sh
 az acr show --name ACREShop --query loginServer
 az acr credential show --name ACREShop --query "passwords[0].value"
 ```

 - deploy container

 ```sh
az container create --resource-group eShopResourceGroup --name orderingapi --image acreshop.azurecr.io/orderingapi:v1 --cpu 1 --memory 1 --registry-login-server acreshop.azurecr.io --registry-username ACREShop --registry-password <acrPassword> --dns-name-label eshop --ports 80
 ```

 - verify deployment progress 

 ```sh
az container show --resource-group eShopResourceGroup --name orderingapi --query instanceView.state
 ```

 - display the container's fully qualified domain name (FQDN)

 ```sh
az container show --resource-group eShopResourceGroup --name orderingapi --query ipAddress.fqdn
 ```

 - open in a web browser the dns name displayed. For example: http://eshop.eastus.azurecontainer.io/api/values

 - view the log output of the container

 ```sh
 az container logs --resource-group eShopResourceGroup --name orderingapi
 ```

  ### Clean the resources

  ```sh
  az group delete --name eShopResourceGroup
  ```