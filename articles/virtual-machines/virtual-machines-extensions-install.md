<properties
 pageTitle="Gestion des extensions de machine virtuelle | Microsoft Azure"
 description="Explique comment ajouter, rechercher, mettre à jour et supprimer des extensions pour les machines virtuelles Azure, dans le modèle de déploiement classique."
 services="virtual-machines"
 documentationCenter=""
 authors="squillace"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="12/08/2015"
 ms.author="rasquill"/>
#Gestion des extensions de machine virtuelle

Décrit comment trouver, ajouter, modifier ou supprimer des extensions de machines virtuelles Windows ou Linux dans Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Mode Resource Manager


##Utilisation d'extensions de machines virtuelles

Les extensions de machines virtuelles Azure mettent en œuvre des comportements ou des fonctionnalités grâce auxquels d’autres programmes peuvent fonctionner sur des machines virtuelles Azure (par exemple, l’extension **WebDeployForVSDevTest** permet à Visual Studio de déployer des solutions web sur votre machine virtuelle Azure) ou interagir avec votre machine virtuelle pour prendre en charge un autre comportement (par exemple, vous pouvez utiliser les extensions d’accès à la machine virtuelle à partir de PowerShell, l’interface de ligne de commande Azure et des clients REST pour réinitialiser ou modifier des valeurs d’accès à distance de votre machine virtuelle Azure).

>[AZURE.IMPORTANT]Pour obtenir une liste complète des extensions selon les fonctionnalités prises en charge, consultez [Fonctionnalités et extensions de machine virtuelle Azure](virtual-machines-extensions-features.md). Étant donné que chaque extension de machine virtuelle prend en charge une fonctionnalité spécifique, elle détermine ce que vous pouvez et ne pouvez pas faire avec une extension. Par conséquent, avant de modifier votre machine virtuelle, assurez-vous d'avoir bien lu la documentation relative à l'extension de machine virtuelle que vous souhaitez utiliser. La suppression de certaines extensions de machine virtuelle n'est pas prise en charge. D'autres ont des propriétés qui peuvent être définies et qui modifient radicalement le comportement de la machine virtuelle.

Les tâches les plus courantes sont :

1.  Recherche d'extensions disponibles

2.  Mise à jour des extensions chargées

3.  Ajout d'extensions

4.  Suppression d'extensions

##Recherche d'extensions disponibles

Les extensions de machines virtuelles Azure sont (pour une liste complète des extensions suivant les fonctionnalités prises en charge, consultez [Fonctionnalités et extensions de machine virtuelle Azure](virtual-machines-extensions-features.md)). Vous pouvez localiser l'extension et obtenir des informations étendues en utilisant :

-   PowerShell
-   Interface multiplateforme Azure (Azure CLI)
-   API REST de gestion de service

Utilisez les applets de commande [Azure PowerShell](https://msdn.microsoft.com/library/azure/dn495240.aspx) ou les [API REST de gestion de service](https://msdn.microsoft.com/library/ee460799.aspx) pour rechercher des informations sur les extensions disponibles.

###Azure PowerShell

Certaines extensions disposent d'applets de commande Powershell spécifiques, ce qui simplifie leur configuration à partir de PowerShell. Mais les applets de commande suivantes fonctionnent pour toutes les extensions de machine virtuelle.

Vous pouvez utiliser les applets de commande suivantes pour obtenir des informations sur les extensions disponibles :

-   Pour les instances de rôles web ou de travail, vous pouvez utiliser l’applet de commande [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx).
-   Pour les instances de machines virtuelles, vous pouvez utiliser l’applet de commande [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx).

     Par exemple, le code qui suit montre comment obtenir les informations sur l’extension **IaaSDiagnostics** avec PowerShell.

        PS C:\PowerShell> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
        VERBOSE: 5:09:01 PM - Begin Operation: Get-AzureVMAvailableExtension
        VERBOSE: 5:09:06 PM - Completed Operation: Get-AzureVMAvailableExtension

        Publisher                   : Microsoft.Azure.Diagnostics
        ExtensionName               : IaaSDiagnostics
        Version                     : 1.2
        Label                       : Microsoft Monitoring Agent Diagnostics
        Description                 : Microsoft Monitoring Agent Extension
        PublicConfigurationSchema   :
        PrivateConfigurationSchema  :
        IsInternalExtension         : False
        SampleConfig                :
        ReplicationCompleted        : True
        Eula                        :
        PrivacyUri                  :
        HomepageUri                 :
        IsJsonExtension             : True
        DisallowMajorVersionUpgrade : False
        SupportedOS                 :
        PublishedDate               :
        CompanyName                 :


###Interface de ligne de commande Azure (Azure CLI)

Certaines extensions ont leurs propres commandes Azure CLI (par exemple, l'extension de machine virtuelle Docker), ce qui peut faciliter leur configuration. Mais les commandes suivantes fonctionnent pour toutes les extensions de machines virtuelles.

La commande **azure vm extension list** permet d’obtenir des informations sur les extensions disponibles et l’option **–-json** permet d’afficher toutes les informations disponibles sur une ou plusieurs extensions. Si vous n'utilisez pas de nom d'extension, la commande renvoie une description json de toutes les extensions disponibles.

Ainsi, l’exemple de code suivant montre comment répertorier les informations relatives à l’extension **IaaSDiagnostics** à l’aide de la commande Azure CLI **azure vm extension list** et utilise l’option **–-json** pour renvoyer des informations complètes.


    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



###API REST de gestion des services

Vous pouvez utiliser les API REST suivantes pour obtenir des informations sur les extensions disponibles :

-   Pour les instances de rôles web ou de travail, vous pouvez utiliser l’opération [Liste des extensions disponibles](https://msdn.microsoft.com/library/dn169559.aspx). Pour dresser une liste des versions des extensions disponibles, vous pouvez utiliser l’opération [Liste des versions d’extension](https://msdn.microsoft.com/library/dn495437.aspx).

-   Pour les instances de machines virtuelles, vous pouvez utiliser l’opération [Liste des extensions des ressources](https://msdn.microsoft.com/library/dn495441.aspx). Pour dresser une liste des versions des extensions disponibles, vous pouvez utiliser l’opération [Liste des versions d’extension des ressources](https://msdn.microsoft.com/library/dn495440.aspx).

##Ajout, mise à jour ou désactivation des extensions

Des extensions peuvent être ajoutées à une instance créée ou déjà exécutée. Des extensions peuvent être mises à jour, désactivées ou supprimées. Vous pouvez effectuer ces actions à l'aide des applets de commande Azure PowerShell ou des opérations de l'API REST de gestion des services. Des paramètres sont requis pour installer et configurer certaines extensions. Des paramètres publics et privés sont pris en charge pour les extensions.


###Azure PowerShell

Le moyen le plus simple pour ajouter et mettre à jour des extensions consiste à utiliser les applets de commande Azure PowerShell. Lorsque vous utilisez les applets de commande de l'extension, la majeure partie de la configuration de l'extension est effectuée pour vous. Il peut arriver que vous deviez ajouter une extension par programmation. Le cas échéant, vous devez indiquer la configuration de l'extension.

Vous pouvez utiliser les applets de commande suivantes pour savoir si une extension nécessite une configuration de paramètres publics et privés :

-   Pour les instances de rôles web ou de travail, vous pouvez utiliser l’applet de commande **Get-AzureServiceAvailableExtension**.

-   Pour les instances de machines virtuelles, vous pouvez utiliser l’applet de commande **Get-AzureVMAvailableExtension**.

###API REST de gestion des services

Lorsque vous récupérez une liste d'extensions disponibles à l'aide des API REST, vous recevez des informations sur la procédure de configuration de l'extension. Elles peuvent comporter des informations sur les paramètres représentés par un schéma public et un schéma privé. Les valeurs de paramètres publics sont renvoyées dans des requêtes relatives aux instances. Les valeurs de paramètres privés ne sont pas renvoyées.

Vous pouvez utiliser les API REST suivantes pour savoir si une extension nécessite une configuration de paramètres publics et privés :

-   Pour les instances de rôles web ou de travail, les éléments **PublicConfigurationSchema** et **PrivateConfigurationSchema** contiennent les informations dans la réponse provenant de l’opération [Liste des extensions disponibles](https://msdn.microsoft.com/library/dn169559.aspx).

-   Pour les instances de machines virtuelles, les éléments **PublicConfigurationSchema** et **PrivateConfigurationSchema** contiennent les informations dans la réponse provenant de l’opération [Liste des extensions de ressources](https://msdn.microsoft.com/library/dn495441.aspx).

>[AZURE.NOTE]Des extensions peuvent également utiliser des configurations définies avec JSON. En cas d’utilisation de ces types d’extension, seul l’élément **SampleConfig** est utilisé.

<!---HONumber=AcomDC_1217_2015-->