# How to deploy and create a new infrastructure on AWS?
Well, we already have our application with the different versions referring to the different versions of the Terraform modules.

Now we need to apply them somehow. We will need a host from which to execute the commands, an AWS account, a directory in which to store the 'Terraform state' file, etc.

![xlrelease image](img_009.png)

## Creating the hosts

The first thing we are going to create in XL Deploy are the hosts on which we have the Terraform client installed and from which we want to execute 'terraform apply' to create our infrastructure. For our example, I am going to imagine that my organization has three hosts enabled to run Terraform templates, each one configured with an AWS account and each one with a different objective. One will serve to create infrastructures for development environments, another host will serve to create infrastructures for pre-production environments, and another one will create infrastructures for production environments.

![xlrelease image](img_010.png)

For each of them I have to setup their IP address, the user to do the login and their private key. I remember that the access credentials to all our infrastructure can be managed directly with XL Deploy, where all sensitive information will be stored encrypted, or it can be managed with tools such as Hashicorp Vault or Ciberark. XL Deploy integrates with them.

## Creating the Terraform clients

The next thing we need are Terraform clients.

![xlrelease image](img_011.png)

We will have to indicate in which path is the binary 'terraform'.

![xlrelease image](img_013.png)

And which working directory to use. **It is in this working directory where the status file will be located after applying the infrastructure.** Therefore, we will need to register as many 'Terraform Clients' as infrastructures we want to create. Why?, because each of these 'Terraform Clients' will be assigned with a unique directory in which to store and update the 'Terraform state' file. In our example, I'm going to choose the directory name depending on the name of the project and the environment I'm provisioning. So, to provision a development environment for the 'calculator' project I will configure the directory
/var/opt/xebialabs/terraform-states/calculator-dev.

![xlrelease image](img_014.png)

That is where I will store the 'Terraform state' file. To name the Terraform client I will follow the same criteria, in this case, the name will be 'calculator-dev'.

If I wanted to manage development, pre-production and production infrastructures for the calculator, petportal and voting-app projects, I would have the following Terraform clients. Each client configured under the corresponding server.

![xlrelease image](img_015.png)

And each client, with its associated working directory to store the 'Terraform state' file.

![xlrelease image](img_016.png)

You can use the next yaml file to create this infrastructure
```
---
apiVersion: xl-deploy/v1
kind: Infrastructure
spec:
- directory: Infrastructure/Terraform
  children:
  - name: terraform-host-dev
    type: overthere.LocalHost
    os: UNIX
    children:
    - name: petportal-dev
      type: terraform.TerraformClient
      workingDirectory: /var/opt/xebialabs/terraform-states/petportal-dev
    - name: voting-app-dev
      type: terraform.TerraformClient
      workingDirectory: /var/opt/xebialabs/terraform-states/voting-app-dev
    - name: calculator-dev
      type: terraform.TerraformClient
      workingDirectory: /var/opt/xebialabs/terraform-states/calculator-dev
  - name: terraform-host-pre
    type: overthere.LocalHost
    os: UNIX
    children:
    - name: voting-app-pre
      type: terraform.TerraformClient
      workingDirectory: /var/opt/xebialabs/terraform-states/voting-app-pre
    - name: calculator-pre
      type: terraform.TerraformClient
      workingDirectory: /var/opt/xebialabs/terraform-states/calculator-pre
    - name: petportal-pre
      type: terraform.TerraformClient
      workingDirectory: /var/opt/xebialabs/terraform-states/petportal-pre
  - name: terraform-host-pro
    type: overthere.LocalHost
    os: UNIX
    children:
    - name: calculator-pro
      type: terraform.TerraformClient
      workingDirectory: /var/opt/xebialabs/terraform-states/calculator-pro
    - name: petportal-pro
      type: terraform.TerraformClient
      workingDirectory: /var/opt/xebialabs/terraform-states/petportal-pro
    - name: voting-app-pro
      type: terraform.TerraformClient
      workingDirectory: /var/opt/xebialabs/terraform-states/voting-app-pro
```

Save it as `infrastructure.yaml` and create the resources ...

```
xl apply -f infrastructure.yaml
```
