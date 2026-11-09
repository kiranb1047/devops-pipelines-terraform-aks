# Creating an AKS(Azure Kubernetes Cluster) using Terraform and Azure DevOps Pipelines

Automate the creation of a secure AKS cluster using Terraform and Azure Key Vault. Leverage Azure DevOps Pipelines for continuous deployment, streamlining your workflow.

# 📖 About the Project Components

* The services that were used during the course of this Projects are as follows:
  * **Azure DevOps Pipelines** - Azure DevOps Pipelines are essentially automated workflows for your code. When you make changes, Pipelines can handle the routine tasks like building, testing, and deployment, freeing you to focus on development. Think of it as an automatic process that gets your code ready for release.
  * **Microsoft Entra ID** - Microsoft Entra ID is your digital work ID card. It simplifies your work life by letting you use one login to access all your approved work apps and resources, like Microsoft 365 or internal company tools. This eliminates juggling multiple passwords and keeps things secure with features like multi-factor authentication.
  * **Azure Key Vault** - Azure Key Vault is a secure cloud storage for sensitive information used by your applications, like passwords, encryption keys, and certificates. It keeps them encrypted, controls access, and simplifies management.  Think of it as a safe deposit box for your cloud programs' secrets.
  * **Terraform** - Terraform is an infrastructure-as-code tool. Instead of clicking through the Azure portal, you describe the resources you want in configuration files, and Terraform works out what to create or change. In this project it provisions the resource group, virtual network, subnet and AKS cluster, reading the SSH public key and service principal credentials from Azure Key Vault. The pipeline runs 'terraform init', 'plan' and 'apply' to deploy it.

# 📖 Prerequisites

### Sign up for Azure and Azure DevOps and set up a new workspace for your project

  ```
  https://azure.microsoft.com/en-us/
  https://azure.microsoft.com/en-us/products/devops
  ```
# 🚀 Installation

### 🛠️ Creating a secure link in Azure DevOps that allows the YAML pipeline to access your Azure resources 

  * On Microsoft Entra ID, go to **App Registration** and set up a new application. This will create a unique identity for your application within the system.
  * Generate a application secret for your newly created application.
  * Add a new **role** in the **role assignment** section of the blade for the newly created DevOps application to create resources in Azure. 
      * *Note* the app ID and secret code we just created. You'll need them later to give your new application permission to access the Key Vault in the next step and in the PowerShell script.
  * In Azure DevOps, create a new **Service Connection** with Azure Resource Manager using service principal (manual).
      * Fill in the details about your Azure subscription and the service principal. This includes your subscription ID, name, the application ID, its app secret, and the tenant ID. Once everything's filled in, click "verify" to create the secure connection.     

### 🛠️ Storing secrets in Azure Key Vault and assign it an access policy

  * By running the custom PowerShell script *ssh-appid-keyvault.ps1* you will create the following:
     * A **Key vault** within a new **Resource group**.
     * An **SSH key** that lets you securely connect to your cluster's agent nodes without needing a password.
     * Use the **Set-AzKeyVaultSecret** to safely store secrets in the Key vault. 
     * Assign **Access policy** to yourself and the service principal to grant permission to fetch secrets automatically from the Key Vault using the **Set-AzKeyVaultAccessPolicy** cmdlet.
     * *Make the changes to the script as mentioned and execute the script in Azure PowerShell.*

### 🛠️ Setting up the Terraform template
   
   * From the *main.tf* file, we'll grab the information we stored in **Azure Key Vault** earlier. This includes the *SSH public key**, the **service principal ID**, and the **secret**
   * Then you will create a resource group for the AKS cluster, virtual network, subnet and the AKS cluster.
   * Finally we will provide the desired variable assignments for the parameters in the **values.auto.tfvars** file.

### 🛠️ Setting up the pipeline to create an AKS cluster using Terraform code.

  * Save your infrastructure code in Terraform files. You can then store these files in a version control tool like GitHub or Azure Repos, just like you would store any other code project.  
  * On your Azure Organization click on Pipelines and then Create Pipeline.
  * Integrate with your Git repository where you have stored the Terraform files.
  * Create a **Starter Pipeline**, use the **YAML** script *azure-pipelines.yml*.
  * The automated deployment process in **YAML** will use the build agent to run Terraform commands. These commands will first **initialize** the Terraform environment, then create a **plan** for the infrastructure changes, and finally **apply** those changes to deploy the resources to Azure.     
  * Lastly for this phase we run the pipeline.

### 🛠️ Running tests to ensure the deployment is functional

  * Once the pipeline finishes without errors, check the Azure portal to confirm resource deployment.
  * Use this command in the Azure CLI to connect to your AKS cluster:
    ```
    az aks get-credentials --resource-group akscluster-rg --name akscluster
    ```
  * Check if kubectl can connect to the cluster and see 2 nodes by running this command:
    ```
    kubectl get nodes
    ```
    
# Project background

These projects are hands-on labs I completed while studying for Microsoft's AZ-104 (Azure Administrator) material. The ARM templates (files starting with `az104-`) come from the Microsoft lab content. I deployed them myself in my own Azure subscription, configured the networking, security rules and DNS, and documented the results here.

# Conclusion

Once you've followed these steps, you'll have a brand new cluster of computers running Kubernetes in Azure! You built it automatically using Terraform and Azure DevOps Pipelines
