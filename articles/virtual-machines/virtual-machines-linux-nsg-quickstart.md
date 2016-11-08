---
title: Ouvrir des ports sur une machine virtuelle Linux | Microsoft Docs
description: Découvrez comment ouvrir un port / créer un point de terminaison sur votre machine virtuelle Linux à l’aide du modèle de déploiement Azure Resource Manager et de l’interface de ligne de commande Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/08/2016
ms.author: iainfou

---
# Ouverture de ports sur une machine virtuelle Linux dans Azure
Pour ouvrir un port ou créer un point de terminaison sur une machine virtuelle dans Azure, créez un filtre réseau sur un sous-réseau ou une interface réseau de machine virtuelle. Vous placez ces filtres, qui contrôlent le trafic entrant et sortant, dans un groupe de sécurité réseau associé à la ressource qui reçoit le trafic. Nous allons utiliser un exemple courant de trafic web sur le port 80.

## Commandes rapides
Pour créer un groupe de sécurité réseau et des règles, vous avez besoin de [l’interface de ligne de commande Azure](../xplat-cli-install.md) en mode Resource Manager (`azure config mode arm`).

Créez votre groupe de sécurité réseau en entrant votre nom et votre emplacement en conséquence :

```
azure network nsg create --resource-group TestRG --name TestNSG --location westus
```

Ajoutez une règle pour autoriser le trafic HTTP sur votre serveur Web (ou ajustez une règle en fonction de votre propre scénario, notamment l’accès SSH ou la connectivité de base de données) :

```
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow --resource-group TestRG --nsg-name TestNSG --name AllowHTTP
```

Associez un groupe de sécurité réseau associé à votre réseau de machines virtuelles.

```
azure network nic set --resource-group TestRG --name TestNIC --network-security-group-name TestNSG
```

Vous pouvez également associer votre groupe de sécurité réseau à un sous-réseau de réseau virtuel et non uniquement à l’interface réseau d’une seule machine virtuelle :

```
azure network vnet subnet set --resource-group TestRG --name TestSubnet --network-security-group-name TestNSG
```

## En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Vous pouvez définir des groupes de sécurité réseau et des règles de liste de contrôle d’accès dans le cadre de modèles Azure Resource Manager. En savoir plus sur la [création de groupes de sécurité réseau avec des modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser le réacheminement de port pour mapper un seul port externe sur un port interne de votre machine virtuelle, utilisez un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez peut-être exposer le port TCP 8080 en externe et diriger le trafic vers le port TCP 80 sur une machine virtuelle. En savoir plus sur [la création d'un équilibreur de charge accessible sur Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md)

## Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

* [Présentation d’Azure Resource Manager](../resource-group-overview.md)
* [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md)
* [Présentation d’Azure Resource Manager Overview pour les équilibreurs de charge](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0907_2016-->