<properties 
	pageTitle="Déploiement et gestion des concentrateurs de notification à l'aide de PowerShell" 
	description="Création et gestion des concentrateurs de notification à l'aide de PowerShell pour Automation" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/24/2015" 
	ms.author="wesmc"/>

# Déploiement et gestion des concentrateurs de notification à l'aide de PowerShell

##Vue d'ensemble

Cet article vous montre comment utiliser Création et gestion d’Azure Notification Hubs à l'aide de PowerShell. Les tâches d'automatisation courantes suivantes figurent dans cette rubrique.

+ Création d’un concentrateur de notification
+ Définition des informations d’identification

Si vous devez également créer un nouvel espace de noms pour Service Bus pour vos concentrateurs de notification, consultez [Gestion de Service Bus avec PowerShell](../service-bus/service-bus-powershell-how-to-provision.md).

La gestion des concentrateurs de notification n’est pas directement prise en charge par les applets de commande inclus avec Azure PowerShell. La meilleure approche à partir de PowerShell consiste à référencer l'assembly Microsoft.ServiceBus.dll. L'assembly est distribué avec le [package NuGet Service Bus](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).


## Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement], les [offres spéciales membres] ou la [version d'évaluation gratuite].

- Un ordinateur sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell].

- Des connaissances générales sur les scripts PowerShell, les packages NuGet et .NET Framework.


## Ajout d'une référence à l'assembly .NET pour Service Bus

La gestion d’Azure Notification Hubs n’est pas encore incluse avec les applets de commande PowerShell dans Azure PowerShell. Pour approvisionner les concentrateurs de notification et les autres entités Service Bus qui ne sont pas exposées via les applets de commande existants, vous pouvez utiliser le client .NET pour Service Bus dans le [package NuGet Service Bus](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

Tout d'abord, vérifiez que votre script trouve bien l'assembly **Microsoft.ServiceBus.dll**, qui est installé avec le package NuGet dans un projet Visual Studio. Pour plus de flexibilité, le script effectue ces étapes :

1. Détermine le chemin d'accès à partir duquel il a été appelé.
2. Parcourt le chemin d'accès jusqu'au dossier nommé `packages`. Ce dossier est créé lorsque vous installez les packages NuGet pour les projets Visual Studio.
3. Dans le dossier `packages`, il effectue une recherche récursive pour trouver l'assembly **Microsoft.ServiceBus.dll**.
4. Référence l'assembly pour rendre les types disponibles pour une utilisation ultérieure.

Voici comment ces étapes sont implémentées dans un script PowerShell :

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## Créez la classe NamespaceManager

Pour configurer les concentrateurs de notification et d'autres entités Service Bus, créez une instance de la classe [NamespaceManager](http://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx) dans le Kit de développement.

Vous pouvez utiliser l'applet de commande [Get-AzureSBAuthorizationRule] inclus avec Azure PowerShell pour récupérer une règle d'autorisation servant à fournir une chaîne de connexion. Nous allons stocker une référence à l'instance `NamespaceManager` dans la variable `$NamespaceManager`. Nous allons utiliser `$NamespaceManager` pour configurer un concentrateur de notification.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## Configuration d’un nouveau concentrateur de notification 

Pour configurer un concentrateur de notification, utilisez l’[API .NET pour Service Bus]. Cet article porte uniquement sur les concentrateurs de notification. Pour travailler avec d'autres entités Service Bus, consultez [Gestion de Service Bus avec PowerShell](../service-bus/service-bus-powershell-how-to-provision.md).

Dans cette partie du script, nous allons créer quatre variables locales supplémentaires.

1. `$Namespace` : affectez le nom de l’espace de noms dans lequel vous souhaitez créer un concentrateur de notification.
2. `$Path` : définissez ce chemin d'accès sur le nom du nouveau concentrateur de notification. Par exemple, « MonConcentrateur ».    
3. `$WnsPackageSid` : définissez cette propriété sur le SID du package pour votre application Windows à partir du [Centre de développement Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey` : définissez cette propriété sur la clé secrète pour votre application Windows à partir du [Centre de développement Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Ces variables sont utilisées pour la connexion à votre espace de noms Service Bus et la création d’un nouveau concentrateur de notification configuré pour gérer les notifications Windows Notification Services (WNS) avec les informations d'identification WNS pour une application Windows. Pour plus d'informations sur l'obtention du SID du package et de la clé secrète, consultez le didacticiel [Prise en main de Notification Hubs](notification-hubs-windows-store-dotnet-get-started.md).

+ L'extrait de code de script utilise l’objet `NamespaceManager` pour vérifier que le concentrateur de notification identifié par `$Path` existe.

+ S’il n'existe pas, le script crée un `NotificationHubDescription` avec les informations d'identification WNS et le transmet à la méthode `CreateNotificationHub` de la classe `NamespaceManager`.

``` powershell

$Namespace = "<Enter your namespace>
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.ServiceBus.Notifications.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.ServiceBus.Notifications.NotificationHubDescription -ArgumentList $Path;
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

Vous pouvez également télécharger des scripts prêts à l'emploi : - [Scripts PowerShell Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[formules d'abonnement]: http://azure.microsoft.com/pricing/purchase-options/
[offres spéciales membres]: http://azure.microsoft.com/pricing/member-offers/
[version d'évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[Installation et configuration d'Azure PowerShell]: ../install-configure-powershell.md
[API .NET pour Service Bus]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 

<!---HONumber=Oct15_HO1-->