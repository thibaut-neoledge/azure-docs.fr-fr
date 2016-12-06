---
title: "Créer une fonction Azure liée à un service Azure | Microsoft Docs"
description: "Créez une fonction Azure, une application sans serveur, qui interagit avec d&quot;autres services Azure."
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: a0a46708645be91f89b0a6ae9059468bc84aeb11
ms.openlocfilehash: c6905452951910d3c62bc5152741a8ead26196ef


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>Créer une fonction Azure liée à un service Azure
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

Dans cette vidéo, vous apprenez à créer une fonction Azure qui écoute les messages dans une file d'attente Azure et copie les messages dans un objet blob Azure.

## <a name="watch-the-video"></a>Regarder la vidéo
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>Création d’une fonction de déclenchement d’une file d’attente d’entrée
L’objectif de cette fonction consiste à écrire un message dans une file d’attente toutes les 10 secondes. Pour ce faire, vous devez créer les files d’attente de fonctions et de messages et ajouter le code pour écrire des messages dans les files d’attente nouvellement créées.

1. Accédez au portail Azure et recherchez votre application de fonction Azure.
2. Cliquez sur **Nouvelle fonction** > **TimerTrigger - Nœud**. Nommez la fonction **FunctionsBindingsDemo1**
3. Entrez une valeur « 0/10 * * * * * » pour la planification. Cette valeur a la forme d’une expression cron. Elle planifie une exécution du minuteur toutes les 10 secondes.
4. Cliquez sur le bouton **Créer** pour créer la fonction.
   
    ![Ajout d’un déclencheur des fonctions de minuteur](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. Vérifiez le bon fonctionnement de la fonction en affichant l’activité dans le journal. Il se peut que vous deviez cliquer sur le lien **journaux** dans le coin supérieur droit pour afficher le volet de journal.
   
   ![Vérifiez le bon fonctionnement de la fonction en affichant le journal.](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>Ajout d'un message à une file d'attente
1. Accédez à l’onglet **Intégrer**.
2. Choisissez **Nouvelle sortie** > **File d’attente de stockage Azure** > **Sélectionner**.
3. Entrez **myQueueItem** dans la zone de texte **Nom du paramètre de message**.
4. Sélectionnez un compte de stockage, ou cliquez sur **nouveau** pour en créer un si vous n’en avez pas.
5. Entrez **functions-bindings** dans la zone de texte **Nom de la file d’attente**.
6. Cliquez sur **Enregistrer**.  
   
   ![Ajout d’un déclencheur des fonctions de minuteur](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>Écriture dans la file d’attente
1. Retournez à l’onglet **Développer** et ajoutez le code suivant à la fonction après le code existant :
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. Modifiez le code de fonction existant pour appeler le code ajouté à l’étape 1. Insérez le code suivant autour de la ligne 9 de la fonction, après l’instruction *if*.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    Ce code crée un élément **myQueueItem** et définit sa propriété **time** sur l’horodatage actuel. Il ajoute ensuite le nouvel élément de file d’attente à la liaison myQueue du contexte.
3. Cliquez sur **Enregistrer et exécuter**.
4. Vérifiez que le code fonctionne en affichant la file d’attente dans Visual Studio.
   
   * Ouvrez Visual Studio et accédez à **Vue** > **Cloud** **Explorer**.
   * Recherchez le compte de stockage et la file d’attente **functions-bindings** que vous avez utilisés lors de la création de la file d’attente myQueue. Vous devriez voir des lignes de données de journal. Vous devrez peut-être vous connecter à Azure par le biais de Visual Studio.  

## <a name="create-an-output-queue-trigger-function"></a>Création d’une fonction de déclenchement d’une file d’attente de sortie
1. Cliquez sur **Nouvelle fonction** > **TimerTrigger - C#**. Nommez la fonction **FunctionsBindingsDemo2**. Notez que vous pouvez combiner des langages dans la même application de fonction (nœud et C# dans le cas présent).
2. Entrez **functions-bindings** dans la zone de texte **Nom de la file d’attente**.
3. Sélectionnez un compte de stockage à utiliser ou créez-en un nouveau.
4. Cliquez sur **Créer**
5. Vérifiez le bon fonctionnement de la nouvelle fonction en affichant à la fois le journal de la fonction et Visual Studio pour les mises à jour. Le journal de la fonction indique que la fonction est en cours d’exécution et que des éléments sont enlevés de la file d’attente. Étant donné que la fonction est liée à la file d’attente de sortie **functions-bindings** comme déclencheur d’entrée, l’actualisation de la file d’attente **functions-bindings** dans Visual Studio doit indiquer que les éléments ont disparu. Ils ont été enlevés de la file d’attente.   
   
   ![Ajout d’une file d’attente de sortie des fonctions de minuterie](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>Modification du type d’élément de file d’attente de JSON à l’objet
1. Remplacez le code dans **FunctionsBindingsDemo2** par le code suivant :    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    Ce code ajoute deux classes, **TableItem** et **QItem**, que vous utilisez pour lire et écrire des files d’attente. En outre, la fonction **Run** a été modifiée pour accepter les paramètres **QItem** et **TraceWriter** au lieu d’une **chaîne** et d’un **TraceWriter**. 
2. Cliquez sur le bouton **Enregistrer** .
3. Vérifiez le code fonctionne en vérifiant le journal. Notez que les fonctions Azure sérialisent et désérialisent automatiquement l’objet pour vous, ce qui facilite l’accès à la file d’attente d’une manière orientée objet pour faire circuler les données. 

## <a name="store-messages-in-an-azure-table"></a>Stockage de messages dans une table Azure
Maintenant que vous avez des files d’attente fonctionnant ensemble, il est temps d’ajouter une table Azure pour un stockage permanent des données de file d’attente.

1. Accédez à l’onglet **Intégrer**.
2. Créez une table de stockage Azure pour la sortie et nommez-la **myTable**.
3. Répondez par **functionsbindings** à la question « Dans quelle table les données doivent-elles être écrites ? ».
4. Modifier le paramétrage de **PartitionKey** de **{id de projet}** à **{partition}**.
5. Choisissez un compte de stockage ou créez-en un nouveau.
6. Cliquez sur **Enregistrer**.
7. Accédez à l’onglet **Développer**.
8. Créez une classe **TableItem** pour représenter une table Azure et modifiez la fonction Run afin d’accepter l’objet TableItem nouvellement créé. Notez que vous devez utiliser les propriétés **PartitionKey** et **RowKey** pour que l’opération réussisse.
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. Cliquez sur **Enregistrer**.
10. Vérifiez que le code fonctionne en consultant les journaux de la fonction et dans Visual Studio. Pour vérifier dans Visual Studio, utilisez le **Cloud Explorer** pour accéder à la table Azure **functionbindings** et vérifiez qu’il contient des lignes.

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


