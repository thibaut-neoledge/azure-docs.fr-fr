<properties
   pageTitle="Présentation de la configuration d’état souhaité pour Azure | Microsoft Azure"
   description="Présentation associée à l’utilisation du gestionnaire d’extensions Microsoft Azure pour la configuration d’état souhaité Powershell. Cela inclut les conditions préalables, l’architecture, les applets de commande, etc."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="01/25/2016"
   ms.author="zachal"/>

# Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

L’agent de machine virtuelle Microsoft Azure et les extensions associées font partie des services d’infrastructure Microsoft Azure. Les extensions de machine virtuelle sont des composants logiciels qui étendent les fonctionnalités d’une machine virtuelle et simplifient les diverses opérations de gestion qui la concernent. Par exemple, l’extension VMAccess peut être utilisée pour réinitialiser le mot de passe d’une machine virtuelle. Quant à l’extension Custom Script, elle peut permettre d’exécuter un script sur la machine virtuelle.

Cet article présente l’extension de configuration d’état souhaité Microsoft PowerShell (PowerShell DSC, Desired State Configuration) pour les machines virtuelles Azure, dans le cadre du Kit de développement logiciel (SDK) Azure PowerShell. Vous pouvez utiliser les nouvelles applets de commande pour charger et appliquer une configuration PowerShell DSC sur une machine virtuelle Azure activée avec l’extension PowerShell DSC. L’extension PowerShell DSC procède à l’appel de PowerShell DSC afin que cette dernière mette en œuvre la configuration DSC reçue sur la machine virtuelle. Cette fonctionnalité est également disponible via l’interface utilisateur du portail en version préliminaire.

## Composants requis ##
**Ordinateur local** Pour pouvoir interagir avec l’extension de machine virtuelle Azure, vous devez recourir à l’interface utilisateur du portail en version préliminaire ou au Kit de développement logiciel (SDK) Azure PowerShell.

**Agent invité** La machine virtuelle Azure qui sera définie par la configuration DSC doit inclure un système d’exploitation prenant en charge Windows Management Framework (WMF) 4.0 ou 5.0. Vous trouverez la liste complète des versions de système d’exploitation prises en charge dans l’historique des versions d’extensions DSC.

## Termes et concepts ##
Ce guide part du principe que vous connaissez les concepts suivants :

Configuration : document de configuration DSC.

Nœud : cible d’une configuration DSC. Dans ce document, le terme « nœud » fera toujours référence à une machine virtuelle Azure.

Données de configuration : fichier .psd1 contenant les données d’environnement d’une configuration.

## Présentation de l’architecture ##

L’extension DSC de Microsoft Azure utilise l’infrastructure d’agent Azure pour fournir, mettre en œuvre et créer des rapports sur les configurations DSC exécutées sur des machines virtuelles Azure. L’extension DSC attend un fichier .zip contenant au moins un document de configuration et un ensemble de paramètres, fournis par le biais du Kit de développement logiciel (SDK) Azure PowerShell ou via l’interface utilisateur du portail.

Lorsque l’extension est appelée pour la première fois, elle exécute un processus d’installation. Ce processus installe une version du logiciel Windows Management Framework (WMF), tel que défini ci-dessous :

1. Si le système d’exploitation de la machine virtuelle Azure est Windows Server 2016, aucune action n’est effectuée. En effet, la dernière version de PowerShell est installée sur ce système d’exploitation.
2. Si la propriété `wmfVersion` est spécifiée, cette version du logiciel WMF est installée, sauf si elle n’est pas compatible avec le système d’exploitation de la machine virtuelle.
3. Si aucune propriété `wmfVersion` n’est spécifiée, la dernière version applicable du logiciel WMF est installée.

L’installation de WMF nécessite un redémarrage. Après le redémarrage, l’extension télécharge le fichier .zip spécifié dans la propriété `modulesUrl`. Si cet emplacement figure dans le stockage Azure Blob Storage, un jeton SAP peut être spécifié dans la propriété `sasToken` pour l’accès au fichier. Une fois le fichier .zip téléchargé et décompressé, la fonction de la configuration définie dans l’élément `configurationFunction` est exécutée pour générer le fichier .MOF. L’extension exécute ensuite la commande `Start-DscConfiguration -force` sur le fichier MOF généré. L’extension capture la sortie et la réécrit dans le canal d’état Azure. À ce stade, le gestionnaire de configuration local DSC gère la surveillance et la correction de la manière habituelle.

## Applets de commande PowerShell ##

Les applets de commande PowerShell peuvent être utilisées avec ARM ou ASM afin d’empaqueter, de publier et de surveiller les déploiements d’extensions DSC. Les applets de commande suivantes correspondent aux modules ASM, mais vous pouvez remplacer « Azure » par « AzureRM » pour utiliser le modèle ARM. Par exemple, l’élément `Publish-AzureVMDscConfiguration` utilise ASM, alors que `Publish-AzureRmVMDscConfiguration` utilise ARM.

Le paramètre `Publish-AzureVMDscConfiguration` récupère un fichier de configuration, l’analyse à la recherche de ressources DSC dépendantes et crée un fichier .zip contenant la configuration et les ressources DSC nécessaires pour mettre en œuvre la configuration. Il peut créer le package en local, à l’aide du paramètre `-ConfigurationArchivePath`. Dans le cas contraire, il publie le fichier .zip sur le stockage Azure Blob Storage et le sécurise avec un jeton SAP.

Le fichier .zip créé par cette applet de commande inclut le script de configuration .ps1, à la racine du dossier d’archivage. Pour les ressources, le dossier du module est placé dans le dossier d’archivage.

L’élément `Set-AzureVMDscExtension` injecte les paramètres requis par l’extension PowerShell DSC dans un objet de configuration de machine virtuelle, qui peut ensuite être appliqué à une machine virtuelle Azure avec le paramètre `Update-AzureVm`.

`Get-AzureVMDscExtension` récupère l’état de l’extension DSC d’une machine virtuelle spécifique.

`Get-AzureVMDscExtensionStatus` récupère l’état de la configuration DSC mise en œuvre par le gestionnaire d’extensions DSC sur une machine virtuelle ou un groupe de machines virtuelles.

`Remove-AzureVMDscExtension` supprime le gestionnaire d’extensions d’une machine virtuelle donnée. Cette opération ne supprime **pas** la configuration ; elle ne désinstalle pas non plus le logiciel WWF et ne modifie pas les paramètres appliqués à la machine virtuelle. Elle ne fait que supprimer le gestionnaire d’extensions.

**Principales différences entre les applets de commande ASM et ARM**

- Les applets de commande ARM sont synchrones, alors que les applets de commande ARM sont asynchrones.
- Les éléments ResourceGroupName, VMName, ArchiveStorageAccountName, Version et Location sont tous de nouveaux paramètres requis.
- L’élément ArchiveResourceGroupName est un nouveau paramètre facultatif pour ARM. Vous pouvez le spécifier lorsque votre compte de stockage appartient à un groupe de ressources différent de celui dans lequel la machine virtuelle est créée.
- L’élément ConfigurationArchive est appelé ArchiveBlobName dans ARM.
- L’élément ContainerName est appelé ArchiveContainerName dans ARM.
- L’élément StorageEndpointSuffix est appelé ArchiveStorageEndpointSuffix dans ARM.
- Le commutateur -AutoUpdate a été ajouté dans ARM pour activer la mise à jour automatique du gestionnaire d’extensions vers la dernière version, dès qu’elle est disponible. Remarque : cette action peut entraîner des redémarrages sur la machine virtuelle, lorsqu’une nouvelle version du logiciel WMF est lancée. 


## Fonctionnalités du portail en version préliminaire ##
Accédez à une machine virtuelle. Sous Paramètres -> Gérer, cliquez sur « Extensions ». Un volet est créé. Cliquez sur « Ajouter » et sélectionnez PowerShell DSC.

Ce portail nécessite la saisie de données. **Script ou modules de configuration** : il s’agit d’un champ obligatoire. Il nécessite un fichier .ps1 contenant un script de configuration, ou un fichier .zip présentant un script de configuration .ps1 à la racine, ainsi que des dossiers de modules regroupant toutes les ressources dépendantes. Cet élément peut être créé avec l’applet de commande `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` incluse dans le Kit de développement logiciel (SDK) Azure PowerShell. Le fichier zip sera chargé dans votre stockage Blob Storage utilisateur, sécurisé via un jeton SAP.

**Fichier PSD1 de données de configuration** : ce champ est facultatif. Si votre configuration nécessite un fichier de données de configuration dans .psd1, utilisez ce champ pour le sélectionner et le charger sur votre stockage Blob Storage utilisateur, où il sera sécurisé par un jeton SAP.
 
**Nom qualifié de module de la configuration** : les fichiers .ps1 peuvent avoir plusieurs fonctions de configuration. Entrez le nom du script .ps1 de configuration suivi d’un signe «''» et du nom de la fonction de configuration (voir exemple).

**Arguments de configuration** : si la fonction de configuration accepte des arguments, entrez-les ici, au format `argumentName1=value1,argumentName2=value2`. Remarque : il s’agit d’un format d’argument de configuration différent de celui qui est accepté via les applets de commande PowerShell ou les modèles ARM.

## Prise en main ##

L’extension Azure DSC récupère les documents de configuration DSC et les met en œuvre sur des machines virtuelles Azure. Voici un exemple simple de configuration. Enregistrez-le en local, sous le nom « IisInstall.ps1 » :

```powershell
configuration IISInstall 
{ 
    node ("localhost") 
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Les étapes suivantes permettent ensuite de placer le script IisInstall.ps1 sur la machine virtuelle spécifiée, d’exécuter la configuration et de créer des rapports sur l’état.
 
```powershell
#Requires Azure Powershell cmdlets
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -vm $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -verbose

#Azure Powershell batches commands until an update-AzureVM command is given
$demoVM | Update-AzureVM -Verbose

#check on status
get-azurevmdscextensionstatus -VM $demovm -verbose
```

## Journalisation ##

Les journaux sont placés dans le répertoire suivant :

C:\\WindowsAzure\\Logs\\Plugins\\Microsoft.Powershell.DSC[Numéro Version]

## Étapes suivantes ##

Pour en savoir plus sur DSC PowerShell, [visitez le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

Pour accéder aux fonctionnalités supplémentaires que vous pouvez gérer avec DSC PowerShell, [parcourez la galerie PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0), afin d’obtenir plus de ressources DSC.

Pour en savoir plus sur le passage de paramètres sensibles dans des configurations, voir [Manage credentials securely with the DSC extension handler](virtual-machines-windows-extensions-dsc-credentials.md) (Gérer les identifiants en toute sécurité avec le gestionnaire d’extensions DSC).

<!---HONumber=AcomDC_0323_2016-->