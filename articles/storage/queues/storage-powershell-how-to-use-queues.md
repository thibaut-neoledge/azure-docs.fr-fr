---
title: "Effectuer des opérations sur Stockage File d’attente Azure avec PowerShell | Microsoft Docs"
description: "Didacticiel : effectuer des opérations sur Stockage File d’attente Azure avec PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 357d8db329a6a3c782753804d681029fdb07b5f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Effectuer des opérations sur Stockage File d’attente Azure avec Azure PowerShell

Stockage File d’attente Azure est un service permettant de stocker un grand nombre de messages accessibles n’importe où dans le monde au moyen d’appels authentifiés avec HTTP ou HTTPS. Pour plus d’informations, consultez [Présentation des files d’attente Azure](storage-queues-introduction.md). Ce didacticiel décrit les opérations courantes liées au stockage de files d’attente. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Création d’une file d’attente
> * Récupérer une file d’attente
> * Ajouter un message
> * Lire un message
> * Supprimer un message 
> * Suppression d'une file d'attente

Ce didacticiel requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Récupérer la liste des régions

Si vous ne savez pas quelle région utiliser, listez celles qui sont disponibles. Dans la liste qui s’affiche, trouvez celle que vous souhaitez utiliser. Ce didacticiel utilise **eastus**. Stockez cette région dans la variable **location** pour une utilisation ultérieure.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Stockez le nom du groupe de ressources dans une variable pour une utilisation ultérieure. Dans cet exemple, un groupe de ressources nommé *howtoqueuesrg* est créé dans la région *eastus*.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Créer un compte de stockage

Créez un compte de stockage standard à usage général avec un stockage localement redondant à l’aide de [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Obtenez le contexte du compte de stockage qui définit le compte de stockage à utiliser. Si un compte de stockage est utilisé, référencez le contexte au lieu d’entrer les informations d’identification à plusieurs reprises.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Création d’une file d’attente

L'exemple suivant établit d'abord une connexion à Azure Storage à l'aide du contexte de compte de stockage, ce qui inclut le nom de compte de stockage et sa clé d'accès. Ensuite, il appelle l’applet de commande [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) pour créer une file d’attente appelée « queuename ».

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Pour plus d’informations sur les conventions d’affectation de noms pour le service de File d’attente Azure, consultez la page [Affectation de noms pour les files d’attente et les métadonnées](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Récupérer une file d’attente

Vous pouvez interroger et récupérer une file d'attente spécifique ou une liste de toutes les files d'attente dans un compte de stockage. Les exemples suivants montrent comment récupérer toutes les files d’attente dans le compte de stockage et une file d’attente spécifique ; les deux commandes utilisent l’applet de commande [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue).

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Ajout d'un message à une file d'attente

Pour ajouter un message à une file d’attente, créez d’abord une instance de la classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Appelez ensuite la méthode [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Un CloudQueueMessage peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets.

L’exemple suivant montre comment ajouter un message à votre file d’attente.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

Si vous utilisez l’[Explorateur Stockage Azure](http://storageexplorer.com), vous pouvez vous connecter à votre compte Azure et afficher les files d’attente dans le compte de stockage, puis en explorer une au niveau du détail pour afficher les messages qu’elle contient. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Lire un message de la file d’attente, puis le supprimer

Les messages sont lus, dans la mesure du possible, dans l’ordre du premier entré, premier sorti. Nous n’offrons aucune garantie. Quand vous lisez le message de la file d’attente, il devient invisible à tous les autres processus ciblant la file d’attente. De cette façon, si votre code ne parvient pas à traiter un message à cause d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer.  

Cette **période d’invisibilité** définit la durée d’invisibilité du message avant qu’il ne redevienne disponible pour traitement. La valeur par défaut est 30 secondes. 

Votre code lit un message dans la file d’attente en deux étapes. Quand vous appelez la méthode [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx), vous obtenez le message suivant dans la file d’attente. Un message renvoyé par **GetMessage** devient invisible par les autres codes lisant les messages de cette file d'attente. Pour finaliser la suppression du message de la file d’attente, vous devez également appeler la méthode [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). 

Dans l’exemple suivant, vous lisez les trois messages de la file d’attente, puis attendez 10 secondes (période d’invisibilité). Ensuite, quand vous relisez les trois messages, vous les supprimez un à un en appelant **DeleteMessage**. Si vous essayez de lire la file d’attente une fois les messages supprimés, $queueMessage retourne NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Suppression d'une file d'attente
Pour supprimer une file d'attente et tous les messages qu'elle contient, exécutez l'applet de commande Remove-AzureStorageQueue. L’exemple suivant montre comment supprimer la file d’attente utilisée dans cet exercice à l’aide de l’applet de commande Remove-AzureStorageQueue.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer tous les composants créés dans cet exercice, supprimez le groupe de ressources. Cette opération supprime également toutes les ressources contenues dans le groupe. Dans le cas présent, le compte de stockage créé et le groupe de ressources sont supprimés.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a présenté les bases de la gestion de Stockage File d’attente avec PowerShell. Vous avez notamment appris à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Création d’une file d’attente
> * Récupérer une file d’attente
> * Ajouter un message
> * Lire le message suivant
> * Supprimer un message 
> * Suppression d'une file d'attente

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Applets de commande Microsoft Azure PowerShell - Stockage
* [Applets de commande PowerShell - Stockage](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorateur Microsoft Azure Storage
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.