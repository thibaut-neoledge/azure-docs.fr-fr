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
   ms.date="02/12/2016"
   ms.author="hermannd"/>

# Test de SAP NetWeaver sur les machines virtuelles Microsoft Azure SUSE Linux


Voici une liste d’éléments à prendre en compte lors du test de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux. Il n’existe à l’heure actuelle aucune déclaration officielle de prise en charge par SAP de SAP-Linux-Azure. Néanmoins, les clients peuvent effectuer quelques tests, démos ou prototypages tant qu’ils ne dépendent pas de la prise en charge officielle de SAP.

Les informations suivantes devraient vous aider à éviter certains pièges potentiels.



## Images SUSE sur Microsoft Azure pour le test de SAP

Pour tester SAP sur Azure, utilisez uniquement SUSE Linux Enterprise Server (SLES) 11 SP4 et SLES 12. Une image SUSE spéciale se trouve dans Azure Marketplace (« SLES 11 SP3 for SAP CAL »), mais elle n’est pas destinée à une utilisation générale. Elle est réservée à la solution [SAP Cloud Appliance Library](https://cal.sap.com/). Il n’y avait aucun moyen de masquer cette image aux yeux du public. Ne l’utilisez pas, tout simplement.

Tous les nouveaux tests sur Azure doivent être effectués avec Azure Resource Manager. Pour rechercher des images et des versions SUSE SLES à l’aide d’Azure PowerShell ou de l’interface de ligne de commande (CLI) Azure, utilisez les commandes suivantes. Vous pouvez ensuite utiliser la sortie, par exemple pour définir l’image du système d’exploitation dans un modèle JSON pour le déploiement d’une nouvelle machine virtuelle SUSE Linux. Les commandes PowerShell ci-dessous sont valides pour Azure PowerShell version 1.0.1 ou ultérieure.

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

L’agent appelé WALinuxAgent fait partie des images SLES dans Azure Marketplace. Voici quelques emplacements où vous trouverez des informations sur l’installation manuelle (par exemple lors du chargement d’un disque dur virtuel de système d’exploitation SLES à partir d’un emplacement local) :

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Microsoft Azure](virtual-machines-linux-endorsed-distributions.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Attachement de disques de données Azure à une machine virtuelle Azure Linux

Ne montez jamais des disques de données Azure dans une machine virtuelle Azure Linux au moyen de l’ID de périphérique. Utilisez plutôt l’UUID. Soyez prudent lorsque vous utilisez, par exemple, des outils graphiques pour monter des disques de données Azure. Vérifiez bien les entrées dans /etc/fstab.

Le problème avec l’ID de périphérique, c’est qu’il peut changer et la machine virtuelle Azure peut se bloquer pendant le processus de démarrage. Vous pouvez ajouter le paramètre nofail dans /etc/fstab pour atténuer le problème. Mais attention : avec nofail, les applications peuvent utiliser le point de montage comme avant, et peuvent écrire dans le système de fichiers racine au cas où un disque de données Azure externe n’a pas été monté lors du démarrage.

La seule exception concernant le montage via l’UUID est relative à l’attachement d’un disque du système d’exploitation à des fins de dépannage, comme décrit dans la section suivante.

## Résolution des problèmes de la machine virtuelle SUSE qui n’est plus accessible

Il peut arriver qu’une machine virtuelle SUSE dans Azure se bloque pendant le processus de démarrage (par exemple, en raison d’une erreur liée au montage de disques). Le problème peut être vérifié entre autres par la fonctionnalité des diagnostics au démarrage dans le portail pour les machines virtuelles v2 ([consultez ce blog](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)).

Une option permettant de résoudre le problème consiste à attacher le disque du système d’exploitation à partir de la machine virtuelle endommagée à une autre machine virtuelle SUSE dans Azure et à apporter les changements appropriés, par exemple en modifiant /etc/fstab ou en supprimant les règles udev réseau comme décrit dans la section suivante.

Un élément important est cependant à prendre en compte. Le déploiement de plusieurs machines virtuelles SUSE à partir de la même image de galerie Azure (par exemple, SLES 11 SP4) indique que le disque du système d’exploitation est toujours monté par le même UUID. L’attachement d’un disque du système d’exploitation par l’UUID à partir d’une machine virtuelle différente qui a été déployée avec la même image de galerie Azure produit donc deux UUID identiques. Cela pose des problèmes et peut indiquer que la machine virtuelle destinée au dépannage démarre en réalité à partir du disque du système d’exploitation attaché et endommagé et non à partir du disque d’origine.

Vous pouvez éviter ce problème de deux façons :

* en utilisant une image de galerie Azure différente pour la machine virtuelle de dépannage (par exemple, SLES 12 au lieu de SLES 11 SP4) ;
* en n’attachant pas le disque du système d’exploitation endommagé à partir d’une autre machine virtuelle via l’UUID, mais en procédant différemment.

## Chargement d’une machine virtuelle SUSE à partir d’un emplacement local vers Azure

[Cet article](virtual-machines-linux-create-upload-vhd-suse.md) décrit les étapes de chargement d’une machine virtuelle SUSE à partir d’un emplacement local vers Azure.

Si vous souhaitez charger une machine virtuelle sans effectuer l’étape d’annulation de l’approvisionnement à la fin, par exemple pour conserver une installation SAP existante en plus du nom d’hôte, notez les points suivants :

* Assurez-vous que le disque du système d’exploitation soit monté par l’intermédiaire de l’UUID, et non de l’ID de périphérique. Utiliser l’UUID dans /etc/fstab ne suffit pas pour le disque du système d’exploitation. N’oubliez pas non plus d’adapter le chargeur de démarrage, par exemple via YaST ou en modifiant /boot/grub/menu.lst
* Si vous avez utilisé le format VHDX pour le disque du système d’exploitation SUSE et que vous le convertissez en disque dur virtuel pour le chargement vers Azure, il est très probable que le périphérique réseau ait basculé d’eth0 à eth1. Pour éviter tout problème lors du démarrage ultérieur dans Azure, vous devez rétablir eth0 comme décrit dans [cet article](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

En plus de ce qui est décrit dans l’article, nous vous recommandons de supprimer la ligne suivante :

   /lib/udev/rules.d/75-persistent-net-generator.rules

L’installation de l’agent Linux Azure (waagent) devrait également vous éviter tout problème potentiel, tant qu’il n’y a pas plus d’une carte réseau.

## Déploiement d’une machine virtuelle SUSE sur Azure

Les nouvelles machines virtuelles SUSE doivent être créées via des fichiers de modèle JSON dans le nouveau modèle Azure Resource Manager. Une fois le fichier de modèle JSON créé, vous pouvez déployer la machine virtuelle à l’aide de la commande CLI suivante comme alternative à PowerShell :

   ``` azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ``` Vous trouverez plus d’informations sur les fichiers de modèle JSON dans [cet article](resource-group-authoring-templates.md) et [cette page web](https://azure.microsoft.com/documentation/templates/).

Pour plus d’informations concernant CLI et Azure Resource Manager, consultez [cet article](xplat-cli-azure-resource-manager.md).

## Licence SAP et clé matérielle

Pour la certification SAP-Windows-Azure officielle, un nouveau mécanisme a été introduit pour calculer la clé matérielle SAP utilisée pour la licence SAP. Le noyau SAP a dû être adapté pour utiliser ce mécanisme. Les versions actuelles du noyau SAP pour Linux n’incluent pas cette modification du code. Ainsi, dans certaines situations (par exemple en cas de redimensionnement de machine virtuelle Azure), il est possible que la clé matérielle SAP change et que la licence SAP ne soit plus valide.

## Package sapconf SUSE

SUSE fournit un package appelé « sapconf », qui prend en charge un ensemble de paramètres spécifiques à SAP. Pour plus d’informations concernant ce package, son utilité et comment l’installer et l’utiliser, consultez [ce billet de blog SUSE](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) et [ce billet de blog SAP](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

## Partage NFS dans les installations SAP distribuées

En cas d’installation distribuée où vous souhaitez par exemple installer la base de données et les serveurs d’applications SAP dans des machines virtuelles distinctes, vous pouvez partager le répertoire /sapmnt par l’intermédiaire de NFS. Si des problèmes surviennent lors des étapes d’installation après la création du partage NFS pour /sapmnt, vérifiez si « no\_root\_squash » est défini pour le partage. Ce cas de figure s’est présenté durant un cas de test interne.


## Volumes logiques

Le Gestionnaire de volumes logiques (LVM) n'est pas pleinement validé sur Azure. Si vous avez besoin d’un gros volume logique partagé sur plusieurs disques de données Azure (par exemple pour la base de données SAP), vous devez utiliser mdadm. [Cet article](virtual-machines-linux-configure-raid.md) explique comment configurer RAID Linux sur Azure à l’aide de mdadm.


## Dépôt SUSE Azure

En cas de problème d’accès au dépôt Azure SUSE standard, il existe une commande simple pour le réinitialiser. Ce problème peut se produire quand vous créez une image de système d’exploitation privée dans une région Azure et que vous la copiez ensuite dans une autre région où vous souhaitez déployer de nouvelles machines virtuelles basées sur cette image de système d’exploitation privée. Il vous suffit d’exécuter la commande suivante à l’intérieur de la machine virtuelle :

   ```
   service guestregister restart
   ```

## Bureau Gnome

Si vous souhaitez utiliser le bureau Gnome pour installer un système complet de démo SAP dans une même machine virtuelle (notamment l’interface utilisateur SAP, le navigateur et la console de gestion SAP), voici une petite astuce pour l’installer sur les images Azure SLES :

   SLES 11

   ```
   zypper in -t pattern gnome
   ```

   SLES 12

   ```
   zypper in -t pattern gnome-basic
   ```

## Prise en charge de SAP-Oracle sur Linux dans le cloud

Il existe une restriction de prise en charge d’Oracle sur Linux dans les environnements virtualisés. Il s'agit d'une rubrique générale, qui n’est pas spécifique à Azure, mais il est important de bien la comprendre. SAP ne prend pas en charge Oracle sur SUSE, ni RedHat dans un cloud public comme Azure. Les clients doivent contacter Oracle directement pour discuter de ce problème.

<!---HONumber=AcomDC_0218_2016-->