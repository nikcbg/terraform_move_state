# terraform_move_state

### Purpose of the repository
- This repository is an example on how to move items in Terraform state. 


### List of the files in the repository
File name |	File description
----------|--------------------
folder1/main.tf	| Terraform configuration code file.

### How to use this repository 
- Install `terraform` by following this [instructions](https://www.terraform.io/intro/getting-started/install.html).
- Clone the repository to your local computer: `git clone git@github.com:nikcbg/terraform_move_state`.
- Go into the cloned repo on your computer: `cd terraform_move_state`.
- Create a new directory(folder1) inside `terraform_move_state` and go into the new directory.
- Into teh new directory folder1 create a `main.tf` file with the following code:

```
resource "random_pet" "name" {
 length    = "4"
 separator = "-"
}

resource "null_resource" "hello" {
  provisioner "local-exec" {
    command = "echo Hello ${random_pet.name.id}"
  }
}

```
- Next execute `terraform init` to download the necessary plugins.
- Next execute `terraform plan` to create execution plan.
- Next execute `terraform apply` to create the new resources.
- The output should display:
```
 null_resource.hello: Creating...
 null_resource.hello: Provisioning with 'local-exec'...
 null_resource.hello (local-exec): Executing: ["/bin/sh" "-c" "echo Hello wrongly-daily-outgoing-camel"]
 null_resource.hello (local-exec): Hello wrongly-daily-outgoing-camel
 null_resource.hello: Creation complete afto download the necessary plugins.ter 0s (ID: 1929874788600161557)

 Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

```
### Module creation and move the state

- Create a new directory in `terraform_move_state` and name it `random_pet`.
- Create a file `module.tf` in `random_pet` directory with the following code:

  ```
   resource "random_pet" "name" {
   length    = "4"
   separator = "-"
   }

  output "display" {
  value = "${random_pet.name.id}"
  }

  ```
- Next edit the `main.tf` file located in `folder1` directory with the following code:

  ```
  module "example"{
  source = "../random_pet"
  }
  resource "null_resource" "hello" {
  provisioner "local-exec" {
  command = "echo Hello ${module.example.display}"
  }
  }

  ```
- Next execute `terraform init` to to download the necessary plugins.
- Next move the `random_pet` resorce in `module.tf` file to the updated `main.tf` module file with the following command:
  - `terraform state mv random_pet.name module.example`, output shoudl display:
  ```
  Moved random_pet.name to module.example
  ```
- Next execute `terraform apply`, output should display:

  ```
  random_pet.name: Refreshing state... (ID: wrongly-daily-outgoing-camel)
  null_resource.hello: Refreshing state... (ID: 1929874788600161557)

  Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

  ```
- You can see that no resources have been created. That's becasue the terraform state remains the same and resources were already created when we ran `main.tf` file. 
 
### To Do: 
- Check if everything works as expected. 
