# Deploying and Using Private Terraform Enterprise on Azure
Many Azure customers have adopted open-source [Terraform](https://www.terraform.io) as the infrastructure provisioning tool of choice. We have worked closely in partnership with HashiCorp, the company behind Terraform, to ensure that support for Terraform in Azure is first-class, and momentum we are seeing indicates that we are indeed headed in that direction. What we have also observed is that once customers realize the benefits that Terraform delivers for infrastructure provisioning and are looking to adopt that tool enterprise-wide, questions about collaboration and governance emerge. The Enterprise version of Terraform (generally referred to as Terraform Enterprise) is designed to solve the challenges of using Terraform in team environments while operating within the enterprise regulatory constraints.

In this blog post, you will learn the basics of installing and running Terraform Enterprise in Azure, as well discover the features that the enterprise version of Terraform brings on top of the open source version. But first, let's go over why you would want to run Terraform Enterprise in Azure.
 
## Reasons for Running Terraform Enterprise in Azure
A significant number of joint Microsoft and HashiCorp customers (especially in financial and healthcare services) operate within regulatory environments where they must be completely in charge of how  and where their cloud infrastructure gets deployed. That includes knowing that their data, including data about the infrastructure, is in certain geographic locales, as well having the ability to scale the infrastructure up (or down) as needed. Additionally, customers request varying degrees of availability necessary to meet their internal SLAs. Installing Terraform Enterprise on Azure (also referred to as Private Terraform Enterprise on Azure) allows customers to achieve this flexibility.

## Installing and Running Terraform Enterprise in Azure
The basics of installing Terraform in Azure are straightforward - you download an executable, then install and configure it on your infrastructure. HashiCorp has a [detailed blog](https://www.terraform.io/docs/enterprise/private/azure-setup-guide.html) post that walks through the installation and various configuration and availability options in Azure. Below, I am highlighting what I feel are the important parts of the installation process in Azure.

First, you have three configuration options for deploying Terraform Enterprise in Azure: _demo_, _production_ and _high-availability production_ (also called [Production-External Services](https://www.terraform.io/docs/enterprise/private/install-installer.html#operational-mode-decision)). The option you choose will determine how resilient Terraform Enterprise will be. The deployment option will also dictate how involved the installation process will be: from a single-server deployment with no external dependencies for the demo to multiple servers configured with PostgreSQL database for the high-availability scenarios. As always, spending some time planning the deployment out and making sure it conforms to enterprise governance policies is paramount.

Second, you will need an SSL certificate for clients to securely communicate with the server. You should follow your enterprise policies for obtaining one and have that ready before installation. Try and avoid self-signed certificates; if your enterprise policy allows that, you can use Terraform Enterprise built-in integration with Let's Encrypt service for getting your certs during the installation process.

Finally, it's important to note the minimum and the recommended requirements for running Terraform Enterprise in Azure. The D-series machines are recommended, and machines with burstable CPUs (B-series) should be avoided for Terraform Enterprise. Note that you also need at least 50Gb of disk storage - if you are installing a demo environment from the Azure portal, remember to change from the default disk size of 30Gb.

Once you have decided on the installation type (demo vs prod) and obtained the SSL certificates, follow the [HashiCorp Terraform Enterprise Setup Guide](https://www.terraform.io/docs/enterprise/private/azure-setup-guide.html) to install Terraform Enterprise on Azure.
 
## Features of Terraform Enterprise
While this might be reHashing the obvious (pun intended), it’s worth emphasizing that Terraform Enterprise builds on top of the open-source version of Terraform. All the features and HCL configs that work with the open source version will continue working with Terraform Enterprise. What you gain with Terraform Enterprise are governance and collaboration features, so let’s look at what those are.

### Governance with HashiCorp Sentinel
In 2017, HashiCorp introduced Sentinel, a language that can be used across the HashiCorp suite of products (Vault, Nomad and Consul, in addition to Terraform) to ensure that enterprise governance guidelines are adhered to. In the case of Terraform, using Sentinel means feeling certain that infrastructure deployments meet enterprise criteria for infrastructure deployments. In the simplest example below, Sentinel policy, defined using its own Sentinel language, ensures that all VMs deployed to Azure contain tags:

```
import "tfplan"
main = rule {
  all tfplan.resources.azurerm_virtual_machine as _, vms {
    all vms as _, r {
      (length(r.applied.tags) else 0) > 0
    }
  }
}
```

With Sentinel, you can specify elaborate enterprise infrastructure deployment policies as code and have those live alongside your infrastructure as code (HCL) artifacts, ready for deployment and versioning across the number of dev, stage and production environments. Sentinel is available with Terraform Enterprise in Azure.

### Collaboration Features of Terraform Enterprise
[Terraform Enterprise](https://www.hashicorp.com/products/terraform#features) has multiple collaboration features that will benefit teams of any size. Here, I’d like to highlight just two that teams could benefit from immediately.

First, remote state management and locking. ReHashing the obvious again, Terraform maintains state of your infrastructure and it’s important that this state be shared by multiple team members without stepping on each other’s toes. While not strictly a feature of Terraform Enterprise, remote state management accomplishes this state sharing and gives your team a collaborative, shared environment, pre-configured and ready to use.

Second, version control. Any conversation about code, including Infrastructure as Code, must have a pre-requisite conversation about versioning and source control. Terraform Enterprise integrates with your existing version control system, allowing you to visually track, branch and rollback your infrastructure as needed.

We are very pleased to give our customers the ability to [run Terraform in Azure](https://www.terraform.io/docs/enterprise/private/azure-setup-guide.html).