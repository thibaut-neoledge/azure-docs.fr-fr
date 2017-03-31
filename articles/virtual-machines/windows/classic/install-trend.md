---
title: "Installer Trend Micro Deep Security sur une machine virtuelle | Microsoft Docs"
description: "Cet article décrit comment installer et configurer la sécurité Trend Micro sur une machine virtuelle créée avec le modèle de déploiement Classic dans Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: cb10c04d76e957f88d67a791e3b4a19797704eea
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Windows
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Cet article décrit comment installer et configurer Trend Micro Deep Security comme service sur une machine virtuelle nouvelle ou existante exécutant Windows Server. Deep Security inclut une protection anti-programmes malveillants, un pare-feu, un système de prévention contre les intrusions et une surveillance de l’intégrité.

Le client est installé sous forme d’extension de sécurité via l’agent de machine virtuelle. Sur une nouvelle machine virtuelle, vous devez installer l'agent de machine virtuelle en même temps que l'agent Deep Security. Sur une machine virtuelle existante sans l'agent, vous devez d'abord télécharger et installer l'agent. Cet article aborde ces deux situations.

Si vous disposez d’un abonnement Trend Micro pour une solution locale, vous pouvez l’utiliser pour protéger vos machines virtuelles Microsoft Azure. Si vous n’êtes pas encore client, vous pouvez vous inscrire pour une version d’évaluation. Pour plus d’informations sur cette solution, consultez le billet de blog [Extension de l’agent de machine virtuelle Microsoft Azure pour Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installation de l'agent Deep Security sur une nouvelle machine virtuelle
Le [portail Azure Classic](http://manage.windowsazure.com) vous permet d’installer l’agent de machine virtuelle et l’extension de sécurité Trend Micro lors de l’utilisation de l’option **À partir de la galerie** pour créer la machine virtuelle. La création d’une machine virtuelle unique à l’aide du portail est une méthode simple pour ajouter une protection Trend Micro.

L'option **À partir de la galerie** ouvre un Assistant qui vous aide à configurer la machine virtuelle. Utilisez la dernière page de l’Assistant pour installer l’agent de machine virtuelle et l’extension de sécurité Trend Micro. Pour des instructions générales, consultez [Création d’une machine virtuelle sous Windows dans le Portail Azure Classic](tutorial.md). Lorsque vous atteignez la dernière page de l’Assistant, procédez comme suit :

1. Sous **Agent de machine virtuelle**, cochez **Installer l’agent de machine virtuelle**.
2. Sous **Extensions de sécurité**, cochez **Trend Micro Deep Security Agent**.
   
   ![Installez l'agent de machine virtuelle et l'agent Deep Security.](./media/install-trend/InstallVMAgentandTrend.png)
3. Cliquez sur la coche pour créer la machine virtuelle.

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installation de l’agent Deep Security sur une machine virtuelle existante
Pour installer l’agent sur une machine virtuelle existante, vous avez besoin des éléments suivants :

* Le module Azure PowerShell, version 0.8.2 ou ultérieure, installé sur votre poste local. Vous pouvez vérifier la version d’Azure PowerShell installée à l’aide de la commande **Get-Module azure | format-table version** . Pour des instructions et un lien vers la dernière version, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs). Connectez-vous à votre abonnement Azure à l’aide d’ `Add-AzureAccount`.
* L'agent de machine virtuelle installé sur la machine virtuelle cible.

Tout d'abord, vérifiez que l’agent de machine virtuelle est déjà installé. Renseignez le nom du service cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes de niveau administrateur Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < et >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Si vous ignorez le nom du service cloud et de la machine virtuelle, exécutez **Get-AzureVM** afin d’afficher ces informations pour toutes les machines virtuelles de l’abonnement actuel.

Si la commande **write-host** renvoie **True**, cela signifie que l’agent de machine virtuelle est installé. Si elle retourne **False**, consultez les instructions et un lien vers le téléchargement dans le billet de blog Azure [Agent de machine virtuelle et extensions - 2e partie](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Si l'agent de machine virtuelle est installé, exécutez ces commandes.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes
Quelques minutes sont nécessaires avant que l'exécution de l'agent ne démarre, après l'installation. Vous devez ensuite activer Deep Security sur la machine virtuelle de façon à ce qu'elle puisse être gérée par Deep Security Manager. Pour obtenir des instructions supplémentaires, consultez les articles suivants :

* L’article de Trend sur cette solution, [Sécurité cloud instantanée pour Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* Un [exemple de script Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) pour configurer la machine virtuelle.
* [instructions](http://go.microsoft.com/fwlink/?LinkId=404099) de l’exemple.

## <a name="additional-resources"></a>Ressources supplémentaires
[Connexion à une machine virtuelle exécutant Windows Server]

[Fonctionnalités et extensions de machine virtuelle Azure]

<!--Link references-->
[Connexion à une machine virtuelle exécutant Windows Server]:connect-logon.md
[Fonctionnalités et extensions de machine virtuelle Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

