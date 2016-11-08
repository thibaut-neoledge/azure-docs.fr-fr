---
title: Création d’une copie de votre machine virtuelle Linux Azure | Microsoft Docs
description: Créer une copie d’une machine virtuelle Linux Azure dans le modèle de déploiement Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn

---
# Créer une copie d’une machine virtuelle Linux exécutée sur Azure
Cet article vous explique comment créer une copie de votre machine virtuelle Azure exécutant Linux dans le modèle de déploiement Resource Manager. Copiez tout d’abord les disques du système d’exploitation et les disques de données dans un nouveau conteneur, configurez les ressources réseau puis créez la nouvelle machine virtuelle.

Vous pouvez également [charger et créer une machine virtuelle à partir d’une image de disque personnalisée](virtual-machines-linux-upload-vhd.md).

## Avant de commencer
Assurez-vous de disposer des composants requis suivants avant de commencer la procédure :

* Vous avez téléchargé et installé l[’interface de ligne de commande Azure](../xplat-cli-install.md) sur votre ordinateur.
* Vous devez également recueillir quelques informations concernant votre machine virtuelle Linux Azure existante :

| Informations sur la machine virtuelle source | Comment les obtenir |
| --- | --- |
| Nom de la machine virtuelle |`azure vm list` |
| Nom du groupe ressources |`azure vm list` |
| Emplacement |`azure vm list` |
| Nom du compte de stockage |`azure storage account list -g <resourceGroup>` |
| Nom du conteneur |`azure storage container list -a <sourcestorageaccountname>` |
| Nom du fichier VHD de la machine virtuelle source |`azure storage blob list --container <containerName>` |

* Vous devrez faire certains choix concernant votre nouvelle machine virtuelle : <br> -nom du contenur <br> -nom de la machine virtuelle <br> -taille de la machine virtuelle <br> -nom du réseau virtuel <br> -nom du sous-réseau <br> -nom IP <br> -nom de la carte réseau

## Se connecter et définir votre abonnement
1. Connectez-vous à l’interface de ligne de commande.
   
        azure login
2. Assurez-vous que vous êtes en mode Gestionnaire de ressources.
   
        azure config mode arm
3. Définissez l'abonnement approprié. Vous pouvez utiliser ’azure account list’ pour voir tous vos abonnements.
   
        azure account set <SubscriptionId>

## Arrêtez la machine virtuelle.
Arrêtez et libérez la machine virtuelle source. Vous pouvez utiliser ’azure vm list’ pour obtenir une liste de toutes les machines virtuelles de votre abonnement ainsi que les noms de leurs groupes de ressources.

        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## Copier le disque dur virtuel
Vous pouvez copier le disque dur virtuel du stockage source vers le stockage de destination à l’aide de la commande `azure storage blob copy start`. Dans cet exemple, nous allons copier le disque dur virtuel vers le même compte de stockage, mais dans un autre conteneur.

Pour copier le disque dur virtuel vers un autre conteneur du même compte de stockage, tapez :

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>


## Configurer le réseau virtuel pour votre nouvelle machine virtuelle
Configurez un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle.

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## Créer la machine virtuelle
Vous pouvez maintenant créer une machine virtuelle à partir du disque virtuel chargé [en utilisant un modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou via la CLI en spécifiant l’URI pointant vers votre disque copié, comme suit :

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## Étapes suivantes
Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure pour gérer votre nouvelle machine virtuelle, voir [Commandes de l’interface de ligne de commande Azure en mode Azure Resource Manager](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0803_2016-->