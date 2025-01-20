+++
authors = ["Lee Williams"]
title = "Terraform? That's a puppet show from the 80's right?"
date = 2025-01-20T08:35:03Z
draft = false
description = "Learn how I found Terraform, the memories it evoked, and why it's an amazing bit of kit"
tags = ["IaC", "Web Development"]
+++
# Terraform? That's a puppet show from the 80's right?

I understand your confusion. But, nope. Sorry.

You are thinking of the 80's Gerry Anderson classic Terrahawks - I remember watching it on a Sunday evening at my Great Nan's. Dr "Tiger" Ninestein and his fellow Terrahawks protecting the earth from Zelda (no not that one!).

Anyway I'm getting sidetracked. A better comparison might have be (yes I'm holding with the puppet theme) Star Fleet (NO, not THAT one!), where three vehicles can form up to make a super robot, it is Japanese in origin and called X-bomber there. Well, suffice to say, little Lee could not get enough of it. But now... I've truly digressed... so...

Where were we? Ah yes Terraform.

At it's root, Terraform is an Infrastructure as code language that allows you to build complex infrastructure using principles of good development. This gets you repeatable infrastructure built in ways that allow reusability and modularity. Terraform maintains a state so it can manage the infrastructure to ensure changes are applied predicatably. It supports a wide array of cloud infrastructure, SaaS tools, Virtualbox all sorts!

Best of all it's open source and extensible!


## Brief background on Infrastructure as Code (IaC).
"Back in the day" we had on premise servers only. And changes were - if you were lucky enough - carefully managed by your administrative team. And these folks completed change request processes made alterations and kept things working like clockwork, ok sometimes changes sneaked past - nothing is ever perfect etc.

However, the point being - as time marched on people used script languages to automate common tasks - and realised the benefits of this approach - repeatability, version control to name two.

Infrastructure as Code (IaC) is a modern approach to managing and provisioning IT infrastructure through code, rather than manual processes. Traditionally, setting up servers, networking, and storage required time-consuming, manual configurations prone to human error. With IaC, infrastructure is defined using code, enabling automation, consistency, and repeatability across environments.

By leveraging IaC tools like Terraform, organizations can version control their infrastructure, apply changes in a controlled manner, and rapidly deploy environments on-demand. This approach aligns with DevOps practices, promoting collaboration between development and operations teams while improving scalability and efficiency.

Popular IaC tools include Terraform, AWS CloudFormation, and Azure Bicep, each offering unique capabilities tailored to different cloud providers and environments. However, Terraform stands out due to its cloud-agnostic nature, allowing teams to manage resources across multiple cloud platforms with a unified workflow.

## Why Terraform?
My initial experience with Terraform came while working on AWS infrastructure, where I needed a repeatable and reliable way to manage cloud resources. The declarative coding style of Terraform felt familiar, resembling other languages I’m comfortable with, making it an intuitive choice for infrastructure automation.

Having used Terraform successfully with AWS, I was eager to test its cloud-agnostic capabilities by applying the same skills to Azure. This flexibility was a key factor in my decision, as it offered the potential to manage multi-cloud environments without having to learn an entirely new toolset.

One of Terraform’s biggest advantages is its ability to create repeatable environments. Whether setting up a new deployment or recovering from an issue, I can quickly rehydrate infrastructure with minimal effort. This not only enhances efficiency but also strengthens disaster recovery and consistency across different stages of development.

Additionally, Terraform’s strong community support and widespread popularity were crucial in my choice. With a rich ecosystem of providers, modules, and active discussions, I knew I could find support and best practices to guide my implementations.

## Setup and Configuration
Setting up Terraform on my Windows machine was a smooth process, thanks to Chocolatey, a powerful package manager for Windows. For those who haven't come across it, Chocolatey simplifies software installation and management, allowing you to install applications via the command line—similar to Homebrew for macOS or APT for Linux. I'd highly recommend you check it out!

To install Terraform, I used the following command:

```powershell
choco install terraform
```

Chocolatey made it easy to get up and running quickly, and updating Terraform is just as simple with:

```powershell
choco upgrade terraform
```

For this project, my focus has been exclusively on Azure, configuring the provider to authenticate securely using a service principal. Terraform's modular approach allowed me to break down infrastructure into reusable components early on, which made it easier to scale and manage complexity as the project progressed. This modular structure helped streamline deployments and maintain consistency across environments.

Currently, I am managing Terraform state locally, which works well for my needs. However, as the project grows, I plan to explore remote state management (e.g., using Azure Blob Storage) to enhance collaboration and avoid potential issues with state drift. Similarly, I haven't implemented workspaces yet, but I see their potential in managing multiple environments like development, staging, and production more effectively.

By focusing on modularity from the start and leveraging Terraform’s declarative approach, I’ve found it easier to iterate on infrastructure configurations without introducing unnecessary complexity.


## Learning the Ropes.
The Hashicorp documentation and community of Terraform helped me get started easily, and when I got stuck the answer was never far away.

To be fair though, I've not really pushed boundaries of this tech my use case was straightforward - I look forward to digging in further.

## Specific use cases you’ve implemented.
I started creating a development environment for a project I'm working on. I want a headless wordpress backend and a frontend development using a popular framework (react/vue/svelte etc. - give me some ideas on what to evaluate) so I created a vnet with private/public address range(s), created a virtual machine and ensured it was running Linux and during "spin-up" it installed latest security patches - also I made sure that it used an existing SSH public key for remote access.


## Tips for structuring code, managing state, and avoiding common pitfalls.
I started - like everyone does I think - with one Terraform file. And it grew. And grew. And grew - it grew, like a tumour, like meningococcal meningitis - and became unwieldly - so I decided to look into how Terraform recommends we deal with this moving forwards. I'm sure the way I chose is not the only way, but I broke it down into modules.

## Summary of Module Usage
In this Terraform Azure infrastructure project, modules are used to enhance modularity, maintainability, and reusability of the codebase. The modules I created  rely on input variables to make configurations flexible and reusable across different environments, while output variables provide key values, such as resource IDs and connection details, to be used by other modules or for external reference.

**For example, in this project:**

Input variables allow customization of key parameters such as resource names, locations, and networking configurations without altering the core module logic.
Output variables provide essential details like the public IP of the virtual machine, making it easy to retrieve necessary information after deployment.
The infrastructure is broken down into three key modules:

**Resource Group Module:**

Handles the creation of a resource group to encapsulate all Azure resources.
Takes parameters for the name and location to allow flexibility across environments.
Network Module:

Provisions a virtual network with both public and private subnets.
Incorporates a network security group (NSG) to restrict access based on allowed IP ranges.
Outputs subnet IDs for easy reference in other modules, ensuring interconnectivity.

**Compute Module:**

Manages the deployment of a Linux virtual machine with configurable options such as SSH access, public IP enablement, and subnet association.

Outputs the public IP of the VM for easy access post-deployment.

This modular structure allows for scalability and easier management by isolating different components of the infrastructure, enabling updates or changes without affecting the entire deployment. It's not perfect by any means, we'd further parameterise the inputs and outputs required to make this truly scale - but have that standard infrastructure we require across the board which ensure the risk of mistakes in configuration are minimal. It's a fine line between usability and making things follow your standards. If I'm honest it's a whole other debate if you are talking about how to manage misconfigurations in which IaC is just part of the puzzle.

### A sample module from my set up
The Compute Module, which provisions the Linux virtual machine, is one of the more interesting components as it involves multiple dependencies, such as networking and authentication.

Module Structure
```css
modules/
└── compute/
    ├── main.tf
    ├── variables.tf
    ├── outputs.tf
```

1. main.tf (Core Compute Logic)
```hcl
resource "azurerm_linux_virtual_machine" "vm" {
  name                  = var.vm_name
  resource_group_name   = var.resource_group_name
  location              = var.location
  size                  = "Standard_B1s"
  admin_username        = "azureuser"
  network_interface_ids = [azurerm_network_interface.vm_nic.id]

  admin_ssh_key {
    username   = "azureuser"
    public_key = file(var.ssh_key_path)
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }
}

resource "azurerm_network_interface" "vm_nic" {
  name                = "${var.vm_name}-nic"
  location            = var.location
  resource_group_name = var.resource_group_name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = var.subnet_id
    private_ip_address_allocation = "Dynamic"

    public_ip_address_id = var.public_ip_enabled ? azurerm_public_ip.vm_public_ip[0].id : null
  }
}

resource "azurerm_public_ip" "vm_public_ip" {
  count               = var.public_ip_enabled ? 1 : 0
  name                = "${var.vm_name}-public-ip"
  resource_group_name = var.resource_group_name
  location            = var.location
  allocation_method   = "Dynamic"
}
```

2. variables.tf (Input Variables)
```hcl
variable "vm_name" {
  description = "The name of the virtual machine"
  type        = string
}

variable "resource_group_name" {
  description = "The name of the resource group"
  type        = string
}

variable "location" {
  description = "Azure region"
  type        = string
}

variable "subnet_id" {
  description = "The subnet ID where the VM will be deployed"
  type        = string
}

variable "public_ip_enabled" {
  description = "Whether to enable a public IP for the VM"
  type        = bool
  default     = false
}

variable "ssh_key_path" {
  description = "Path to the SSH public key"
  type        = string
}
```

3. outputs.tf (Outputs for External Use)
```hcl
output "public_ip" {
  description = "The public IP address of the VM"
  value       = var.public_ip_enabled ? azurerm_public_ip.vm_public_ip[0].ip_address : null
}

output "vm_id" {
  description = "The ID of the virtual machine"
  value       = azurerm_linux_virtual_machine.vm.id
}
```

#### Key Takeaways from Using Modules in this Project
**Modularity:**
Breaking down infrastructure into smaller, reusable modules simplifies maintenance and enhances readability.

**Separation of Concerns:**
Each module focuses on a specific aspect of the infrastructure (e.g., networking, compute), making it easier to update or extend specific components independently.

**Reusability:**
Modules allow for consistent deployments across multiple environments by simply adjusting variable inputs without modifying the core logic.

**Scalability:**
The modular design supports scaling by reusing modules in multiple projects with different configurations.

## Future Plans
I want to ensure in the future that any key I do use for SSH, is also protected by only allow listing only my public IP. It does do this now, but every time my router resets I have to update the infrastructure which in the "real" world is not a tenable solution. I will have to pay for a static IP for this to be a sensible option - currently I do not do that, but I'm changing providers soon to one that offers it so at a discounted price based on my existing provider I now also will have a static IP.

I also want to use Terraform in my CI/CD pipeline to keep changes "fleet of foot" for users of my upcoming project.

For now, I'm signing off.

Leeksy.