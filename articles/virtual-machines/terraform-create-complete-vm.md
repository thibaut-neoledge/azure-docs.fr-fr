---
title: "Créer une infrastructure de base dans Azure à l’aide de Terraform | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 8b8b3b0b46f79058ee69b9a2014581df433f8d3f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="create-basic-infrastructure-in-azure-using-terraform"></a>Créer une infrastructure de base dans Azure à l’aide de Terraform
Cet article décrit les étapes nécessaires pour configurer une machine virtuelle, ainsi que l’infrastructure sous-jacente, dans Azure. Vous allez apprendre à créer des scripts Terraform et à visualiser les modifications avant de les ajouter à votre infrastructure cloud. Vous allez également apprendre à créer une infrastructure dans Azure à l’aide de Terraform.

Pour commencer, dans l’éditeur de texte de votre choix (Visual Studio Code/Sublime/Vim/etc.), créez un fichier appelé _terraform_azure101.tf_. Le nom exact du fichier n’est pas important, étant donné que terraform accepte le nom du dossier en tant que paramètre : tous les scripts contenus dans le dossier sont exécutés. Collez le code suivant dans ce nouveau fichier :

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_client_id_from_script_execution"
  client_secret   = "your_client_secret_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
Dans la section provider du script, vous devez demander à Terraform d’utiliser un fournisseur Azure pour approvisionner les ressources dans le script. Reportez-vous au guide d’[installation et de configuration de Terraform](terraform-install-configure.md) pour obtenir les valeurs de subscription_id, client_id, client_secret et tenant_id. Si vous avez créé des variables d’environnement pour les valeurs de ce bloc, vous n’avez pas besoin de l’inclure. 

La ressource azure_rm_resource_group demande à Terraform de créer un groupe de ressources. Vous trouverez d’autres types de ressources disponibles dans Terraform ci-dessous.

## <a name="executing-the-script"></a>Exécution du script
Une fois le script enregistré, quittez l’écran pour revenir à la console/ligne de commande et tapez
```
terraform plan terraformscripts
```
Dans l’exemple ci-dessus, nous supposons que « terraformscripts » désigne le dossier dans lequel le script a été enregistré. Nous avons utilisé la commande Terraform « plan », qui examine les ressources définies dans les scripts, les compare aux informations d’état enregistrées par Terraform et génère ensuite l’exécution planifiée _sans_ créer réellement de ressources dans Azure. 

Vous devez voir un écran du type suivant après avoir exécuté la commande ci-dessus

![Image de Terraform Plan](linux/media/terraform/tf_plan2.png)

Tout semble correct. Poursuivez et approvisionnez ce nouveau groupe de ressources dans Azure en exécutant 
```
terraform apply terraformscripts
```
Si vous accédez maintenant au portail Azure, vous devez voir le nouveau groupe de ressources vide appelé « terraformtest ». Dans la section ci-dessous, vous allez ajouter une machine virtuelle et toute l’infrastructure de prise en charge pour cette machine virtuelle dans ce groupe de ressources.

## <a name="provisioning-ubuntu-vm-with-terraform"></a>Approvisionnement de la machine virtuelle Ubuntu avec Terraform
Nous allons étendre le script Terraform créé ci-dessus avec les détails nécessaires à l’approvisionnement d’une machine virtuelle exécutant Ubuntu. Voici les ressources que vous allez approvisionner dans les sections ci-dessous : réseau avec sous-réseau unique, carte d’interface réseau, compte de stockage avec conteneur de stockage, adresse IP publique, et machine virtuelle utilisant toutes les ressources ci-dessus. Pour obtenir une documentation complète de chacune des ressources Azure Terraform, consultez la [documentation Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

La version complète du [script d’approvisionnement](#complete-terraform-script) est également fournie pour des raisons de commodité.

### <a name="extending-the-terraform-script"></a>Extension du script Terraform
Étendez le script créé ci-dessus avec les ressources suivantes : 
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
Le script ci-dessus crée un réseau virtuel et un sous-réseau au sein de ce réseau virtuel. Prenez note de la référence au groupe de ressources que vous avez déjà créé par le biais de « ${azurerm_resource_group.helloterraform.name} » dans le réseau virtuel et la définition de sous-réseau.

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
Les extraits de code de script ci-dessus permettent de créer une adresse IP publique et une interface réseau qui utilise l’adresse IP publique créée. Prenez note des références à subnet_id et public_ip_address_id. Terraform dispose d’une intelligence intégrée et comprend ainsi que cette interface réseau a une dépendance vis-à-vis des ressources qui doivent être créées avant l’interface réseau.

~~~~
# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
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
Vous venez de créer un compte de stockage et de définir un conteneur de stockage au sein de ce compte de stockage, là où vous stockez des VHD pour la machine virtuelle qui est sur le point d’être créée.

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
Enfin, l’extrait de code ci-dessus crée une machine virtuelle qui utilise toutes les ressources que nous avons déjà approvisionnées : compte de stockage et conteneur pour un disque dur virtuel (VHD), interface réseau avec adresse publique IP et sous-réseau spécifié, ainsi que le groupe de ressources que vous avez déjà créé. Prenez note de la propriété vm_size, où le script spécifie une référence SKU Azure A0.

### <a name="executing-the-script"></a>Exécution du script
Une fois le script complet enregistré, quittez l’écran pour revenir à la console/ligne de commande et tapez
```
terraform apply terraformscripts
```
Patientez quelques instants pour voir les ressources, y compris une machine virtuelle, qui apparaissent dans le groupe de ressources « terraformtest » sur le portail Azure.

## <a name="complete-terraform-script"></a>Script Terraform complet

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

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
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
Vous avez créé une infrastructure de base dans Azure à l’aide de Terraform. Pour des scénarios plus complexes, y compris des exemples d’utilisation des équilibreurs de charge et des groupes de machines virtuelles identiques, consultez les nombreux [exemples Terraform pour Azure](https://github.com/hashicorp/terraform/tree/master/examples). Les [documents Terraform](https://www.terraform.io/docs/providers/azurerm/index.html) contiennent une liste actualisée et complète des fournisseurs Azure pris en charge.
