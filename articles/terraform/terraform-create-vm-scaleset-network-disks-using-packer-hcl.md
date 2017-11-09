---
title: "Utiliser Terraform pour créer un groupe de machines virtuelles Azure identiques à partir d’une image Packer personnalisée"
description: "Utilisez Terraform pour effectuer la configuration et gérer les versions d’un groupe de machines virtuelles Azure identiques à partir d’une image personnalisée générée par Packer (comprenant un réseau virtuel et des disques attachés gérés)."
keywords: "terraform, devops, groupe de machines virtuelles identiques, machine virtuelle, réseau, stockage, modules images personnalisées, packer"
author: VaijanathB
ms.author: tarcher
ms.date: 10/29/2017
ms.topic: article
ms.openlocfilehash: 284eae93de36986e41ba80f98f86495d8f34f57b
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Utiliser Terraform pour créer un groupe de machines virtuelles Azure identiques à partir d’une image Packer personnalisée

Dans ce didacticiel, vous allez utiliser [Terraform](https://www.terraform.io/) pour créer et déployer un [groupe de machines virtuelles Azure identiques](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview), créé avec une image personnalisée générée à l’aide de [Packer](https://www.packer.io/intro/index.html) et comprenant des disques gérés qui utilisent le [langage de configuration Hashicorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer votre déploiement Terraform
> * Utiliser des variables et des sorties pour le déploiement Terraform 
> * Créer et déployer une infrastructure réseau
> * Créer une image de machine virtuelle personnalisée à l’aide de Packer
> * Créer et déployer un groupe de machines virtuelles identiques à l’aide de l’image personnalisée
> * Créer et déployer un jumpbox 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer
> * [Installez Terraform et configurez l’accès à Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [Créez une paire de clés SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) si vous n’en avez pas encore
> * [Installez Packer](https://www.packer.io/docs/install/index.html) s’il n’est pas encore installé sur votre ordinateur local


## <a name="create-the-file-structure"></a>Créer la structure de fichiers

Créez trois nouveaux fichiers dans un répertoire vide avec les noms suivants :

- ```variables.tf``` Ce fichier contient les valeurs des variables utilisées dans le modèle.
- ```output.tf``` Ce fichier décrit les paramètres qui s’affichent après le déploiement.
- ```vmss.tf``` Ce fichier contient le code de l’infrastructure que vous déployez.

##  <a name="create-the-variables"></a>Créer les variables 

Cette étape vous permet de définir les variables qui personnalisent les ressources créées par Terraform.

Modifiez le fichier `variables.tf`, copiez le code suivant, puis enregistrez les modifications.

```tf 
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> Comme la valeur par défaut de la variable resource_group_name n’est pas définie, vous devez lui affecter votre propre valeur.

Enregistrez le fichier .

Lorsque vous déployez votre modèle Terraform, vous avez besoin de récupérer le nom de domaine complet utilisé pour accéder à l’application. Utilisez le type de ressource ```output``` de Terraform et dérivez la propriété ```fqdn``` de la ressource. 

Modifiez le fichier `output.tf` et copiez le code suivant pour exposer le nom de domaine complet pour les machines virtuelles. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Définir l’infrastructure réseau dans un modèle 

Cette étape vous permet de créer l’infrastructure réseau suivante dans un nouveau groupe de ressources Azure : 
  - Un réseau virtuel avec l’espace d’adressage 10.0.0.0/16 
  - Un sous-réseau avec l’espace d’adressage 10.0.2.0/24
  - Deux adresses IP publiques. Une est utilisée par l’équilibrage de charge du groupe de machines virtuelles identiques et l’autre est utilisée pour la connexion à la jumpbox SSH.

Vous avez également besoin d’un groupe de ressources où sont créées toutes les ressources. 

Modifiez et copiez le code suivant dans le fichier ```vmss.tf``` : 

```tf 

resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Nous vous recommandons de baliser les ressources déployées dans Azure afin de faciliter leur identification à l’avenir.

## <a name="create-the-network-infrastructure"></a>Créer l’infrastructure réseau

Initialisez l’environnement Terraform en exécutant la commande suivante dans le répertoire où vous avez créé les fichiers `.tf` :

```bash
terraform init 
```
 
Les plug-ins du fournisseur sont téléchargés à partir du registre Terraform dans le dossier ```.terraform``` du répertoire où vous avez exécuté la commande.

Exécutez la commande suivante pour déployer l’infrastructure dans Azure.

```bash
terraform apply
```

Vérifiez que le nom de domaine complet de l’adresse IP publique correspond à votre configuration.

![Nom de domaine complet Terraform du groupe de machines virtuelles identiques pour l’adresse IP publique](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Le groupe de ressources contient les ressources suivantes :

![Ressources réseau Terraform du groupe de machines virtuelles identiques](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Créer une image Azure à l’aide de Packer
Pour créer une image Linux personnalisée, suivez la procédure décrite dans le didacticiel [Comment utiliser Packer pour créer des images de machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer).
 
Suivez le didacticiel pour créer une image Ubuntu déprovisionnée avec NGINX installé.

![Une fois l’image Packer créée, vous disposez d’une image](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Pour les besoins de ce didacticiel, une commande est exécutée dans l’image Packer pour installer nginx. Vous pouvez également exécuter votre propre script lors de la procédure de création.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Modifier l’infrastructure pour ajouter le groupe de machines virtuelles identiques

Cette étape vous permet de créer les ressources suivantes sur le réseau qui a déjà été déployé :
- Un équilibrage de charge Azure pour prendre en charge l’application et l’attacher à l’adresse IP publique déployée à l’étape 4
- Un équilibrage de charge Azure et des règles pour prendre en charge l’application et l’attacher à l’adresse IP publique configurée précédemment
- Un pool d’adresses principales Azure qui est ensuite affecté à l’équilibrage de charge 
- Un port de sonde d’intégrité utilisé par l’application et configuré sur l’équilibrage de charge 
- Un groupe de machines virtuelles identiques derrière l’équilibrage de charge, exécuté sur le réseau virtuel déployé précédemment
- [Nginx](http://nginx.org/) sur les nœuds du groupe de machines virtuelles identiques installé à partir de l’image personnalisée.


Ajoutez le code suivant à la fin du fichier `vmss.tf`.

```tf


resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id                = "${azurerm_lb.vmss.id}"
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = "${var.application_port}"
  backend_port                   = "${var.application_port}"
  backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id="${data.azurerm_image.image.id}"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Personnalisez le déploiement en ajoutant le code suivant à `variables.tf` :

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Déployer le groupe de machines virtuelles identiques dans Azure

Exécutez la commande suivante pour visualiser le déploiement du groupe de machines virtuelles identiques :

```bash
terraform plan
```

La sortie de la commande doit se présenter comme suit :

![Ajout par Terraform d’un plan de groupes de machines virtuelles identiques](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Déployez les ressources supplémentaires dans Azure : 

```bash
terraform apply 
```

Le contenu du groupe de ressources ressemble à l’image suivante :

![Groupe de ressources du groupe de machines virtuelles identiques Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Ouvrez un navigateur et connectez-vous au nom de domaine complet qui a été retourné par la commande. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Ajouter une jumpbox au réseau existant 

Cette étape facultative permet d’utiliser une jumbox pour autoriser un accès SSH aux instances du groupe de machines virtuelles identiques.

Ajoutez les ressources suivantes à votre déploiement existant :
- Une interface réseau connectée au même sous-réseau que le groupe de machines virtuelles identiques
- Une machine virtuelle connectée avec cette interface réseau

Ajoutez le code suivant à la fin du fichier `vmss.tf` :

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Modifiez `outputs.tf` et ajoutez le code suivant pour afficher le nom d’hôte de la jumpbox lorsque le déploiement est terminé :

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Déployer la jumpbox

Déployez la jumpbox.

```bash
terraform apply 
```

Une fois le déploiement terminé, le contenu du groupe de ressources ressemble à ceci :

![Groupe de ressources du groupe de machines virtuelles identiques Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> La connexion avec un mot de passe est désactivée sur la jumpbox et le groupe de machines virtuelles identiques que vous avez déployés. Connectez-vous avec SSH pour accéder aux machines virtuelles.

## <a name="clean-up-the-environment"></a>Nettoyer l’environnement

La commande suivante supprime les ressources créées dans ce didacticiel :

```bash
terraform destroy
```

Tapez `yes` lorsque vous êtes invité à confirmer la suppression des ressources. Le processus de destruction peut prendre plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un groupe de machines virtuelles identiques et une jumpbox dans Azure à l’aide de Terraform. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Initialiser le déploiement Terraform
> * Utiliser des variables et des sorties pour le déploiement Terraform 
> * Créer et déployer une infrastructure réseau
> * Créer une image de machine virtuelle personnalisée à l’aide de Packer
> * Créer et déployer un groupe de machines virtuelles identiques à l’aide de l’image personnalisée
> * Créer et déployer un jumpbox 