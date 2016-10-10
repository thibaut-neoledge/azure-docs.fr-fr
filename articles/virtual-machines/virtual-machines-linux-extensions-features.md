<properties
 pageTitle="Extensions et fonctionnalités des machines virtuelles | Microsoft Azure"
 description="Découvrez les extensions disponibles pour les machines virtuelles, regroupées par ce qu’ils fournissent ou améliorent."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="nepeters"/>

# À propos des extensions et des fonctionnalités des machines virtuelles

## Extensions de machine virtuelle Azure

Les extensions de machine virtuelle Azure sont de petites applications qui exécutent des tâches de configuration et d’automatisation post-déploiement sur des machines virtuelles Azure. Par exemple, si une machine virtuelle requiert l’installation d’un logiciel, une protection antivirus ou une configuration de Docker, il est possible d’effectuer ces tâches à l’aide d’une extension de machine virtuelle. Les extensions de machine virtuelle Azure peuvent être exécutées à l’aide de l’interface de ligne de commande Azure, de PowerShell, de modèles Resource Manager et du portail Azure. Les extensions peuvent être associées à un nouveau déploiement de machine virtuelle ou s’exécuter sur tout système existant.

Ce document précise les conditions préalables pour une extension de machine virtuelle Azure. Il fournit également des instructions sur la manière de détecter des extensions de machine virtuelle disponibles.

## Agent Azure VM

L’agent Azure VM gère l’interaction entre une machine virtuelle et le contrôleur de structure Azure. L’agent VM est responsable de nombreux aspects fonctionnels liés au déploiement et à la gestion des machines virtuelles Azure, dont les extensions de machine virtuelle en cours d’exécution. L’agent Azure VM est préinstallé sur les images de la galerie Azure et peut être installé sur les systèmes d’exploitation pris en charge.

Pour plus d’informations sur les systèmes d’exploitation pris en charge et des instructions d’installation, voir [Guide d’utilisateur de l’agent Linux Azure](./virtual-machines-linux-agent-user-guide.md).

## Détecter des extensions de machine virtuelle

De nombreuses extensions de machine virtuelle différentes sont disponibles pour une utilisation avec les machines virtuelles Azure. Pour en obtenir la liste complète, exécutez la commande suivante avec l’interface de ligne de commande Azure, en remplaçant location par l’emplacement de votre choix.

```none
azure vm extension-image list westus
```

<br />

## Extensions de machine virtuelle courantes

|Nom de l’extension |Description |Informations complémentaires |
|---|---|---|
|Extension de script personnalisé pour Linux | Exécuter des scripts sur une machine virtuelle Azure |[Extension de script personnalisé pour Linux](./virtual-machines-linux-extensions-customscript.md) |
|Extension Docker |Installe le démon Docker pour prendre en charge des commandes Docker distantes. | [Extension de machine virtuelle Docker](./virtual-machines-linux-dockerextension.md) |
|Extension d’accès aux machines virtuelles | Récupérer l’accès à une machine virtuelle Azure |[Extension d’accès aux machines virtuelles](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
|Extension Diagnostics Azure |Gérer les diagnostics Azure |[Extension Diagnostics Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

<!---HONumber=AcomDC_0928_2016-->