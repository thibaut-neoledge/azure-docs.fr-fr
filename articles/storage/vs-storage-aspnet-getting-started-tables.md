---
title: "Prise en main du Stockage Table Azure et des services connectés de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Comment prendre en main le Stockage Table Azure dans un projet ASP.NET dans Visual Studio après s’être connecté à un compte de stockage à l’aide des services connectés de Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 07f827e13e5f01a373e69b90e8a5a0c72081acd0
ms.openlocfilehash: 24cfb3217dbadba3a086a8b0251efe5ab0173d1f


---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Prise en main du Stockage Table Azure et des services connectés de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d'ensemble

Le stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles.

Cet article explique comment gérer par programmation les entités du Stockage Table Azure, effectuer des tâches courantes telles que la création et la suppression d’une table, et comment manipuler les entités d’une table. 

> [!NOTE]
> 
> Les sections de code du présent article supposent que vous vous êtes connecté à un compte de stockage Azure à l’aide des services connectés. Pour configurer ces services, démarrez l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur le projet et, dans le menu contextuel, sélectionnez l’option **Ajouter -> Service connecté**. À partir de là, suivez les instructions de la boîte de dialogue pour vous connecter au compte de stockage Azure souhaité.      

## <a name="create-a-table-in-code"></a>Création d'une table dans le code

Les étapes suivantes expliquent comment créer une table par programmation. Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur.

1. Ajoutez les directives *using* suivantes :

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudTableClient** représentant un client du service de Table.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenez un objet **CloudTable** représentant une référence au nom de la table souhaitée. (Remplacez la valeur * <nom-table> * par le nom de la table que vous souhaitez créer.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Appelez la méthode **CloudTable.CreateIfNotExists** pour créer la table, le cas échéant.   
   
        table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Ajout d'une entité à une table

Les étapes suivantes expliquent comment ajouter par programmation une entité à une table. Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur. 

1. Ajoutez les directives *using* suivantes :

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudTableClient** représentant un client du service de Table.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenez un objet **CloudTable** représentant une référence au nom de la table souhaitée. (Remplacez la valeur * <nom-table> * par le nom de la table à laquelle ajouter l’entité.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Pour ajouter une entité à une table, définissez une classe dérivée de **TableEntity**. Le code suivant définit une classe d'entité nommée **CustomerEntity** , utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. Instanciez l’entité.

        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.Email = "Walter@contoso.com";

7. Créez l’objet **TableOperation** qui insère l’entité du client.

        TableOperation insertOperation = TableOperation.Insert(customer1);

8. Exécutez l’insertion en appelant la méthode **CloudTable.Execute**. Vous pouvez vérifier le résultat de cette opération en inspectant la propriété **TableResult.HttpStatusCode**. Un code d’état de 2xx indique que l’action demandée par le client a été correctement traitée. Par exemple, l’aboutissement de l’insertion de nouvelles entités entraîne l’affichage d’un code d’état HTTP de 204, qui indique que l’opération a été correctement traitée et que le serveur n’a renvoyé aucun contenu.

        TableResult result = table.Execute(insertOperation);

        // Inspect result.HttpStatusCode for success/failure.

## <a name="add-a-batch-of-entities-to-a-table"></a>Ajout d’un lot d’entités à une table

Vous pouvez ajouter une entité à une table, ou en ajouter plusieurs au sein d’un lot. Cela réduit le nombre d’allers-retours entre votre code et le service de Table Azure. Les étapes suivantes expliquent comment ajouter par programmation plusieurs entités à une table, via une seule opération. Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur.

1. Ajoutez les directives *using* suivantes :

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudTableClient** représentant un client du service de Table.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenez un objet **CloudTable** représentant une référence au nom de la table souhaitée. (Remplacez la valeur * <nom-table> * par le nom de la table à laquelle ajouter les entités.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Pour ajouter une entité à une table, définissez une classe dérivée de **TableEntity**. Le code suivant définit une classe d'entité nommée **CustomerEntity** , utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. Instanciez les entités.

        CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
        customer1.Email = "Jeff@contoso.com";
    
        CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
        customer2.Email = "Ben@contoso.com";

7. Obtenez un objet **TableBatchOperation**.

        TableBatchOperation batchOperation = new TableBatchOperation();

8. Ajoutez des entités à l’opération d’insertion par lot.

        batchOperation.Insert(customer1);
        batchOperation.Insert(customer2);

9. Exécutez l’insertion par lot en appelant la méthode **CloudTable.ExecuteBatch**. La méthode **CloudTable.ExecuteBatch** renvoie une liste d’objets **TableResult**. Vous pouvez vérifier le résultat de cette insertion par lot en inspectant la propriété **TableResult.HttpStatusCode** de chaque objet **TableResult** dans la liste. Un code d’état de 2xx indique que l’action demandée par le client a été correctement traitée. Par exemple, l’aboutissement de l’insertion de nouvelles entités entraîne l’affichage d’un code d’état HTTP de 204, qui indique que l’opération a été correctement traitée et que le serveur n’a renvoyé aucun contenu.
    
        IList<TableResult> results = table.ExecuteBatch(batchOperation);

        // Inspect the HttpStatusCode property of each TableResult object
        // in the results list for success/failure.

## <a name="get-a-single-entity"></a>Obtention d'une seule entité

Les étapes suivantes indiquent comment obtenir par programmation une entité d’une table. Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur. 

> [!NOTE]
> 
> Le code de cette section fait référence à la classe **CustomerEntity** et aux données présentées dans la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table). 

1. Ajoutez les directives *using* suivantes :

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudTableClient** représentant un client du service de Table.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenez un objet **CloudTable** représentant une référence au nom de la table souhaitée. (Remplacez la valeur * <nom-table> * par le nom de la table à laquelle ajouter les entités.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Créez un objet d’opération de récupération qui prend un objet d’entité dérivé de **TableEntity**. Le premier paramètre est *partitionKey* et le deuxième, *rowKey*. À l’aide de la classe **CustomerEntity** et des données présentées dans la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table), l’extrait de code suivant interroge la table pour obtenir une entité **CustomerEntity** avec une valeur *partitionKey* égale à « Smith » et une valeur *rowKey* égale à « Ben ».  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. Exécutez l’opération de récupération.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. Vérifiez le résultat de l’opération en inspectant la propriété **TableOperation.HttpStatusCode**, sachant qu’un code d’état de 200 indique que l’action demandée par le client a été correctement traitée. Vous pouvez également inspecter la propriété **TableResult.Result** qui contient l’entité renvoyée (si l’opération a réussi).

        CustomerEntity customer = null;

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            // Process the customer entity.
            customer = retrievedResult.Result as CustomerEntity;
        }

## <a name="get-all-entities-in-a-partition"></a>Obtenir toutes les entités d’une partition

Les étapes suivantes indiquent comment obtenir par programmation toutes les entités d’une partition. Dans l’application MVC ASP.NET, le code est inséré dans un contrôleur. 

> [!NOTE]
> 
> Le code de cette section fait référence à la classe **CustomerEntity** et aux données présentées dans la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table). 

1. Ajoutez les directives *using* suivantes :

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudTableClient** représentant un client du service de Table.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenez un objet **CloudTable** représentant une référence au nom de la table souhaitée. (Remplacez la valeur * <nom-table> * par le nom de la table à laquelle ajouter les entités.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Instanciez un objet **TableQuery** spécifiant la requête dans la clause **Where**. À l’aide de la classe **CustomerEntity** et des données présentées dans la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table), l’extrait de code suivant interroge la table pour obtenir toutes les entités pour lesquelles le paramètre **PartitionKey** est égal à « Smith ».

        TableQuery<CustomerEntity> query = 
            new TableQuery<CustomerEntity>()
            .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

6. Dans une boucle, appelez la méthode **CloudTable.ExecuteQuerySegmented** passant l’objet de requête que vous avez instancié à l’étape précédente.  La méthode **CloudTable.ExecuteQuerySegmented** renvoie un objet **TableContinuationToken** qui indique qu’il n’existe plus d’entités à récupérer (lorsque la valeur est **null**). Dans la boucle, utilisez une autre boucle pour effectuer l’itération sur les entités renvoyées.

        TableContinuationToken token = null;
        do
        {
            TableQuerySegment<CustomerEntity>resultSegment = table.ExecuteQuerySegmented(query, token);
            token = resultSegment.ContinuationToken;

            foreach (CustomerEntity customer in resultSegment.Results)
            {
                // Process customer entity.
            }
        } while (token != null);

## <a name="delete-an-entity"></a>Suppression d’une entité

Les étapes suivantes indiquent comment rechercher, puis supprimer une entité.

1. Ajoutez les directives *using* suivantes :

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez la valeur *<nom-compte-stockage>* par le nom du compte de stockage Azure auquel vous accédez.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudTableClient** représentant un client du service de Table.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenez un objet **CloudTable** représentant une référence au nom de la table souhaitée. (Remplacez la valeur * <nom-table> * par le nom de la table à laquelle ajouter les entités.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Créez un objet d’opération de récupération qui prend un objet d’entité dérivé de **TableEntity**. Le premier paramètre est *partitionKey* et le deuxième, *rowKey*. À l’aide de la classe **CustomerEntity** et des données présentées dans la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table), l’extrait de code suivant interroge la table pour obtenir une entité **CustomerEntity** avec une valeur *partitionKey* égale à « Smith » et une valeur *rowKey* égale à « Ben ».  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. Exécutez l’opération de récupération.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. Vérifiez le résultat de l’opération en inspectant la propriété **TableOperation.HttpStatusCode**, sachant qu’un code d’état de 200 indique que l’action demandée par le client a été correctement traitée. Vous pouvez également inspecter la propriété **TableResult.Result** qui contient l’entité renvoyée (si l’opération a réussi). Dans l’instruction conditionnelle permettant de vérifier que l’opération a réussi, créez une opération de suppression (en passant l’entité renvoyée par la requête), puis exécutez-la.

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            CustomerEntity customer = retrievedResult.Result as CustomerEntity;

            // Create the delete operation.
            TableOperation deleteOperation = TableOperation.Delete(customer);

            // Execute the delete operation.
            table.Execute(deleteOperation);
        }

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]




<!--HONumber=Dec16_HO2-->


