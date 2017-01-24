---
title: "Gérer les nœuds de calcul de cluster HPC Pack | Microsoft Docs"
description: "En savoir plus sur les outils de script PowerShell pour ajouter, supprimer, démarrer et arrêter des nœuds de calcul de cluster HPC Pack dans Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 3cd3c847eb476c783d09f5f18a5a54a75ade6c5f


---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Gérer le nombre et la disponibilité des nœuds de calcul dans un cluster HPC Pack dans Azure
Une fois un cluster HPC Pack créé dans des machines virtuelles Azure, il peut s’avérer utile de pouvoir facilement ajouter, supprimer, démarrer (approvisionner) ou arrêter (annuler l’approvisionnement) de nombreuses machines virtuelles à nœud de calcul dans le cluster. Pour effectuer ces tâches, exécutez des scripts Azure PowerShell qui sont installés sur la machine virtuelle à nœud principal. Ces scripts vous aident à contrôler le nombre et la disponibilité de vos ressources de cluster HPC Pack afin de contrôler les coûts.

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

## <a name="prerequisites"></a>Composants requis
* **Cluster HPC Pack dans les machines virtuelles Azure** : créez un cluster HPC Pack dans le modèle de déploiement classique en utilisant au moins HPC Pack 2012 R2 Update 1. Par exemple, vous pouvez automatiser le déploiement à l’aide de l’image de machine virtuelle de HPC Pack actuelle dans Azure Marketplace et un script Azure PowerShell. Pour plus d’informations, y compris concernant la configuration requise, consultez [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  
    Après le déploiement, trouvez les scripts de gestion de nœud dans le dossier %CCP\_HOME%bin sur le nœud principal. Vous devez exécuter chacun des scripts en tant qu’administrateur.
* **Fichier de paramètres de publication ou certificat de gestion Azure**. Vous devez effectuer l’une des opérations suivantes sur le nœud principal :
  
  * **Importer le fichier de publication de paramètres**. Pour ce faire, exécutez les applets de commande Azure PowerShell suivantes sur le nœud principal :
    
    ```
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Configurez le certificat de gestion Azure sur le nœud principal**. Si vous disposez du fichier .cer, importez-le dans le magasin de certificats CurrentUser\My, puis exécutez l’applet de commande Azure PowerShell suivante pour votre environnement Azure (AzureCloud ou AzureChinaCloud) :
    
    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Ajouter des machines virtuelles à nœud de calcul
Ajoutez des nœuds de calcul avec le script **Add-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Paramètres
* **ServiceName** : nom du service cloud auquel les nouvelles machines virtuelles à nœud de calcul doivent être ajoutées.
* **ImageName** : nom de l’image de machine virtuelle Azure, qui peut être obtenu par le biais du Portail Azure Classic ou de l’applet de commande Azure PowerShell **Get-AzureVMImage**. L’image doit répondre aux exigences suivantes :
  
  1. Un système d’exploitation Windows doit être installé.
  2. HPC Pack doit être installé dans le rôle de nœud de calcul.
  3. L’image doit être une image privée dans la catégorie Utilisateur, et non une image de machine virtuelle Azure publique.
* **Quantity** : nombre de machines virtuelles à nœud de calcul à ajouter.
* **InstanceSize** : taille des machines virtuelles à nœud de calcul.
* **DomainUserName** : nom d’utilisateur de domaine à utiliser pour joindre les nouvelles machines virtuelles au domaine.
* **DomainUserPassword** : mot de passe de l’utilisateur de domaine.
* **NodeNameSeries** (facultatif): modèle d’affectation de noms pour les nœuds de calcul. Le format doit être le suivant : &lt;*Root\_Name*&gt;&lt;*Start\_Number*&gt;%. Par exemple, MyCN%10% représente une série de noms de nœud de calcul commençant par MyCN11. Si ce paramètre n’est pas spécifié, le script utilise la série d’affectation de noms configurée dans le cluster HPC.

### <a name="example"></a>Exemple
L’exemple suivant ajoute 20 machines virtuelles à nœud de calcul de grande taille au service cloud *hpcservice1*, en se basant sur l’image de machine virtuelle *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Supprimer des machines virtuelles à nœud de calcul
Supprimez des nœuds de calcul avec le script **Remove-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe
```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Paramètres
* **Name** : noms des nœuds de cluster à supprimer. Les caractères génériques sont pris en charge. Le nom du jeu de paramètres est Name. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.
* **Nœud** : objet HpcNode des nœuds à supprimer, qui peut être obtenu par le biais de l’applet de commande HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètres est Node. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.
* **DeleteVHD** (facultatif) : permet de supprimer les disques associés aux machines virtuelles qui sont supprimées.
* **Force** (facultatif) : permet de mettre les nœuds HPC hors connexion avant de les supprimer.
* **Confirm** (facultatif) : permet d’inviter à confirmer avant d’exécuter la commande.
* **WhatIf** : permet de décrire l’exécution de la commande sans l’exécuter.

### <a name="example"></a>Exemple
L’exemple suivant met hors connexion les nœuds dont le nom commence par *HPCNode-CN-* , puis supprime les nœuds et leurs disques associés.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Démarrer des machines virtuelles à nœud de calcul
Démarrez des nœuds de calcul avec le script **Start-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe
```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Paramètres
* **Name** : noms des nœuds de cluster à démarrer. Les caractères génériques sont pris en charge. Le nom du jeu de paramètres est Name. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.
* **Nœud**: objet HpcNode des nœuds à démarrer, qui peut être obtenu par le biais de l’applet de commande HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètres est Node. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.

### <a name="example"></a>Exemple
L’exemple suivant démarre les nœuds dont le nom commence par *HPCNode-CN-*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Arrêter des machines virtuelles à nœud de calcul
Arrêtez des nœuds de calcul avec le script **Stop-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe
```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Paramètres
* **Name**: noms des nœuds de cluster à arrêter. Les caractères génériques sont pris en charge. Le nom du jeu de paramètres est Name. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.
* **Nœud** : objet HpcNode des nœuds à arrêter, qui peut être obtenu par le biais de l’applet de commande HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètres est Node. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.
* **Force** (facultatif) : permet de mettre les nœuds HPC hors connexion avant de les arrêter.

### <a name="example"></a>Exemple
L’exemple suivant met hors connexion les nœuds dont le nom commence par *HPCNode-CN-* , puis les arrête.

Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force

## <a name="next-steps"></a>Étapes suivantes
* Pour savoir comment augmenter ou réduire automatiquement les nœuds de cluster en fonction de la charge de travail actuelle des travaux et des tâches sur le cluster, consultez [Agrandir et réduire automatiquement les ressources de cluster HPC Pack dans Azure en fonction de la charge de travail du cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Dec16_HO1-->


