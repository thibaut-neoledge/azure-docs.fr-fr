---
title: "Réseaux virtuels Azure et machines virtuelles Linux | Microsoft Docs"
description: "Didacticiel : Gérer des réseaux virtuels Azure et des machines virtuelles Linux avec Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 1ac628b606a198bb437c02d00467d48462c34334
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Gérer des réseaux virtuels Azure et des machines virtuelles Linux avec Azure CLI

Dans ce didacticiel, vous apprendrez à créer plusieurs machines virtuelles dans un réseau virtuel et à configurer leur connectivité réseau. Une fois cette opération terminée, une machine virtuelle frontale est accessible sur Internet à partir du port 22 pour les connexions SSH et à partir du port 80 pour les connexions HTTP. Une machine virtuelle principale avec une base de données MySQL est isolée et accessible uniquement à partir de la machine virtuelle frontale sur le port 3306.

Les étapes de ce didacticiel peuvent être effectuées à l’aide de la dernière version [d’Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-vm-and-vnet"></a>Créer une machine virtuelle et un réseau virtuel

Un réseau virtuel (VNet) Azure est une représentation de votre propre réseau dans le cloud. Il s’agit d’un isolement logique du cloud Azure dédié à votre abonnement. Un réseau virtuel comporte des sous-réseaux, des règles de connectivité vers ces sous-réseaux et des connexions entre les machines virtuelles et les sous-réseaux. Azure CLI facilite la création de toutes les ressources réseau dont vous avez besoin pour prendre en charge l’accès à vos machines virtuelles. 

Avant de pouvoir créer d’autres ressources Azure, vous devez créer un groupe de ressources avec az group create. L’exemple suivant crée un groupe de ressources nommé *myRGNetwork* à l’emplacement *westus* :

```azurecli
az group create --name myRGNetwork --location westus
```

Lorsque vous créez une machine virtuelle à l’aide d’Azure CLI, les ressources réseau nécessaires sont automatiquement créées en même temps. Créez *myFrontendVM* et ses ressources réseau de support avec [az vm create](https://docs.microsoft.com/cli/azure/vm#create) :

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Une fois la machine virtuelle créée, notez l’adresse IP publique. Cette adresse sera utilisée dans les étapes suivantes du didacticiel :

```bash
{
  "fqdns": "",
  "id": "/subscriptions/{id}/resourceGroups/myRGNetwork/providers/Microsoft.Compute/virtualMachines/myFrontendVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myRGNetwork"
}
```

Les ressources réseau suivantes ont été créées :

- **myFrontendVMNSG** : groupe de sécurité réseau qui sécurise le trafic entrant vers *myFrontendVM*.
- **myVMPublicIP** : adresse IP publique qui offre un accès Internet à *myFrontendVM*.
- **myVMVMNic** : interface réseau virtuelle qui fournit une connectivité réseau à *myFrontendVM*.
- **myVMVNET** : réseau virtuel auquel *myFrontendVM* est connecté.

## <a name="install-web-server"></a>Installer le serveur web

Créez une connexion SSH avec *myFrontendVM*. Remplacez l’exemple d’adresse IP par l’adresse IP publique de la machine virtuelle :

```bash
ssh 40.68.254.142
```

Exécutez les commandes suivantes pour installer NGINX :

```bash
sudo apt-get -y update && sudo apt-get -y install nginx
```

Fermez la session SSH :

```bash
exit
```

## <a name="manage-internet-traffic"></a>Gérer le trafic internet

Un groupe de sécurité réseau contient une liste de règles de sécurité qui autorisent ou rejettent le trafic réseau vers les ressources connectées à un réseau virtuel. Les groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des cartes réseau distinctes qui sont attachées aux machines virtuelles. L’ouverture et la fermeture de l’accès aux machines virtuelles par le biais des ports s’effectuent à l’aide des règles de groupe de sécurité réseau. Quand vous avez créé *myFrontendVM*, le port d’entrée 22 a été automatiquement ouvert pour la connectivité SSH.

Ouvrez le port 80 sur *myFrontendVM* avec [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) :

```azurecli
az vm open-port --resource-group myRGNetwork --name myFrontendVM --port 80
```

Vous pouvez maintenant accéder à l’adresse IP publique de la machine virtuelle pour afficher le site NGINX.

![Site par défaut NGINX](./media/quick-create-cli/nginx.png)

## <a name="manage-internal-traffic"></a>Gérer le trafic interne

La communication interne des machines virtuelles peut également être configurée à l’aide d’un groupe de sécurité réseau. Dans cette section, vous allez apprendre à créer un sous-réseau supplémentaire dans le réseau et à affecter un groupe de sécurité réseau au sous-réseau pour autoriser une connexion de *myFrontendVM* vers *myBackendVM* sur le port 3306. Le sous-réseau est ensuite affecté à la machine virtuelle lors de sa création.

Ajoutez un groupe de sécurité réseau nommé *myBackendNSG* avec la commande [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create). 

```azurecli
az network nsg create \
 --resource-group myRGNetwork \
 --name myBackendNSG
```

Configurez un port pour permettre à *myFrontendVM* et *myBackendVM* de communiquer dans le réseau virtuel. Ajoutez une règle de groupe de sécurité réseau qui autorise le trafic vers *myBackendSubnet* uniquement à partir de *myVMSubnet* avec [az network rule create](/cli/azure/network/rule#create) :

```azurecli
az network nsg rule create \
 --resource-group myRGNetwork \
 --nsg-name myBackendNSG \
 --name com-rule \
 --access Allow \
 --protocol Tcp \
 --direction Inbound \
 --priority 100 \
 --source-address-prefix 10.0.0.0/24 \
 --source-port-range "*" \
 --destination-address-prefix "*" \
 --destination-port-range 3306
```

## <a name="add-back-end-subnet"></a>Ajouter un sous-réseau principal

Un sous-réseau est une ressource enfant d’un réseau virtuel, et permet de définir des segments d’espaces d’adressage dans un bloc CIDR, à l’aide de préfixes d’adresses IP. Les cartes d’interface réseau (NIC) peuvent être ajoutées aux sous-réseaux et connectées aux machines virtuelles, ce qui fournit une connectivité pour différentes charges de travail.

Ajoutez *myBackEndSubnet* à *myFrontendVMVNet* avec [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) :

```azurecli
az network vnet subnet create \
 --address-prefix 10.0.1.0/24 \
 --name myBackendSubnet \
 --resource-group myRGNetwork \
 --vnet-name myFrontendVMVNET \
 --network-security-group myBackendNSG
```

## <a name="create-back-end-vm"></a>Créer une machine virtuelle principale

Créez *myBackendVM* à l’aide de *myBackendSubnet* avec [az vm create](/cli/azure/vm#create) :

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --subnet myBackendSubnet \
  --vnet-name myFrontendVMVNET \
  --public-ip-address ""

```

## <a name="install-database"></a>Installer la base de données

Pour ce tutoriel, vous devez copier la clé privée à partir de votre machine virtuelle de développement vers *myFrontendVM*. Dans un environnement de production, il est recommandé de créer des clés spécifiques à utiliser sur les machines virtuelles plutôt que d’utiliser --generate-ssh-keys lorsque vous créez les machines virtuelles. 

La machine virtuelle principale n’est pas censée être accessible publiquement. Dans cette section, vous allez apprendre à utiliser SSH pour vous connecter à *myFrontendVM*, puis à utiliser SSH pour vous connecter à *myBackendVM* à partir de *myFrontendVM*.

Remplacez l’exemple d’adresse IP par l’adresse IP publique de *myFrontendVM* :

```bash
scp ~/.ssh/id_rsa 40.68.254.142:~/.ssh/id_rsa
```

Créez une connexion SSH avec *myFrontendVM*. Remplacez l’exemple d’adresse IP par l’adresse IP publique de *myFrontendVM* :

```bash
ssh 40.68.254.142
```

À partir de *myFrontendVM*, connectez-vous à *myBackendVM* :

```bash
ssh myBackendVM
```

Exécutez la commande suivante pour installer MySQL :

```bash
sudo apt-get -y install mysql-server
```

Suivez les instructions de configuration de MySQL.

Fermez les sessions SSH :

```bash
exit
```

L’installation de MySQL montre comment une application peut être installée sur *myBackendVM*, mais MySQL n’est pas utilisé dans ce tutoriel.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer et à sécuriser des réseaux Azure en rapport avec des machines virtuelles. Passez au tutoriel suivant pour apprendre à surveiller la sécurité des machines virtuelles avec Azure Security Center.

[Gérer la sécurité des machines virtuelles](./tutorial-azure-security.md)
