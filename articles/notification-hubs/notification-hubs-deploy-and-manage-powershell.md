<properties 
	pageTitle="Déploiement et gestion des concentrateurs de notification à l'aide de PowerShell" 
	description="Création et gestion des concentrateurs de notification à l'aide de PowerShell pour Automation" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="wesmc"/>

# Déploiement et gestion des concentrateurs de notification à l'aide de PowerShell

##Vue d'ensemble

Cet article vous montre comment utiliser Création et gestion d’Azure Notification Hubs à l'aide de PowerShell. Les tâches d'automatisation courantes suivantes figurent dans cette rubrique.

+ Création d’un concentrateur de notification
+ Définition des informations d’identification

Si vous devez également créer un nouvel espace de noms pour Service Bus pour vos concentrateurs de notification, consultez [Gestion de Service Bus avec PowerShell](../service-bus/service-bus-powershell-how-to-provision.md).

La gestion des concentrateurs de notification n’est pas directement prise en charge par les applets de commande inclus avec Azure PowerShell. Dans PowerShell, la meilleure approche consiste à référencer l’assembly Microsoft.Azure.NotificationHubs.dll. L’assembly est distribué avec le [package NuGet Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## Composants requis

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement], les [offres spéciales membres] ou la [version d'évaluation gratuite].

- Un ordinateur sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell].

- Des connaissances générales sur les scripts PowerShell, les packages NuGet et .NET Framework.


## Ajout d'une référence à l'assembly .NET pour Service Bus

La gestion d’Azure Notification Hubs n’est pas encore incluse avec les applets de commande PowerShell dans Azure PowerShell. Pour approvisionner les hubs de notification, vous pouvez utiliser le client .NET fourni dans le [package NuGet Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Tout d’abord, vérifiez que votre script trouve bien l’assembly **Microsoft.Azure.NotificationHubs.dll**, qui est installé avec le package NuGet dans un projet Visual Studio. Pour plus de flexibilité, le script effectue ces étapes :

1. Détermine le chemin d'accès à partir duquel il a été appelé.
2. Parcourt le chemin d'accès jusqu'au dossier nommé `packages`. Ce dossier est créé lorsque vous installez les packages NuGet pour les projets Visual Studio.
3. Dans le dossier `packages`, il effectue une recherche récursive pour trouver l’assembly **Microsoft.Azure.NotificationHubs.dll**.
4. Référence l'assembly pour rendre les types disponibles pour une utilisation ultérieure.

Voici comment ces étapes sont implémentées dans un script PowerShell :

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## Créez la classe NamespaceManager

Pour approvisionner les hubs de notification, créez une instance de la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) à partir du Kit de développement logiciel (SDK).

Vous pouvez utiliser l'applet de commande [Get-AzureSBAuthorizationRule] inclus avec Azure PowerShell pour récupérer une règle d'autorisation servant à fournir une chaîne de connexion. Nous allons stocker une référence à l'instance `NamespaceManager` dans la variable `$NamespaceManager`. Nous allons utiliser `$NamespaceManager` pour configurer un concentrateur de notification.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## Configuration d’un nouveau concentrateur de notification 

Pour approvisionner un hub de notification, utilisez l’[API .NET pour Notification Hubs].

Dans cette partie du script, nous allons créer quatre variables locales supplémentaires.

1. `$Namespace` : définissez cette variable sur le nom de l’espace de noms dans lequel vous souhaitez créer un hub de notification.
2. `$Path` : définissez cette variable de chemin d’accès sur le nom du nouveau hub de notification. Par exemple, « MonConcentrateur ».
3. `$WnsPackageSid` : définissez cette variable sur le SID de package de votre application Windows à partir du [Centre de développement Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey` : définissez cette variable sur la clé secrète de votre application Windows à partir du [Centre de développement Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Ces variables sont utilisées pour la connexion à votre espace de noms et la création d’un nouveau hub de notification configuré pour gérer les notifications Windows Notification Services (WNS) avec les informations d’identification WNS pour une application Windows. Pour plus d’informations sur l’obtention du SID du package et de la clé secrète, consultez le didacticiel [Prise en main de Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

+ L’extrait de script utilise l’objet `NamespaceManager` pour vérifier que le hub de notification identifié par `$Path` existe.

+ S’il n’existe pas, le script crée un `NotificationHubDescription` avec les informations d’identification WNS et le transmet à la méthode `CreateNotificationHub` de la classe `NamespaceManager`.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## Ressources supplémentaires

- [Gestion de Service Bus avec PowerShell](../service-bus/service-bus-powershell-how-to-provision.md)
- [Comment créer des files d'attente, des rubriques et des abonnements Service Bus à l'aide d'un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Comment créer un espace de noms et un concentrateur d'événements Service Bus à l'aide d'un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Vous pouvez également télécharger des scripts prêts à l’emploi :
- [Scripts PowerShell pour Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[formules d'abonnement]: http://azure.microsoft.com/pricing/purchase-options/
[offres spéciales membres]: http://azure.microsoft.com/pricing/member-offers/
[version d'évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[Installation et configuration d'Azure PowerShell]: ../powershell-install-configure.md
[API .NET pour Notification Hubs]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 

<!---HONumber=AcomDC_0706_2016-->