<properties
	pageTitle="Cloner une machine virtuelle Classic vers Azure Resource Manager à l’aide de scripts PowerShell"
	description="Cet article décrit comment cloner une machine virtuelle Classic unique vers Azure Resource Manager à l’aide de scripts PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="singhkay"/>

# Cloner une machine virtuelle Classic vers Azure Resource Manager à l’aide de scripts PowerShell

Cet article vous explique comment utiliser les scripts disponibles sur le site [Azure/classic-iaas-resourcemanager-migration](https://github.com/Azure/classic-iaas-resourcemanager-migration) pour cloner une machine virtuelle Classic **unique** vers le modèle de déploiement Azure Resource Manager.

>[AZURE.IMPORTANT]Le clonage à l’aide de ces scripts entraînera un temps d’arrêt pour votre machine virtuelle Classic. Si vous souhaitez effectuer une migration prise en charge par la plateforme, voir les articles suivants :
- [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](./virtual-machines-windows-migration-classic-resource-manager.md)
- [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## Obtention des scripts

>[AZURE.NOTE]Les scripts PowerShell ne sont pas pris en charge officiellement par le Support Microsoft. Ils sont donc disponibles en open source sur Github, et nous acceptons volontiers les demandes de publication de correctifs ou de scénarios supplémentaires.

Vous pouvez obtenir les scripts en téléchargeant le fichier zip à partir du référentiel spécifié ci-dessus, ou cloner le référentiel en utilisant **l’une** des commandes ci-dessous :

```
git clone https://github.com/Azure/classic-iaas-resourcemanager-migration.git
```

**OR**

```
git clone git@github.com:Azure/classic-iaas-resourcemanager-migration.git
```

## Importer le module de migration

L’étape suivante consiste à importer le module dans votre session PowerShell. Vous pouvez effectuer cette opération avec la commande ci-dessous :

```
Import-Module .\asm2arm.psd1
```

## S’authentifier auprès des abonnements de modèle de déploiement Classic et Azure Resource Manager

Pour que vous puissiez cloner votre machine virtuelle Classic, vous devez authentifier votre session PowerShell auprès des piles de modèle de déploiement Classic et Azure Resource Manager. Vous pouvez effectuer cette opération avec les applets de commande suivantes :

```
Add-AzureAccount
Login-AzureRmAccount
```

>[AZURE.IMPORTANT]Assurez-vous que les abonnements par défaut sont sélectionnés à l’aide de `Select-AzureSubscription` pour le modèle de déploiement Classic et au moyen de Set-AzureRmContext pour Azure Resource Manager.

## Utilisation des scripts

### Applets de commande

L’importation du module ajoute les deux applets de commande ci-dessous à votre session.

```
Add-AzureSMVmToRM
New-AzureSmToRMDeployment
```

Ces applets de commande mettent en œuvre les fonctionnalités spécifiées ci-après.

#### Add-AzureSMVmToRM
- Génère un ensemble de modèles Azure Resource Manager et de scripts PowerShell impératifs (pour copier les objets blob de disque et dans le cas où la machine virtuelle comporte des extensions d’agent de machine virtuelle) en fonction d’une machine virtuelle donnée.

>[AZURE.IMPORTANT]Si le commutateur `-Deploy` est spécifié, cette applet de commande appelle l’applet de commande `New-AzureSmToRMDeployment` pour déployer les modèles Azure Resource Manager et les scripts PowerShell impératifs générés ci-dessus.

#### New-AzureSmToRMDeployment
- Déploie les modèles et les scripts PowerShell impératifs générés par l’applet de commande `Add-AzureSMVmToRM` pour démarrer la migration.

### Identifier la machine virtuelle Classic à cloner

Vous pouvez effectuer cette opération en stockant l’état de la machine virtuelle Classic dans une variable et en transmettant cette dernière à l’applet de commande `Add-AzureSMVmToRM` ou en utilisant directement les paramètres `ServiceName` et `Name` de machine virtuelle. Une fois que vous avez identifié la machine virtuelle Classic, vous pouvez la cloner à l’aide de l’applet de commande `Add-AzureSMVmToRM`.

```
$vm = Get-AzureVm -ServiceName acloudservice -Name atestvm
Add-AzureSMVmToRM -VM $vm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

**OR**

```
Add-AzureSMVmToRM -ServiceName acloudservice -Name atestvm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

>[AZURE.IMPORTANT]Étant donné que nous utilisons le commutateur `-Deploy` dans les exemples ci-dessus, l’utilisation de l’applet de commande `New-AzureSmToRMDeployment` n’est pas nécessaire.

## Comment la migration fonctionne-t-elle avec ces scripts ?

Les applets de commande suivent les étapes de clonage de la machine virtuelle Classic et génèrent des ressources sous la forme de tables de hachage PowerShell personnalisées pour les fournisseurs de ressources de stockage, de réseau et de calcul. Ces tables de hachage qui représentent les ressources sont ajoutées à un tableau, converties par la suite en modèle par le biais de la sérialisation au format JSON, puis écrites dans un fichier.

Le modèle crée les fichiers en fonction de l’existence d’extensions d’agent de machine virtuelle Classic et de la valeur de l’option DiskAction. Ces fichiers sont tous placés dans le répertoire spécifié par le paramètre OutputFileFolder. Ces fichiers sont les suivants :

1. `<ServiceName>-<VMName>-setup<optional timestamp>.json` : représente les ressources qu’il est nécessaire de préparer avant de procéder au clonage de la machine virtuelle Classic, et qui peuvent être identiques pour les machines virtuelles suivantes (nous ne conservons pas l’état entre deux exécutions successives, mais étant donné qu’un compte de stockage doit être approvisionné avant le déclenchement d’une opération de copie d’objets blob, laquelle est effectuée de façon impérative, il était logique de regrouper les ressources identiques).

2.  `<ServiceName>-<VMName>-deploy<optional timestamp>.json` : contient le modèle pour la machine virtuelle Resource Manager.
3.  `<ServiceName>-<VMName>-parameters<optional timestamp>.json` : contient les paramètres réels transmis aux modèles.
4.  `<ServiceName>-<VMName>-setextensions<optional timestamp>.json` : contient un ensemble d’applets de commande PowerShell à exécuter pour définir les extensions d’agent de machine virtuelle.
4.  `<ServiceName>-<VMName>-copydisks<optional timestamp>.json` : contient un ensemble d’applets de commande PowerShell à exécuter pour copier les objets blob de disque, si l’option CopyDisks est spécifiée.

Si l’indicateur -Deploy est défini, après la génération des fichiers, l’applet de commande déploie le modèle <NomService>-<NomMV>-setup.json, copie les objets blob de disque de la machine virtuelle source si le paramètre DiskAction est défini sur CopyDisks, puis déploie le modèle <NomService>-<NomMV>-deploy.json en utilisant le fichier <NomService>-<NomMV>-parameters.json contenant les paramètres. Une fois le déploiement de la machine virtuelle effectué, s’il existe un script impératif (pour les extensions d’agent de machine virtuelle) ou un script concernant la copie des disques, ces scripts sont exécutés.

### Détails du réseau
L’applet de commande n’est pas destinée à cloner les paramètres de réseau Classic vers Resource Manager. Elle utilise les fonctionnalités de mise en réseau de la façon la mieux adaptée au clonage de la machine virtuelle proprement dite. Voici en quoi consiste le déroulement des opérations dans différents scénarios :

1.  Aucun réseau virtuel sur le groupe de ressources cible
    - La machine virtuelle source ne se trouve pas sur un sous-réseau : un réseau virtuel par défaut avec 10.0.0.0/16 est créé en même temps qu’un sous-réseau, avec l’espace d’adressage 10.0.0.0/22.
    - La machine virtuelle source appartient à un sous-réseau : le réseau virtuel dans lequel se trouve la machine virtuelle est découvert, et la spécification du réseau virtuel ainsi que les sous-réseaux sont copiés.
2.  Groupe de ressources cible avec un réseau virtuel portant le nom `<VM virtual network>arm` (la chaîne « arm » est ajoutée)
    - Si le réseau virtuel comporte un sous-réseau avec un nom et un espace d’adressage identiques, utilisez-le.
    - Si aucun sous-réseau approprié n’est trouvé, recherchez un bloc d’adresses parmi les sous-réseaux existants avec le masque de bits 22 et utilisez ce dernier.

## Comparaison entre le clonage et la migration prise en charge par la plateforme

Il existe quelques différences entre l’approche de clonage actuelle et la procédure de migration prise en charge par la plateforme.

### Clonage


| Avantages | Inconvénients |
|--------------------------------------------------------|:----------------------------------------------------------------:|
| Possibilité de clonage de n’importe quelle machine virtuelle dans un service cloud | Temps d’arrêt découlant de l’obligation d’arrêter les machines virtuelles |
| Possibilité de clonage d’une machine virtuelle au sein ou en dehors d’un réseau virtuel | Délais importants dans les situations exigeant la copie des objets blob de disque |
| | Modification de la configuration du réseau (adresses IP internes, adresses IP virtuelles) pour la machine virtuelle |


### Migration prise en charge par la plateforme


| Avantages | Inconvénients |
|----------------------------------|:-------------------------------------------:|
| Aucun temps d’arrêt induit pour la majorité des configurations de machine virtuelle dans un réseau virtuel | Obligation de migration simultanée de toutes les machines virtuelles d’un service cloud ou du réseau virtuel dans lequel toutes les ressources sont déployées |
 
 
## Scénarios non pris en charge

**Les scripts de clonage ne prennent pas en charge les opérations ci-après :**

 1. Arrêt d’une machine virtuelle en cours d’exécution
 2. Modification de vos données/disques
 3. Clonage de machines virtuelles en cours d’exécution
 4. Clonage automatique de plusieurs machines virtuelles dans un scénario complexe
 5. Clonage de l’ensemble de la configuration du réseau ASM
 6. Création de machines virtuelles à charge équilibrée (nous partons du principe que cette configuration doit être gérée de manière explicite par l’expert Azure)
 
 
## Configurations testées

L’applet de commande _Add-AzureSMVmToRM_ a été validée pour les cas de test suivants :

| # | Description |
|:---|:---|
| 1 | Machine virtuelle Windows avec un disque de système d’exploitation existant |
| 2 | Machine virtuelle Linux avec un disque de système d’exploitation existant |
| 3 | Machine virtuelle Windows avec des disques de système d’exploitation et de données existants |
| 4 | Machine virtuelle Linux avec des disques de système d’exploitation et de données existants |
| 5 | Machine virtuelle Windows avec un nouveau disque de système d’exploitation provenant de la Bibliothèque d’images |
| 6 | Machine virtuelle Linux avec un nouveau disque de système d’exploitation provenant de la Bibliothèque d’images |
| 7 | Machine virtuelle Windows avec un nouveau disque de système d’exploitation et des disques de données vides |
| 8 | Machine virtuelle Linux avec un nouveau disque de système d’exploitation et des disques de données vides |
| 9 | Machine virtuelle Windows avec des points de terminaison publics |
| 10 | Machine virtuelle Linux avec des points de terminaison publics |
| 11 | Machine virtuelle Windows avec un certificat WinRM |
| 12 | Machine virtuelle Windows dans un réseau virtuel et un sous-réseau |
| 13\. | Machine virtuelle Linux dans un réseau virtuel et un sous-réseau |
| 14 | Machine virtuelle Windows avec des extensions personnalisées |
| 15 | Machine virtuelle Windows dans un groupe à haute disponibilité |
| 16 | Machine virtuelle Windows dans un groupe à haute disponibilité, avec plusieurs disques de données, des points de terminaison publics, dans un réseau virtuel et un sous-réseau et avec des extensions personnalisées |
| 17 | Machine virtuelle Linux dans un groupe à haute disponibilité, avec plusieurs disques de données, des points de terminaison publics, dans un réseau virtuel et un sous-réseau et avec des extensions personnalisées |

## Remarques
1. Si plusieurs machines virtuelles sont clonées les unes après les autres à brefs intervalles, des conflits de nom DNS risquent de survenir pour les adresses IP publiques en raison du temps d’actualisation du cache DNS.

<!---HONumber=AcomDC_0824_2016-->