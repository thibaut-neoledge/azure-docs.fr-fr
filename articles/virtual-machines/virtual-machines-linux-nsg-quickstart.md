<properties
   pageTitle="Autoriser l’accès externe à une machine virtuelle Linux | Microsoft Azure"
   description="Découvrez comment ouvrir un port / créer un point de terminaison qui permet un accès externe à votre machine virtuelle Linux à l’aide du modèle de déploiement Resource Manager et de l’interface de ligne de commande Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/24/2016"
   ms.author="iainfou"/>

# Autoriser l’accès externe à votre machine virtuelle
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Commandes rapides
Pour créer un groupe de sécurité de réseau et des règles, vous avez besoin de l’[interface de ligne de commande Azure](../xplat-cli-install.md) en mode Resource manager (`azure config mode arm`).

Procédez comme suit pour créer votre groupe de sécurité réseau en entrant votre nom et votre emplacement en conséquence :

```
azure network nsg create --resource-group TestRG --name TestNSG --location westus
```

Ajoutez une règle pour autoriser le trafic HTTP sur votre serveur Web (elle peut être ajustée en fonction de votre propre scénario, notamment l’accès SSH ou la connectivité de base de données) :

```
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow --resource-group TestRG --nsg-name TestNSG --name AllowHTTP
```

Associez un groupe de sécurité réseau associé à votre réseau de machines virtuelles.

```
azure network nic set --resource-group TestRG --name TestNIC --network-security-group-name TestNSG
```

Vous pouvez également associer votre groupe de sécurité réseau à un sous-réseau de réseau virtuel et non pas uniquement à l’interface réseau d’une seule machine virtuelle :

```
azure network vnet subnet set --resource-group TestRG --name TestSubnet --network-security-group-name TestNSG
```

## En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Les groupes de sécurité réseau et les règles de liste de contrôle d’accès peuvent également être définis dans le cadre de modèles Azure Resouce Manager. En savoir plus sur la [création de groupes de sécurité réseau avec des modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser le réacheminement de port pour mapper un seul port externe sur un port interne de votre machine virtuelle, vous devez utiliser un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez peut-être exposer le port TCP 8080 en externe et diriger le trafic vers le port TCP 80 sur une machine virtuelle. En savoir plus sur [la création d'un équilibreur de charge accessible sur Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md)

## Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

- [Présentation d’Azure Resource Manager](../resource-group-overview.md)
- [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md)
- [Présentation d’Azure Resource Manager Overview pour les équilibreurs de charge](../load-balancer2 /load-balancer-arm.md)

<!---HONumber=AcomDC_0601_2016-->