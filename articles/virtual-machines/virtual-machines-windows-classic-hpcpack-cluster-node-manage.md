<properties
 pageTitle="Gérer les nœuds de calcul de cluster HPC Pack | Microsoft Azure"
 description="En savoir plus sur les outils de script PowerShell pour ajouter, supprimer, démarrer et arrêter des nœuds de calcul de cluster HPC Pack dans Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="04/18/2016"
 ms.author="danlep"/>

# Gérer le nombre et la disponibilité des nœuds de calcul dans un cluster HPC Pack dans Azure

Une fois un cluster HPC Pack créé dans des machines virtuelles Azure, il peut s’avérer utile de pouvoir facilement ajouter, supprimer, démarrer (approvisionner) ou arrêter (annuler l’approvisionnement) de nombreuses machines virtuelles à nœud de calcul dans le cluster. Pour effectuer ces tâches, exécutez des scripts Azure PowerShell qui sont installés sur la machine virtuelle à nœud principal. Ces scripts vous aident à contrôler le nombre et la disponibilité de vos ressources de cluster HPC Pack afin de contrôler les coûts.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## Composants requis

* **Cluster HPC Pack dans les machines virtuelles Azure** : créez un cluster HPC Pack dans le modèle de déploiement classique en utilisant au moins HPC Pack 2012 R2 Update 1. Par exemple, vous pouvez automatiser le déploiement à l’aide de l’image de machine virtuelle de HPC Pack actuelle dans Azure Marketplace et un script Azure PowerShell. Pour plus d’informations, y compris concernant la configuration requise, consultez [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

    Après le déploiement, trouvez les scripts de gestion de nœud dans le dossier %CCP\_HOME%bin sur le nœud principal. Vous devez exécuter chacun des scripts en tant qu’administrateur.

* **Fichier de paramètres de publication ou certificat de gestion Azure**. Vous devez effectuer l’une des opérations suivantes sur le nœud principal :

    * **Importer le fichier de publication de paramètres**. Pour ce faire, exécutez les applets de commande Azure PowerShell suivantes sur le nœud principal :

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **Configurez le certificat de gestion Azure sur le nœud principal**. Si vous disposez du fichier .cer, importez-le dans le magasin de certificats CurrentUser\\My, puis exécutez l’applet de commande Azure PowerShell suivante pour votre environnement Azure (AzureCloud ou AzureChinaCloud) :

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## Ajouter des machines virtuelles à nœud de calcul

Ajoutez des nœuds de calcul avec le script **Add-HpcIaaSNode.ps1**.

### Syntaxe
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### Paramètres

* **ServiceName** : nom du service cloud auquel les nouvelles machines virtuelles à nœud de calcul doivent être ajoutées.

* **ImageName** : nom de l’image de machine virtuelle Azure, qui peut être obtenu par le biais du portail Azure Classic ou de l’applet de commande Azure PowerShell **Get-AzureVMImage**. L’image doit répondre aux exigences suivantes :

    1. Un système d’exploitation Windows doit être installé.

    2. HPC Pack doit être installé dans le rôle de nœud de calcul.

    3. L’image doit être une image privée dans la catégorie Utilisateur, et non une image de machine virtuelle Azure publique.

* **Quantity** : nombre de machines virtuelles à nœud de calcul à ajouter.

* **InstanceSize** : taille des machines virtuelles à nœud de calcul.

* **DomainUserName** : nom d’utilisateur de domaine à utiliser pour joindre les nouvelles machines virtuelles au domaine.

* **DomainUserPassword** : mot de passe de l’utilisateur de domaine.

* **NodeNameSeries** (facultatif): modèle d’affectation de noms pour les nœuds de calcul. Le format doit être &lt;*nom\_racine*&gt;&lt;*numéro\_départ*&gt;%. Par exemple, MyCN%10% représente une série de noms de nœud de calcul commençant par MyCN11. Si ce paramètre n’est pas spécifié, le script utilise la série d’affectation de noms configurée dans le cluster HPC.

### Exemple

L’exemple suivant ajoute 20 machines virtuelles à nœud de calcul de grande taille au service cloud *hpcservice1*, en se basant sur l’image de machine virtuelle *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## Supprimer des machines virtuelles à nœud de calcul

Supprimez des nœuds de calcul avec le script **Remove-HpcIaaSNode.ps1**.

### Syntaxe

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### Paramètres

* **Name** : noms des nœuds de cluster à supprimer. Les caractères génériques sont pris en charge. Le nom du jeu de paramètres est Name. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.

* **Nœud** : objet HpcNode des nœuds à supprimer, qui peut être obtenu par le biais de l’applet de commande HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètres est Node. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.

* **DeleteVHD** (facultatif) : permet de supprimer les disques associés aux machines virtuelles qui sont supprimées.

* **Force** (facultatif) : permet de mettre les nœuds HPC hors connexion avant de les supprimer.

* **Confirm** (facultatif) : permet d’inviter à confirmer avant d’exécuter la commande.

* **WhatIf** : permet de décrire l’exécution de la commande sans l’exécuter.

### Exemple

L’exemple suivant met hors connexion les nœuds dont le nom commence par *HPCNode-CN-*, puis supprime les nœuds et leurs disques associés.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## Démarrer des machines virtuelles à nœud de calcul

Démarrez des nœuds de calcul avec le script **Start-HpcIaaSNode.ps1**.

### Syntaxe

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### Paramètres

* **Name** : noms des nœuds de cluster à démarrer. Les caractères génériques sont pris en charge. Le nom du jeu de paramètres est Name. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.

* **Nœud** : objet HpcNode des nœuds à démarrer, qui peut être obtenu par le biais de l’applet de commande HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètres est Node. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.

### Exemple

L’exemple suivant démarre les nœuds dont le nom commence par *HPCNode-CN-*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## Arrêter des machines virtuelles à nœud de calcul

Arrêtez des nœuds de calcul avec le script **Stop-HpcIaaSNode.ps1**.

### Syntaxe

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### Paramètres


* **Name** : noms des nœuds de cluster à arrêter. Les caractères génériques sont pris en charge. Le nom du jeu de paramètres est Name. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.

* **Nœud** : objet HpcNode des nœuds à arrêter, qui peut être obtenu par le biais de l’applet de commande HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètres est Node. Vous ne pouvez pas spécifier à la fois les paramètres **Name** et **Node**.

* **Force** (facultatif) : permet de mettre les nœuds HPC hors connexion avant de les arrêter.

### Exemple

L’exemple suivant met hors connexion les nœuds dont le nom commence par *HPCNode-CN-*, puis les arrête.

Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force

## Étapes suivantes

* Pour savoir comment augmenter ou réduire automatiquement les nœuds de cluster en fonction de la charge de travail actuelle des travaux et des tâches sur le cluster, consultez [Agrandir et réduire automatiquement les ressources de cluster HPC Pack dans Azure en fonction de la charge de travail du cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!---HONumber=AcomDC_0629_2016-->