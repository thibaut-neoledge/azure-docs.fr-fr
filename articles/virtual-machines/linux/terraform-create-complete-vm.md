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
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: aa0926de32dd85f4460bbfa84b7a6007e2199d51
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

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

# create a resource group if it doesn't exist
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
* Un compte de stockage pour les diagnostics de machine virtuelle
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

    tags {
        environment = "Terraform Demo"
    }
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
        environment = "Terraform Demo"
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

    tags {
        environment = "Terraform Demo"
    }
}
~~~~
Les extraits de code de script précédents permettent de créer une adresse IP publique et une interface réseau qui utilise l’adresse IP publique créée. Prenez note des références à subnet_id et public_ip_address_id. Terraform dispose d’une intelligence intégrée et comprend ainsi que cette interface réseau a une dépendance vis-à-vis des ressources qui doivent être créées avant l’interface réseau.

~~~~
# create a random id
resource "random_id" "randomId" {
  keepers = {
    # Generate a new id only when a new resource group is defined
    resource_group = "${azurerm_resource_group.helloterraform.name}"
  }

  byte_length = 8
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "West US"
    account_type = "Standard_LRS"

    tags {
        environment = "Terraform Demo"
    }
}
~~~~
Ici, vous avez créé un compte de stockage. Ce compte de stockage est là où la machine virtuelle stocke les détails de diagnostics.

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_DS1_v2"

    os_profile {
        computer_name = "hostname"
        admin_username = "azureuser"
        admin_password = ""
    }

    os_profile_linux_config {
        disable_password_authentication = true

        ssh_keys {
            path = "/home/azureuser/.ssh/authorized_keys"
            key_data = "... INSERT OPENSSH PUBLIC KEY HERE ..."
        }
    }

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "16.04.0-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        managed_disk_type = "Premium_LRS"
        create_option = "FromImage"
    } 

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
~~~~
Enfin, l’extrait de code précédent crée une machine virtuelle qui utilise toutes les ressources déjà approvisionnées. Il s’agit du compte et du conteneur pour les diagnostics de la machine virtuelle, de l’interface réseau avec adresse IP publique et du sous-réseau spécifié, et du groupe de ressources déjà créé. Prenez note de la propriété vm_size, où le script spécifie une référence SKU Azure Standard DS1v2.

Vous devez fournir une clé SSH publique. Placez la valeur de clé publique dans la section **... INSERT OPENSSH PUBLIC KEY HERE ...** ci-dessus. Vous pouvez utiliser une paire de clés SSH existante ou suivre la documentation [Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows) ou [Linux/macOS](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys) pour générer la paire de clés.

### <a name="execute-the-script"></a>Exécuter le script
Une fois le script complet enregistré, quittez la console/ligne de commande et saisissez ce qui suit :
```
terraform apply
```
Après quelques instants, les ressources (y compris une machine virtuelle) s’affichent dans le groupe de ressources `terraformtest` sur le portail Azure.

## <a name="complete-terraform-script"></a>Script Terraform complet

Pour faciliter votre travail, voici l’intégralité du script Terraform qui configure l’ensemble de l’infrastructure décrite dans cet article.

```
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

# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}

# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
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

    tags {
        environment = "Terraform Demo"
    }
}

# create a random id
resource "random_id" "randomId" {
  keepers = {
    # Generate a new id only when a new resource group is defined
    resource_group = "${azurerm_resource_group.helloterraform.name}"
  }

  byte_length = 8
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "West US"
    account_type = "Standard_LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_DS1_v2"

    os_profile {
        computer_name = "hostname"
        admin_username = "azureuser"
        admin_password = ""
    }

    os_profile_linux_config {
        disable_password_authentication = true

        ssh_keys {
            path = "/home/azureuser/.ssh/authorized_keys"
            key_data = "... INSERT OPENSSH PUBLIC KEY HERE ..."
        }
    }

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "16.04.0-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        managed_disk_type = "Premium_LRS"
        create_option = "FromImage"
    } 

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Vous avez créé une infrastructure de base dans Azure à l’aide de Terraform. Pour des scénarios plus complexes, y compris des exemples utilisant des équilibreurs de charge et des groupes de machines virtuelles identiques, consultez les nombreux [exemples Terraform pour Azure](https://github.com/hashicorp/terraform/tree/master/examples). Pour obtenir une liste actualisée et complète des fournisseurs Azure pris en charge, consultez la [documentation Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

