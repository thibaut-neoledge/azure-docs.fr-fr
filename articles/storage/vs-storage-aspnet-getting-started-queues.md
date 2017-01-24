---
title: "Prise en main du Stockage File d’attente Azure et des services connectés de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Comment prendre en main le Stockage File d’attente Azure dans un projet ASP.NET dans Visual Studio après s’être connecté à un compte de stockage à l’aide des services connectés de Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f58c2b522f81dc2dc86f0d2c6bc4872504cf7377
ms.openlocfilehash: 70875287e79aaf49e1b8802cf2953cd5381b97f4


---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Prise en main du Stockage File d’attente Azure et des services connectés de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Le Stockage File d’attente Azure est un service permettant de stocker de grandes quantités de données non structurées, accessibles depuis n’importe où dans le monde via HTTP ou HTTPS. Un message de file d’attente peut avoir une taille de 64 Ko maximum, et une file d’attente peut contenir un nombre illimité de messages, jusqu’à la limite de capacité totale d’un compte de stockage.

Cet article explique comment gérer par programmation les entités du Stockage File d’attente Azure, effectuer des tâches courantes telles que la création d’une file d’attente Azure et l’ajout, la modification, la lecture et la suppression des messages de file d’attente.

> [!NOTE]
> 
> Les sections de code du présent article supposent que vous vous êtes connecté à un compte de stockage Azure à l’aide des services connectés. Pour configurer ces services, démarrez l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur le projet et, dans le menu contextuel, sélectionnez l’option **Ajouter -> Service connecté**. À partir de là, suivez les instructions de la boîte de dialogue pour vous connecter au compte de stockage Azure souhaité.      

## <a name="create-a-queue"></a>Création d’une file d’attente

Les étapes suivantes expliquent comment créer une file d’attente par programmation. Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur.

1. Ajoutez les directives *using* suivantes :
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenez un objet **CloudQueue** représentant une référence au nom de la file d’attente souhaitée. (Remplacez la valeur *<nom-file_attente>* par le nom de la file d’attente que vous souhaitez créer.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Appelez la méthode **CloudQueue.CreateIfNotExists** pour créer la file d’attente, le cas échéant. 

        queue.CreateIfNotExists();


## <a name="add-a-message-to-a-queue"></a>Ajout d'un message à une file d'attente

Les étapes suivantes expliquent comment ajouter par programmation un message à une file d’attente. Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur.

1. Ajoutez les directives *using* suivantes :
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenez un objet **CloudQueue** représentant une référence au nom de la file d’attente souhaitée. (Remplacez la valeur * <nom-file_attente> * par le nom de la file d’attente à laquelle ajouter le message.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Créez l’objet **CloudQueueMessage** représentant le message que vous souhaitez ajouter à la file d’attente. Un objet **CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets. (Remplacez la valeur *<message-file_attente>* par le message que vous voulez ajouter.)

        CloudQueueMessage message = new CloudQueueMessage(<queue-message>);

6. Appelez la méthode **CloudQueue.AddMessage** pour ajouter le message à la file d’attente.

        queue.AddMessage(message);

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Lire un message depuis une file d’attente, sans le supprimer

Les étapes suivantes illustrent comment lire rapidement un message mis en file d’attente (c’est-à-dire lire le premier message sans le supprimer), par programmation. Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur. 

1. Ajoutez les directives *using* suivantes :
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenez un objet **CloudQueue** représentant une référence à la file d’attente. (Remplacez la valeur * <nom-file_attente> * par le nom de la file d’attente à partir de laquelle lire un message.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Appelez la méthode **CloudQueue.PeekMessage** pour lire le message situé en haut de la file d’attente, sans le supprimer.

        CloudQueueMessage message = queue.PeekMessage();

6. Accédez à la valeur de l’objet **CloudQueueMessage** en utilisant la propriété **CloudQueueMessage.AsBytes** ou **CloudQueueMessage.AsString**.

        string messageAsString = message.AsString;
        byte[] messageAsBytes = message.AsBytes;

## <a name="read-and-remove-a-message-from-a-queue"></a>Lire et supprimer un message dans une file d’attente

Les étapes suivantes expliquent comment lire par programmation un message mis en file d’attente, puis le supprimer. Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur. 

1. Ajoutez les directives *using* suivantes :
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenez un objet **CloudQueue** représentant une référence à la file d’attente. (Remplacez la valeur * <nom-file_attente> * par le nom de la file d’attente à partir de laquelle lire un message.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Appelez la méthode **CloudQueue.GetMessage** pour lire le premier message de la file d’attente. La méthode **CloudQueue.GetMessage** rend le message invisible pendant 30 secondes (par défaut) pour tout autre code lisant les messages, afin qu’aucun autre code ne puisse modifier ou supprimer ce message pendant le traitement. Pour modifier la durée d’invisibilité d’un message, changez la valeur du paramètre **visibilityTimeout** passé à la méthode **CloudQueue.GetMessage**.

        // This message will be invisible to other code for 30 seconds.
        CloudQueueMessage message = queue.GetMessage();     

6. Appelez la méthode **CloudQueueMessage.Delete** pour supprimer le message dans la file d’attente.

        queue.DeleteMessage(message);

## <a name="get-the-queue-length"></a>Obtention de la longueur de la file d'attente

Les étapes suivantes indiquent comment obtenir par programmation la longueur de la file d’attente (c’est-à-dire le nombre de messages). Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur. 

1. Ajoutez les directives *using* suivantes :
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenez un objet **CloudQueue** représentant une référence à la file d’attente. (Remplacez la valeur * <nom-file_attente> * par le nom de la file d’attente dont vous voulez connaître la longueur.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Appelez la méthode **CloudQueue.FetchAttributes** pour récupérer les attributs de la file d’attente (y compris sa longueur). 

        queue.FetchAttributes();

6. Accédez à la propriété **CloudQueue.ApproximateMessageCount** pour obtenir la longueur de la file d’attente.
 
        int? nMessages = queue.ApproximateMessageCount;

## <a name="delete-a-queue"></a>Suppression d'une file d'attente
Les étapes suivantes expliquent comment supprimer une file d’attente par programmation. 

1. Ajoutez les directives *using* suivantes :
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenez un objet **CloudQueue** représentant une référence à la file d’attente. (Remplacez la valeur * <nom-file_attente> * par le nom de la file d’attente dont vous voulez connaître la longueur.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Appelez la méthode **CloudQueue.Delete** pour supprimer la file d’attente représentée par l’objet **CloudQueue**.

        messageQueue.Delete();

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]




<!--HONumber=Dec16_HO2-->


