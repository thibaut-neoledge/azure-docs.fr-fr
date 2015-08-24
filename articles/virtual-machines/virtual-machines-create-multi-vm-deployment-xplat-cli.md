<properties
   pageTitle="Créer un déploiement de machines virtuelles à l'aide de l'interface de ligne de commande | Microsoft Azure"
   description="Apprenez à créer un déploiement de plusieurs machines virtuelles à l'aide de l'interface de ligne de commande Microsoft Azure"
   services="virtual-machines"
   documentationCenter="nodejs"
   authors="AlanSt"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="alanst;kasing"/>

# Créer un déploiement de plusieurs machines virtuelles à l'aide de l'interface de ligne de commande Microsoft Azure

Le script suivant vous montre comment configurer un déploiement de plusieurs services cloud avec plusieurs machines virtuelles dans un réseau virtuel à l’aide de l’interface de ligne de commande Microsoft Azure.

L'illustration ci-dessous présente l'aspect de votre déploiement une fois le script terminé :

![](./media/virtual-machines-create-multi-vm-deployment-xplat-cli/multi-vm-xplat-cli.png)

Le script crée une machine virtuelle (**servervm**) dans le service cloud **servercs** avec deux disques de données attachés et deux machines virtuelles (**clientvm1, clientvm2**) dans le service cloud **workercs**. Les deux services cloud sont placés dans le réseau virtuel **samplevnet**. Le service cloud **servercs** a également un point de terminaison configuré pour la connectivité externe.

## Script d'interface de ligne de commande nécessaire
Le code pour effectuer ce paramétrage est relativement simple :

>[AZURE.NOTE]Vous devrez probablement modifier les noms de service cloud servercs et workercs en noms de service cloud unique

    azure network vnet create samplevnet -l "West US"
    azure vm create -l "West US" -w samplevnet -e 10000 -z Small -n servervm servercs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-fr-fr-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10001 -z Small –n clientvm1 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-fr-fr-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10002 -c -z Small -n clientvm2 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-fr-fr-30GB azureuser Password@1
    azure vm disk attach-new servervm 100
    azure vm disk attach-new servervm 500
    azure vm endpoint create servervm 443 443 -n https -o tcp

Voici le code qui permet de le supprimer :

    azure vm delete -b -q servervm
    azure vm delete -b -q clientvm1
    azure vm delete -b -q clientvm2
    azure network vnet delete -q samplevnet

*L'option –q supprime la confirmation interactive de la suppression des objets, tandis que l'option -b nettoie les disques/objets BLOB associés à la machine virtuelle.*

## Formes génériques des commandes utilisées

Bien que vous puissiez trouver plus d'informations à l'aide de l'option –help sur les commandes d'interface de ligne de commande Azure, la forme générique de chaque commande utilisée ci-dessus est la suivante :

    azure network vnet create -l <Region> <VNet_name>
    azure network vnet delete -q <VNet_name>

    azure vm create -l <Region> -w <Vnet_name> -e <SSH_port> -z <VM_size> -n <VM_name> <Cloud_service_name> <VM_image> <Username> <Password>
    azure vm delete -b -q <VM_name>
    azure vm disk attach-new <VM_name>
    azure vm endpoint create <VM_name> <External_port> <Internal_port> -n <Endpoint_name> -o <TCP/UDP>

## Étapes suivantes


* [Linux et informatique open-source sur Microsoft Azure](virtual-machines-linux-opensource.md)
* [Connexion à une machine virtuelle exécutant Linux](virtual-machines-linux-how-to-log-on.md)
 

<!---HONumber=August15_HO7-->