# terraform_move_state
This is a guide on HOW to use **move_state** in Terraform.
We have the alredy created project with help of **null_resource** and **random_pet**.
Our goal is to move already created resource **random_pet** to different module and to different state, without destroying.
Follow the steps in section **Step by step guideline**

## Files
- `main/main.tf` - terraform configuration file 
- `random_pet/module.tf` - terraform file for random_pet resource (Currently not in the repo. It will be created by following the instructions in **Step by step guideline** section )

## Requirements
Terraform installed
If you do not have Terraform installed on your computer, install it from [here](https://learn.hashicorp.com/terraform/getting-started/install.html)



## Step by step guideline

 **Part 1 - create a simple project:**
- Download the repo **berchev/terraform_move_state**: `git clone https://github.com/berchev/terraform_move_state.git`
- Change to terraform_move_state: `cd terraform_move_state`
- Change into **main** directory: `cd main`
- Type: `terraform init` in order to install required providers
- Type: `terraform apply` in order to provision the changed made in **main/main.tf** file

**Part 2 - create module and move the state:**
- In order to move the state, please create new directory named **random_pet** into **terraform_move_state directory**: `mkdir random_pet`
- Create **module.tf** file into **random_pet** directory with the following content:
```
resource "random_pet" "name" {
  length    = "4"
  separator = "-"
}

output "out" {
  value = "${random_pet.name.id}"
}
```
- Edit file **main.tf** located in the **main** directory as follows:
```
module "move_state" {
  source = "../random_pet"
}

resource "null_resource" "hello" {
  provisioner "local-exec" {
    command = "echo Hello ${module.move_state.out}"
  }
}
```
- Type: `terrafom init`
- Type: `terraform state mv random_pet.name module.move_state`. This command is going to move current state of **random_pet** resource to newly created module
- Type: `terraform apply` and you should notice that nothing will be created and state should persist. The output should be like this:
```
gberchev@BerchevPC main (setup) $ terraform apply
random_pet.name: Refreshing state... (ID: explicitly-wholly-liberal-mullet)
null_resource.hello: Refreshing state... (ID: 1684903708041524439)

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.
gberchev@BerchevPC main (setup) $ 
```
- Type: `terraform destroy` in order to delete the existing state and resources

## TODO
