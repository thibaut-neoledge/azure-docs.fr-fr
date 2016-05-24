<properties
   pageTitle="Test de SAP NetWeaver sur les machines virtuelles Microsoft Azure SUSE Linux | Microsoft Azure"
   description="Test de SAP NetWeaver sur les machines virtuelles Microsoft Azure SUSE Linux"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="hermannd"/>

# Tester SAP NetWeaver sur les machines virtuelles Microsoft Azure SUSE Linux


Cet article décrit les divers éléments à prendre en compte lorsque vous testez SAP NetWeaver sur les machines virtuelles Microsoft Azure SUSE Linux. À l’heure actuelle, il n’existe aucune déclaration officielle de prise en charge par SAP de ces machines virtuelles. Toutefois, vous pouvez effectuer quelques tests, démonstrations ou prototypages, tant que vous ne dépendez pas de la prise en charge officielle par SAP.

Les informations suivantes devraient vous aider à éviter certains pièges potentiels.

## Images SUSE sur Azure pour le test de SAP

Pour tester SAP sur Azure, utilisez uniquement SUSE Linux Enterprise Server (SLES) 11 SP4 et SLES 12. Une image SUSE spéciale se trouve dans Azure Marketplace (« SLES 11 SP3 for SAP CAL »), mais elle n’est pas destinée à une utilisation générale. N’utilisez pas cette image, car elle est réservée à la solution [SAP Cloud Appliance Library](https://cal.sap.com/).

Tous les nouveaux tests sur Azure doivent être effectués avec Azure Resource Manager. Pour rechercher des images et des versions SUSE SLES à l’aide d’Azure PowerShell ou de l’interface de ligne de commande (CLI) Azure, utilisez les commandes suivantes. Vous pouvez ensuite utiliser la sortie, par exemple pour définir l’image du système d’exploitation dans un modèle JSON pour le déploiement d’une nouvelle machine virtuelle SUSE Linux. Ces commandes PowerShell sont valides pour Azure PowerShell version 1.0.1 et ses versions ultérieures.

* Rechercher les éditeurs existants, notamment SUSE :

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Rechercher les offres existantes de SUSE :

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Rechercher les offres SUSE SLES :

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Rechercher une version spécifique d’un SKU SLES :

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## Installation de WALinuxAgent dans une machine virtuelle SUSE

L’agent appelé WALinuxAgent fait partie des images SLES dans Azure Marketplace. Pour plus d’informations sur son installation manuelle (par exemple, lors du chargement d’un disque dur virtuel (VHD) du système d’exploitation SLES à partir d’un emplacement local), voir les articles suivants :

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Microsoft Azure](virtual-machines-linux-endorsed-distros.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Attachement de disques de données Azure à une machine virtuelle Azure Linux

Ne montez jamais de disques de données Azure dans une machine virtuelle Azure Linux en utilisant l’ID de périphérique. À la place, utilisez l’identificateur unique universel (UUID). Soyez prudent, par exemple lorsque vous utilisez des outils graphiques pour monter des disques de données Azure. Vérifiez bien les entrées dans /etc/fstab.

Le problème avec l’ID de périphérique, c’est qu’il peut changer et la machine virtuelle Azure peut se bloquer pendant le processus de démarrage. Pour atténuer ce problème, vous pouvez ajouter le paramètre nofail dans /etc/fstab. Toutefois, utilisez nofail avec précaution, car les applications risquent d’utiliser le point de montage précédent et d’écrire dans le système de fichiers racine au cas où un disque de données Azure externe n’a pas été monté lors du démarrage.

La seule exception au montage par le biais de l’UUID concerne l’attachement d’un disque du système d’exploitation à des fins de résolution des problèmes, comme décrit dans la section suivante.

## Résolution des problèmes d’une machine virtuelle SUSE devenue inaccessible

Il peut arriver qu’une machine virtuelle SUSE dans Azure se bloque pendant le processus de démarrage (par exemple, en raison d’une erreur liée au montage de disques). Vous pouvez vérifier ce problème à l’aide de la fonctionnalité des diagnostics de démarrage pour Azure Virtual Machines v2 dans le portail Azure. Pour plus d’informations, voir [Boot diagnostics](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) (Diagnostics de démarrage).

L’une des solutions pour résoudre le problème consiste à attacher le disque du système d’exploitation de la machine virtuelle endommagée à une autre machine virtuelle SUSE dans Azure. Il convient ensuite d’apporter les changements appropriés, par exemple en modifiant /etc/fstab ou en supprimant les règles udev réseau, comme décrit dans la section suivante.

Un élément important doit cependant être pris en compte. Le déploiement de plusieurs machines virtuelles SUSE à partir de la même image Azure Marketplace (par exemple, SLES 11 SP4) entraîne le montage systématique du disque du système d’exploitation par le même UUID. Par conséquent, l’utilisation de l’UUID pour attacher un disque du système d’exploitation provenant d’une autre machine virtuelle déployée à l’aide de la même image Azure Marketplace produit deux UUID identiques. Cela pose des problèmes et peut indiquer que la machine virtuelle destinée à la résolution des problèmes démarre en réalité à partir du disque du système d’exploitation attaché et endommagé, et non à partir du disque d’origine.

Vous disposez de deux méthodes pour éviter cette situation :

* Utilisez une image Azure Marketplace différente pour la machine virtuelle de résolution des problèmes (par exemple, SLES 12 au lieu de SLES 11 SP4).
* N’attachez pas le disque du système d’exploitation endommagé d’une autre machine virtuelle en utilisant l’UUID, mais procédez différemment.

## Chargement d’une machine virtuelle SUSE à partir d’un emplacement local vers Azure

Pour obtenir une description de la procédure de chargement d’une machine virtuelle SUSE à partir d’un emplacement local vers Azure, voir [Préparation d’une machine virtuelle SLES ou openSUSE pour Azure](virtual-machines-linux-create-upload-vhd-suse.md).

Si vous souhaitez charger une machine virtuelle en omettant l’étape finale d’annulation de l’approvisionnement (par exemple, pour conserver une installation SAP existante en plus du nom d’hôte), suivez les consignes suivantes :

* Assurez-vous que le disque du système d’exploitation est monté par l’intermédiaire de l’UUID, et non de l’ID de périphérique. Utiliser l’UUID dans /etc/fstab ne suffit pas pour le disque du système d’exploitation. N’oubliez pas non plus d’adapter le chargeur de démarrage par le biais de YaST ou en modifiant /boot/grub/menu.lst.
* Si vous utilisez le format VHDX pour le disque du système d’exploitation SUSE et que vous le convertissez en disque dur virtuel pour le chargement dans Azure, il est très probable que le périphérique réseau bascule d’eth0 vers eth1. Pour éviter tout problème lors du démarrage ultérieur dans Azure, vous devez rétablir eth0 comme décrit dans l’article [Fixing eth0 in cloned SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/) (Correction d’eth0 dans SLES 11 cloné dans VMware).

En plus de ce qui est décrit dans l’article, nous vous recommandons de supprimer la ligne suivante :

   /lib/udev/rules.d/75-persistent-net-generator.rules

Vous pouvez également installer l’agent Linux Azure (waagent) pour éviter tout problème potentiel, à condition qu’il n’y ait pas plus d’une carte d’interface réseau.

## Déploiement d’une machine virtuelle SUSE sur Azure

Pour créer des machines virtuelles SUSE, vous devez utiliser les fichiers de modèle JSON dans le nouveau modèle Azure Resource Manager. Après la création du fichier de modèle JSON, vous pouvez déployer la machine virtuelle à l’aide de la commande CLI suivante comme alternative à PowerShell :

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Pour plus d’informations sur les fichiers de modèle JSON, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md) et [Modèles de démarrage rapide Microsoft Azure](https://azure.microsoft.com/documentation/templates/).

Pour plus d’informations sur CLI et sur Azure Resource Manager, voir [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](xplat-cli-azure-resource-manager.md).

## Licence SAP et clé matérielle

Pour la certification SAP-Azure officielle, un nouveau mécanisme a été introduit afin de calculer la clé matérielle SAP utilisée pour la licence SAP. Le noyau SAP a dû être adapté pour utiliser ce mécanisme. Les versions actuelles du noyau SAP pour Linux n’incluent pas cette modification du code. Ainsi, dans certaines situations (par exemple, en cas de redimensionnement de machine virtuelle Azure), la clé matérielle SAP change, et la licence SAP risque de ne plus être valide.

## Package sapconf SUSE

SUSE fournit un package appelé « sapconf » qui gère un ensemble de paramètres propres à SAP. Pour plus d’informations sur le rôle, l’installation et l’utilisation de ce package, voir [Utilisation de sapconf pour préparer un serveur SUSE Linux Enterprise Server à l’exécution de systèmes SAP](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) et [Qu’est-ce que sapconf et comment préparer un serveur SUSE Linux Enterprise Server à l’exécution de systèmes SAP ?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

## Partage NFS dans les installations SAP distribuées

Si vous disposez d’une installation distribuée, par exemple lorsque vous souhaitez installer la base de données et les serveurs d’applications SAP sur des machines virtuelles distinctes, vous pouvez partager le répertoire/sapmnt par l’intermédiaire du système de gestion de fichiers en réseau (NFS). Si vous rencontrez des problèmes lors des étapes d’installation après la création du partage NFS pour /sapmnt, vérifiez si « no\_root\_squash » est défini pour le partage.

## Volumes logiques

Le Gestionnaire de volumes logiques (LVM) n'est pas pleinement validé sur Azure. Si vous avez besoin d’un important volume logique partagé entre plusieurs disques de données Azure (par exemple, pour la base de données SAP), vous devez utiliser mdadm. Pour plus d’informations sur la procédure de configuration de RAID Linux sur Azure à l’aide de mdadm, voir [Configurer un RAID logiciel sur Linux](virtual-machines-linux-configure-raid.md).


## Dépôt SUSE Azure

Si vous rencontrez un problème d’accès au référentiel Azure SUSE standard, vous pouvez réinitialiser ce dernier à l’aide d’une commande simple. Ce problème peut se produire si vous créez une image de système d’exploitation privée dans une région Azure, puis que vous la copiez dans une autre région où vous souhaitez déployer de nouvelles machines virtuelles basées sur cette image. Il vous suffit d’exécuter la commande suivante à l’intérieur de la machine virtuelle :

   ```
   service guestregister restart
   ```

## Bureau Gnome

Si vous souhaitez utiliser le bureau Gnome pour installer un système complet de démonstration SAP dans une même machine virtuelle, comprenant une interface utilisateur SAP, un navigateur et une console de gestion SAP, suivez le conseil ci-après pour l’installer sur les images Azure SLES :

   Pour SLES 11 :

   ```
   zypper in -t pattern gnome
   ```

   Pour SLES 12 :

   ```
   zypper in -t pattern gnome-basic
   ```

## Prise en charge par SAP d’Oracle sur Linux dans le cloud

Il existe une restriction de prise en charge d’Oracle sur Linux dans les environnements virtualisés. Bien que cette question ne soit pas propre à Azure, il est important de bien la comprendre. SAP ne prend pas en charge Oracle sur SUSE ni Red Hat dans un cloud public comme Azure. Pour discuter de ce problème, contactez directement Oracle.

<!---HONumber=AcomDC_0511_2016-->