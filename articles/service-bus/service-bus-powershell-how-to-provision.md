<properties
	pageTitle="Gestion de Service Bus avec PowerShell | Microsoft Azure"
	description="Gérer Service Bus avec des scripts PowerShell"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="sethm"/>

# Gestion de Service Bus avec PowerShell

## Vue d'ensemble

Microsoft Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cet article décrit comment utiliser PowerShell pour approvisionner et gérer des entités de Service Bus, telles que les espaces de noms, les files d'attente et les hubs d'événements, à l'aide d'une console locale Azure PowerShell.

## Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][], les [offres spéciales membres][] ou la [version d'évaluation gratuite][].

- Un ordinateur sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][].

- Des connaissances générales sur les scripts PowerShell, les packages NuGet et .NET Framework.

## Ajout d'une référence à l'assembly .NET pour Service Bus

Il existe un nombre limité d'applets de commande PowerShell disponibles pour gérer Service Bus. Pour approvisionner les entités qui ne sont pas exposées via les applets de commande existantes, vous pouvez utiliser le client .NET pour Service Bus dans le [package NuGet Service Bus].

Tout d'abord, vérifiez que le script trouve bien l'assembly **Microsoft.ServiceBus.dll**, qui est installé avec le package NuGet. Pour plus de flexibilité, le script effectue ces étapes :

1. Détermine le chemin d'accès à partir duquel il a été appelé.
2. Parcourt le chemin d'accès jusqu'au dossier nommé `packages`. Ce dossier est créé lorsque vous installez les packages NuGet.
3. Dans le dossier `packages`, il effectue une recherche récursive pour trouver l'assembly **Microsoft.ServiceBus.dll**.
4. Référence l'assembly pour rendre les types disponibles pour une utilisation ultérieure.

Voici comment ces étapes sont implémentées dans un script PowerShell :

```
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

## Approvisionnement d'un espace de noms Service Bus

Deux applets de commande PowerShell prennent en charge les opérations d'espace de noms Service Bus. Au lieu d’utiliser les API du Kit de développement logiciel (SDK) .NET, vous pouvez utiliser [Get-AzureSBNamespace][] et [New-AzureSBNamespace][].

Cet exemple crée quelques variables locales dans le script ; `$Namespace` et `$Location`.

- `$Namespace` est le nom de l'espace de noms Service Bus que nous allons utiliser.
- `$Location` identifie le centre de données dans lequel le script approvisionne l'espace de noms.
- `$CurrentNamespace` stocke l'espace de noms de référence récupéré (ou créé) par le script.

Dans un script réel, les variables `$Namespace` et `$Location` peuvent être transmises en tant que paramètres.

Cette partie du script effectue les opérations suivantes :

1. Le script essaie de récupérer un espace de noms Service Bus du nom indiqué.
2. S'il trouve l'espace de noms recherché, il signale qu'il l'a trouvé.
3. S'il ne trouve pas l'espace de noms recherché, il le crée, puis il récupère le nouvel espace de noms.

	```
	$Namespace = "MyServiceBusNS"
	$Location = "West US"
	
	# Query to see if the namespace currently exists
	$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	
	# Check if the namespace already exists or needs to be created
	if ($CurrentNamespace)
	{
	    Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
	}
	else
	{
	    Write-Host "The [$Namespace] namespace does not exist."
	    Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
	    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
	    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
	}
	```

Pour approvisionner d’autres entités Service Bus, créez une instance de la classe [NamespaceManager][] à partir du Kit de développement logiciel (SDK). Vous pouvez utiliser l'applet de commande [Get-AzureSBAuthorizationRule][] pour récupérer une règle d'autorisation servant à fournir une chaîne de connexion. Nous allons stocker une référence à l’instance `NamespaceManager` dans la variable `$NamespaceManager`. Nous utiliserons `$NamespaceManager` plus tard dans le script pour approvisionner d’autres entités.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## Approvisionnement d'autres entités de Service Bus

Pour approvisionner d’autres entités, telles que des files d’attente, des rubriques et des hubs d’événements, utilisez l’[API .NET pour Service Bus][]. Cet article traite uniquement les hubs d'événements, mais les étapes à suivre pour les autres entités sont similaires. Vous trouverez également d'autres exemples détaillés, notamment pour les autres entités, à la fin de cet article.

Cette partie du script crée quatre variables locales supplémentaires. Ces variables permettent d'instancier un objet `EventHubDescription`. Le script effectue les opérations suivantes :

1. À l'aide de l'objet `NamespaceManager`, vérifiez si le concentrateur d'événements identifié par `$Path` existe.
2. S'il n'existe pas, créez une `EventHubDescription` et transmettez-la à la classe `NamespaceManager` de la méthode `CreateEventHubIfNotExists`.
3. Après avoir déterminé que le hub d'événements est disponible, créez un groupe de consommateurs avec `ConsumerGroupDescription` et `NamespaceManager`.

	```
	$Path  = "MyEventHub"
	$PartitionCount = 12
	$MessageRetentionInDays = 7
	$UserMetadata = $null
	$ConsumerGroupName = "MyConsumerGroup"
		
	# Check to see if the Event Hub already exists
	if ($NamespaceManager.EventHubExists($Path))
	{
	    Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
	}
	else
	{
	    Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
	    $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
	    $EventHubDescription.PartitionCount = $PartitionCount
	    $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
	    $EventHubDescription.UserMetadata = $UserMetadata
	    $EventHubDescription.Path = $Path
	    $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
	    Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
	}
		
	# Create the consumer group if it doesn't exist
	Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
	$ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
	$ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
	$NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
	Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
	```

## Migrer un espace de noms vers un autre abonnement Azure

La séquence de commandes suivante déplace un espace de noms d'un abonnement Azure vers un autre. Pour exécuter cette opération, l'espace de noms doit être déjà actif, et l'utilisateur qui exécute les commandes PowerShell doit être administrateur des abonnements à la fois source et cible.

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRP' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRP' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## Étapes suivantes

Cet article fournit une présentation générale de l'approvisionnement des entités de Service Bus à l'aide de PowerShell. Toutes les opérations que vous pouvez effectuer en utilisant les bibliothèques clientes .NET sont également réalisables au moyen d'un script PowerShell.

Vous trouverez d’autres exemples détaillés dans ces billets de blog :

- [Comment créer des files d'attente, des rubriques et des abonnements Service Bus à l'aide d'un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Comment créer un espace de noms et un concentrateur d'événements Service Bus à l'aide d'un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Vous pouvez également télécharger des scripts prêts à l’emploi :
- [Scripts PowerShell pour Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[formules d'abonnement]: http://azure.microsoft.com/pricing/purchase-options/
[offres spéciales membres]: http://azure.microsoft.com/pricing/member-offers/
[version d'évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[Installation et configuration d'Azure PowerShell]: ../powershell-install-configure.md
[package NuGet Service Bus]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET pour Service Bus]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

<!---HONumber=AcomDC_0504_2016-->