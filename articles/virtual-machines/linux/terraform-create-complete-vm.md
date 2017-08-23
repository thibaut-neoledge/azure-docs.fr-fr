---
title: "Créer une infrastructure de base dans Azure à l’aide de Terraform | Microsoft Docs"
description: "Découvrez comment créer des ressources Azure à l’aide de Terraform"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 5377f84a29482e07ae089c7025dc41b8e12bfa3a
ms.contentlocale: fr-fr
ms.lasthandoff: 08/11/2017

---

# <a name="create-basic-infrastructure-in-azure-by-using-terraform"></a>Créer une infrastructure de base dans Azure à l’aide de Terraform
Cet article décrit les étapes nécessaires pour configurer une machine virtuelle, ainsi que l’infrastructure sous-jacente, dans Azure. Vous allez apprendre à rédiger des scripts Terraform et à visualiser les modifications avant de les ajouter à votre infrastructure cloud. Vous allez également apprendre à créer une infrastructure dans Azure à l’aide de Terraform.

Pour commencer, créez un fichier appelé \terraform_azure101.tf dans l’éditeur de texte de votre choix (Visual Studio Code/Sublime/Vim/etc.). Le nom exact du fichier n’est pas important, étant donné que Terraform accepte le nom du dossier en tant que paramètre : tous les scripts contenus dans le dossier sont exécutés. Collez le code suivant dans le nouveau fichier :

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_appId_from_script_execution"
  client_secret   = "your_password_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
Dans la section `provider` du script, vous devez demander à Terraform d’utiliser un fournisseur Azure pour approvisionner les ressources dans le script. Pour obtenir les valeurs de subscription_id, appId, password et tenant_id, reportez-vous au guide [d’installation et de configuration de Terraform](terraform-install-configure.md). Si vous avez créé des variables d’environnement pour les valeurs de ce bloc, vous n’avez pas besoin de l’inclure. 

La ressource `azurerm_resource_group` demande à Terraform de créer un groupe de ressources. Vous trouverez d’autres types de ressources disponibles dans Terraform plus loin dans cet article.

## <a name="execute-the-script"></a>Exécuter le script
Une fois le script enregistré, quittez l’écran pour revenir à la console/ligne de commande et saisissez ce qui suit :
```
terraform init
```
 afin d’initialiser le fournisseur Terraform pour Azure. Puis accédez au répertoire **terraformscripts**et exécutez la commande suivante :
```
terraform plan
```
Nous avons utilisé la commande Terraform `plan`, qui examine les ressources définies dans les scripts. Elle les compare aux informations d’état enregistrées par Terraform et génère ensuite l’exécution planifiée _sans_ créer réellement de ressources dans Azure. 

Après l’exécution de la commande précédente, vous devriez voir un écran similaire au suivant :

![plan Terraform](./media/terraform/tf_plan2.png)

Si tout semble correct, approvisionnez ce nouveau groupe de ressources dans Azure en exécutant la commande suivante : 
```
terraform apply
```
Dans le portail Azure, vous devez voir le nouveau groupe de ressources vide appelé `terraformtest`. Dans la section suivante, vous allez ajouter une machine virtuelle et toute l’infrastructure de prise en charge pour cette dernière dans ce groupe de ressources.

## <a name="provision-an-ubuntu-vm-with-terraform"></a>Approvisionner une machine virtuelle Ubuntu avec Terraform
Nous allons étendre le script Terraform créé avec les détails nécessaires à l’approvisionnement d’une machine virtuelle exécutant Ubuntu. Voici les ressources approvisionnées dans les sections suivantes :

* Un réseau avec un sous-réseau unique
* Une carte d’interface réseau 
* Un compte de stockage avec un conteneur de stockage
* Une adresse IP publique
* Une machine virtuelle qui utilise toutes les ressources précédentes 

Pour obtenir une documentation complète de chacune des ressources Azure Terraform, consultez la [documentation Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

La version complète du [script d’approvisionnement](#complete-terraform-script) est également fournie pour des raisons de commodité.

### <a name="extend-the-terraform-script"></a>Étendre le script Terraform
Étendez le script créé avec les ressources suivantes : 
~~~~
# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}
~~~~
Le script précédent crée un réseau virtuel et un sous-réseau au sein de ce réseau virtuel. Prenez note de la référence au groupe de ressources que vous avez déjà créé par le biais de « ${azurerm_resource_group.helloterraform.name} » dans le réseau virtuel et la définition de sous-réseau.

~~~~
# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}
~~~~
Les extraits de code de script précédents permettent de créer une adresse IP publique et une interface réseau qui utilise l’adresse IP publique créée. Prenez note des références à subnet_id et public_ip_address_id. Terraform dispose d’une intelligence intégrée et comprend ainsi que cette interface réseau a une dépendance vis-à-vis des ressources qui doivent être créées avant l’interface réseau.

~~~~
# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}
~~~~
Ici, vous avez créé un compte de stockage et y avez défini un conteneur de stockage. C’est dans ce compte de stockage que sont stockés les disques durs virtuels (VHD) de la machine virtuelle que vous êtes sur le point de créer.

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
~~~~
Enfin, l’extrait de code précédent crée une machine virtuelle qui utilise toutes les ressources déjà approvisionnées. Il s’agit du compte et du conteneur de stockage pour disque dur virtuel, de l’interface réseau avec adresse IP publique et du sous-réseau spécifié, et du groupe de ressources déjà créé. Prenez note de la propriété vm_size, où le script spécifie une référence SKU Azure A0.

### <a name="execute-the-script"></a>Exécuter le script
Une fois le script complet enregistré, quittez la console/ligne de commande et saisissez ce qui suit :
```
terraform apply
```
Après quelques instants, les ressources (y compris une machine virtuelle) s’affichent dans le groupe de ressources `terraformtest` sur le portail Azure.

## <a name="complete-terraform-script"></a>Script Terraform complet

Pour faciliter votre travail, voici l’intégralité du script Terraform qui configure l’ensemble de l’infrastructure décrite dans cet article.

```
variable "resourcesname" {
  default = "helloterraform"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = "XXX"
  client_id       = "XXX"
  client_secret   = "XXX"
  tenant_id       = "XXX"
}

# create a resource group if it doesn't exist
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}

# create virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "tfvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "tfsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}


# create public IPs
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}

# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Vous avez créé une infrastructure de base dans Azure à l’aide de Terraform. Pour des scénarios plus complexes, y compris des exemples utilisant des équilibreurs de charge et des groupes de machines virtuelles identiques, consultez les nombreux [exemples Terraform pour Azure](https://github.com/hashicorp/terraform/tree/master/examples). Pour obtenir une liste actualisée et complète des fournisseurs Azure pris en charge, consultez la [documentation Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

