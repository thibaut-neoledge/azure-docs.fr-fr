---
title: Articles techniques concernant les machines virtuelles Windows | Microsoft Azure
description: "Liste complète des articles de la documentation Microsoft Azure liée aux machines virtuelles Windows dans le modèle de déploiement Classic"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-service-management
editor: 
ms.assetid: 7f9a508b-34ec-4bdb-92d1-8844480369d5
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 5c971ed2e09bd275dc34da3ea94b3ff4a6338c9a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---
# <a name="technical-articles-for-windows-vms-in-the-classic-deployment-model"></a>Articles techniques concernant les machines virtuelles Windows dans le modèle de déploiement Classic
Recherchez tous les documents dont vous avez besoin pour créer et gérer des machines virtuelles Azure basées sur Windows dans le modèle de déploiement Classic.

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

## <a name="overview"></a>Vue d'ensemble
[À propos des machines virtuelles](windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Forum aux questions sur les machines virtuelles Azure créées avec le modèle de déploiement classique](windows/classic/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Comparaison des machines virtuelles, des sites web et des services cloud](../app-service-web/choose-web-site-cloud-service-vm.md)

[Machines virtuelles et conteneurs dans Azure](windows/containers.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="environment-setup"></a>Configuration de l’environnement
[Compte gratuit](https://azure.microsoft.com/free/)

[Installation d'Azure PowerShell](/powershell/azure/overview)

[Installation de l’interface de ligne de commande Azure](../cli-install-nodejs.md)

## <a name="get-started"></a>Prise en main
[Parcours d’apprentissage pour les machines virtuelles Windows](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)

[Création d’une machine virtuelle Windows dans le portail Azure Classic.](windows/classic/tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Connexion à une machine virtuelle classique exécutant Windows Server](windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="plan"></a>Planification
[À propos des images pour les machines virtuelles classiques](windows/classic/about-images.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Tailles de machines virtuelles](windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Tailles de machines virtuelles de calcul haute performance](windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Maintenance planifiée des machines virtuelles Azure](windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Instructions d’implémentation des services d’infrastructure Azure](windows/infrastructure-subscription-accounts-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Création d’un groupe à haute disponibilité pour machines virtuelles](windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="deploy"></a>Déployer
[Création d’une machine virtuelle personnalisée exécutant Windows](windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Capturer une machine virtuelle Windows créée avec le modèle de déploiement classique](windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Création et téléchargement d’un VHD Windows Server classique à l’aide de PowerShell](windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Automatisation du déploiement de machine virtuelle Azure avec Chef](windows/chef-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Création et configuration d’une machine virtuelle Windows classique avec Azure PowerShell](windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Injection de données personnalisées dans une machine virtuelle Azure](windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="manage"></a>Gérer
[Gérer vos machines virtuelles à l’aide d’Azure PowerShell](windows/classic/manage-psh.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)

[À propos de l'agent de machine virtuelle et des extensions](windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Gestion des extensions de machine virtuelle](windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Extension de script personnalisé pour machines virtuelles Windows classiques](windows/classic/extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](windows/migration-classic-resource-manager-deep-dive.md)

## <a name="configure"></a>Configuration
[Réinitialisation d’un mot de passe ou du service Bureau à distance pour une machine virtuelle Windows](windows/reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[À propos des extensions et des fonctionnalités des machines virtuelles](windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Windows](windows/classic/install-symantec.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Windows](windows/classic/install-trend.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Configuration d’un groupe à haute disponibilité pour des machines virtuelles dans le modèle de déploiement classique](windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Comment configurer des points de terminaison sur une machine virtuelle Azure classique](windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="storage"></a>Storage
[À propos des disques et des VHD pour les machines virtuelles Azure](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Attachement d’un disque de données à une machine virtuelle Windows classique](windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Détachement d’un disque de données d’une machine virtuelle Windows classique](windows/classic/detach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Utilisation du lecteur D comme lecteur de données sur une machine virtuelle Windows](windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="networking"></a>Mise en réseau
[Présentation du réseau virtuel.](../virtual-network/virtual-networks-overview.md)

[Connectez des ordinateurs virtuels créés avec le modèle de déploiement classique avec un réseau virtuel ou un service cloud](windows/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Gérer les groupes de sécurité réseau avec Azure PowerShell](../virtual-network/virtual-networks-create-nsg-classic-ps.md)

[Créer un équilibrage de charge](../load-balancer/load-balancer-get-started-internet-classic-portal.md)

## <a name="develop"></a>Développement
[Créer et gérer des machines virtuelles Azure dans Visual Studio](windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Création d’une machine virtuelle pour une application web avec Visual Studio](windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Exécution d'une tâche nécessitant beaucoup de ressources en langage Java sur une machine virtuelle](windows/classic/java-run-compute-intensive-task.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Application web Django Hello World sur une machine virtuelle Windows Server](windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="workloads"></a>Charges de travail
[HPC Pack](windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[MongoDB](windows/classic/install-mongodb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[MySQL](windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Oracle](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)

[SAP](windows/classic/sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[SQL Server](./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[Tomcat](windows/classic/java-run-tomcat-app-server.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="reference"></a>Référence
[Commandes de l’interface de ligne de commande Azure en mode Service Management](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)

[API REST de gestion de service](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[API .NET de gestion de service](https://msdn.microsoft.com/library/azure/mt420161.aspx)

[Documentation de référence sur l’applet de commande PowerShell de gestion des services Azure](/powershell/azure/overview?view=azuresmps-3.7.0)

## <a name="troubleshooting"></a>Résolution de problèmes
[Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure exécutant Windows](windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Résolution des problèmes d’accès à une application exécutée sur une machine virtuelle Azure](windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Résoudre les problèmes d’allocation pendant la création, le redémarrage ou le redimensionnement de machines virtuelles dans Azure](windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Résoudre les problèmes de déploiement Classic liés à la création d’une machine virtuelle Windows dans Azure](windows/classic/troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Résoudre les problèmes de déploiement Classic liés au redémarrage ou au redimensionnement d’une machine virtuelle Windows existante dans Azure](windows/classic/virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)

