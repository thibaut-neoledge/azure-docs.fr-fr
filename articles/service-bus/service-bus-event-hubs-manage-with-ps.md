<properties
   pageTitle="Utilisation de PowerShell pour gérer des ressources Service Bus et des concentrateurs d'événements"
   description="Utilisation de PowerShell pour créer et gérer des ressources Service Bus et des concentrateurs d'événements"
   services="service-bus"
   documentationCenter=".NET"
   authors="sethmanheim"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="sethm"/>

# Utilisation de PowerShell pour gérer des ressources Service Bus et des concentrateurs d'événements

Microsoft Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion des services Azure. Cet article décrit comment utiliser PowerShell pour approvisionner et gérer des entités de Service Bus, telles que les espaces de noms, les files d'attente et les concentrateurs d'événements, à l'aide d'une console locale Azure PowerShell.

## Conditions préalables

Avant de débuter, vous avez besoin des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][], les [offres spéciales membres][] ou la [version d'évaluation gratuite][].

- Un ordinateur sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][].

- Des connaissances générales sur les scripts PowerShell, les packages NuGet et .NET Framework.

## Ajout d'une référence à .NET Assembly pour Service Bus

Il existe un nombre limité d'applets de commande PowerShell disponibles pour gérer Service Bus. Pour approvisionner les entités qui ne sont pas exposées via les applets de commande existantes, vous pouvez utiliser le client .NET pour Service Bus à partir de PowerShell en faisant référence au [package NuGet Service Bus].

Tout d'abord, vérifiez que le script trouve bien l'assembly **Microsoft.ServiceBus.dll**, qui est installé avec le package NuGet. Pour plus de flexibilité, le script effectue ces étapes :

1. Détermine le chemin d'accès à partir duquel il a été appelé.
2. Parcourt le chemin d'accès jusqu'au dossier nommé `packages`. Ce dossier est créé lorsque vous installez les packages NuGet.
3. Dans le dossier `packages`, il effectue une recherche récursive pour trouver l'assembly **Microsoft.ServiceBus.dll**.
4. Référence l'assembly pour rendre les types disponibles pour une utilisation ultérieure.

Voici comment ces étapes sont implémentées dans un script PowerShell :

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
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

Lorsque vous utilisez des espaces de noms Service Bus, il existe deux applets de commande que vous pouvez utiliser au lieu du SDK .NET : [Get-AzureSBNamespace] et [New-AzureSBNamespace].

Cet exemple crée quelques variables locales dans le script ; `$Namespace` et `$Location`.

- `$Namespace` est le nom de l'espace de noms Service Bus que nous allons utiliser.
- `$Location` identifie le centre de données dans lequel nous allons approvisionner l'espace de noms.
- `$CurrentNamespace` stocke l'espace de noms de référence récupéré (ou créé).

Dans un script réel, les variables `$Namespace` et `$Location` peuvent être transmises en tant que paramètres.

Cette partie du script effectue les opérations suivantes :

1. Le script essaie de récupérer un espace de noms Service Bus du nom indiqué.
2. S'il trouve l'espace de noms recherché, il signale qu'il l'a trouvé.
3. S'il ne trouve pas l'espace de noms recherché, il le crée, puis il récupère le nouvel espace de noms.

	``` powershell

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
	    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
	    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
	}
	```
Pour approvisionner d'autres entités Service Bus, créez une instance de l'objet `NamespaceManager` à partir du Kit de développement logiciel (SDK). Vous pouvez utiliser l'applet de commande [Get-AzureSBAuthorizationRule] pour récupérer une règle d'autorisation servant à fournir une chaîne de connexion. Cet exemple stocke une référence à l'instance `NamespaceManager` dans la variable `$NamespaceManager`. Le script utilise ensuite `$NamespaceManager` pour approvisionner d'autres entités.

	``` powershell
	$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
	# Create the NamespaceManager object to create the Event Hub
	Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
	$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
	Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
	```

## Approvisionnement d'autres entités de Service Bus

Pour approvisionner d'autres entités, telles que des files d'attente, des rubriques et des concentrateurs d'événements, vous pouvez utiliser l'[API .NET pour Service Bus]. Vous trouverez d'autres exemples détaillés, notamment pour les autres entités, à la fin de cet article.

### Création d’un concentrateur d’événements

Cette partie du script crée quatre variables locales supplémentaires. Ces variables permettent d'instancier un objet `EventHubDescription`. Le script effectue les opérations suivantes :

1. À l'aide de l'objet `NamespaceManager`, vérifiez si le concentrateur d'événements identifié par `$Path` existe.
2. S'il n'existe pas, créez une `EventHubDescription` et transmettez-la à la classe `NamespaceManager` de la méthode `CreateEventHubIfNotExists`.
3. Après avoir déterminé que le concentrateur d'événements est disponible, créez un groupe de consommateurs avec `ConsumerGroupDescription` et `NamespaceManager`.

	``` powershell

	$Path  = "MyEventHub"
	$PartitionCount = 12
	$MessageRetentionInDays = 7
	$UserMetadata = $null
	$ConsumerGroupName = "MyConsumerGroup"

	# Check if the Event Hub already exists
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

### Création d'une file d'attente

Pour créer une file d'attente ou une rubrique, effectuez une vérification de l'espace de noms dans la section précédente.

	if ($NamespaceManager.QueueExists($Path))
	{
	    Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
	}
	else
	{
	    Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
	    $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
	    $QueueDescription.EnableExpress = $EnableExpress
	    $QueueDescription.EnablePartitioning = $EnablePartitioning
	    $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
	    $QueueDescription.ForwardTo = $ForwardTo
	    $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    if ($LockDuration -gt 0)
	    {
	        $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
	    }
	    $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
	    $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    $QueueDescription.RequiresSession = $RequiresSession
	    if ($EnablePartitioning)
	    {
	        $QueueDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $QueueDescription.SupportOrdering = $SupportOrdering
	    }
	    $QueueDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateQueue($QueueDescription);
	}

### Création d'une rubrique

	if ($NamespaceManager.TopicExists($Path))
	{
	    Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
	}
	else
	{
	    Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
	    $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $TopicDescription.EnableExpress = $EnableExpress
	    $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
	    $TopicDescription.EnablePartitioning = $EnablePartitioning
	    $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    if ($EnablePartitioning)
	    {
	        $TopicDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $TopicDescription.SupportOrdering = $SupportOrdering
	    }
	    $TopicDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateTopic($TopicDescription);
	}

## Étapes suivantes

Cet article comporte une présentation générale de l’approvisionnement des entités de Service Bus à l’aide de PowerShell. Bien qu'il existe un nombre limité d'applets de commande PowerShell disponibles pour gérer les entités de messagerie Service Bus, en référençant l'assembly Microsoft.ServiceBus.dll, pratiquement toutes les opérations que vous pouvez effectuer à l'aide des bibliothèques clientes .NET peuvent également être effectuées dans un script PowerShell.

Vous trouverez d’autres exemples détaillés dans ces billets de blog :

- [Comment créer des files d'attente, des rubriques et des abonnements Service Bus à l'aide d'un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Comment créer un espace de noms et un concentrateur d'événements Service Bus à l'aide d'un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Vous pouvez également télécharger des scripts prêts à l’emploi :

- [Scripts PowerShell pour Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[formules d'abonnement]: http://azure.microsoft.com/pricing/purchase-options/
[offres spéciales membres]: http://azure.microsoft.com/pricing/member-offers/
[version d'évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[package NuGet Service Bus]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET pour Service Bus]: https://msdn.microsoft.com/fr-FR/library/azure/mt419900.aspx
[Installation et configuration d'Azure PowerShell]: ../install-configure-powershell.md

<!---HONumber=AcomDC_1217_2015-->