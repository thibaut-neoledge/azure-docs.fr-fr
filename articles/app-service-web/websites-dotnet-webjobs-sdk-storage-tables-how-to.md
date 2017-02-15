---
title: "Utilisation du stockage de tables Microsoft Azure avec le Kit de développement logiciel (SDK) WebJobs"
description: "Découvrez comment utiliser le stockage de tables Microsoft Azure avec le Kit de développement logiciel (SDK) WebJobs. Créez des tables, ajoutez des entités à des tables et lisez les tables existantes."
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: jimbe
ms.assetid: 451432cc-c780-4310-85d3-84f44fe48afe
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 47db68afb8ea938a9861765f9e60c78436569110


---
# <a name="how-to-use-azure-table-storage-with-the-webjobs-sdk"></a>Utilisation du stockage de tables Microsoft Azure avec le Kit de développement logiciel (SDK) WebJobs
## <a name="overview"></a>Vue d’ensemble
Ce guide fournit des exemples de code C# qui indiquent comment lire et écrire des tables de stockage Azure à l’aide du [Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk.md) version 1.x.

Ce guide suppose que vous savez [comment créer un projet WebJob dans Visual Studio avec des chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md) ou [plusieurs comptes de stockage](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

Certains extraits de code illustrent l’attribut `Table` utilisé dans des fonctions [appelées manuellement](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual), c’est-à-dire sans utiliser l’un des attributs de déclenchement. 

## <a name="a-idingressa-how-to-add-entities-to-a-table"></a><a id="ingress"></a> Ajout d’une entité à une table
Pour ajouter des entités à une table, utilisez l’attribut `Table` avec un paramètre `ICollector<T>` ou `IAsyncCollector<T>`, dans lequel `T` spécifie le schéma des entités que vous souhaitez ajouter. Le constructeur d’attribut prend un paramètre de chaîne qui spécifie le nom de la table. 

L'exemple de code suivant ajoute `Person` entités à une table nommée *Entrée*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() { 
                        PartitionKey = "Test", 
                        RowKey = i.ToString(), 
                        Name = "Name" }
                    );
            }
        }

En général, le type que vous utilisez avec `ICollector` dérive de l’élément `TableEntity` ou implémente `ITableEntity`, mais ce n’est pas obligatoire. L’une ou l’autre des classes `Person` suivantes fonctionne avec le code indiqué dans la méthode `Ingress` précédente.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Si vous souhaitez utiliser directement l’API Microsoft Azure Storage, vous pouvez ajouter un paramètre `CloudStorageAccount` à la signature de méthode.

## <a name="a-idmonitora-real-time-monitoring"></a><a id="monitor"></a> Surveillance en temps réel
Étant donné que les fonctions d’entrée de données traitent souvent des volumes importants de données, le tableau de bord du Kit de développement logiciel (SDK) WebJobs fournit des données d’analyse en temps réel. La section **Journal d’appels** vous signale si la fonction est toujours en cours d’exécution.

![Fonction d’entrée en cours d’exécution](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

La page **Détails des appels** signale la progression de la fonction (c’est-à-dire le nombre d’entités écrites) alors qu’elle s’exécute et vous permet de l’arrêter. 

![Fonction d’entrée en cours d’exécution](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Lorsque l’exécution de la fonction se termine, la page **Détails des appels** indique le nombre de lignes écrites.

![Arrêt de la fonction d’entrée](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a name="a-idmultiplea-how-to-read-multiple-entities-from-a-table"></a><a id="multiple"></a> Lecture de plusieurs entrées à partir d’une table
Pour lire une table, utilisez l’attribut `Table` avec un paramètre `IQueryable<T>`, dans lequel le type `T` dérive de `TableEntity` ou implémente `ITableEntity`.

L’exemple de code suivant lit et enregistre toutes les lignes de la table `Ingress` :

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="a-idreadonea-how-to-read-a-single-entity-from-a-table"></a><a id="readone"></a> Lecture d’une entité unique à partir d’une table
Il existe un constructeur d’attribut `Table` présentant deux paramètres supplémentaires, qui vous permettent de spécifier la clé de partition et la clé de ligne lorsque vous souhaitez effectuer une liaison avec une entité de table unique.

L’exemple de code suivant lit une ligne de table pour une entité `Person` basée sur des valeurs de clé de partition et de clé de ligne reçues dans un message en file d’attente :  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


La classe `Person` figurant dans cet exemple n’est pas obligée d’implémenter `ITableEntity`.

## <a name="a-idstorageapia-how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a><a id="storageapi"></a> Utilisation directe de l’API de stockage .NET pour travailler avec une table
Vous pouvez également utiliser l’attribut `Table` avec un objet `CloudTable`, afin de garantir une utilisation plus souple des tables.

L’exemple de code suivant utilise un objet `CloudTable` pour ajouter une entité unique à la table *Entrée* . 

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Pour en savoir plus sur l’utilisation de l’objet `CloudTable` , voir [Utilisation du stockage de tables à partir de .NET](../storage/storage-dotnet-how-to-use-tables.md). 

## <a name="a-idqueuesarelated-topics-covered-by-the-queues-how-to-article"></a><a id="queues"></a>Sujets connexes traités dans l’article de procédure relatif aux files d’attente
Pour en savoir plus sur la gestion du traitement de tables déclenché par un message en file d’attente, ou pour consulter des scénarios relatifs au Kit de développement logiciel (SDK) WebJobs non spécifiques du traitement des tables, voir [Comment utiliser le stockage de la file d’attente Azure avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Les sujets abordés dans cet article sont les suivants :

* Fonctions asynchrones
* Instances multiples
* Arrêt approprié
* Utilisation des attributs du Kit de développement logiciel (SDK) WebJobs dans le corps d’une fonction
* Définition des chaînes de connexion du SDK dans le code
* Définition des valeurs des paramètres de constructeur du Kit de développement logiciel (SDK) WebJobs dans le code
* Déclenchement manuel d’une fonction
* Écriture de journaux

## <a name="a-idnextstepsa-next-steps"></a><a id="nextsteps"></a> Étapes suivantes
Ce guide fournit des exemples de code qui indiquent comment gérer des scénarios courants pour l’utilisation des tables Microsoft Azure. Pour plus d’informations sur l’utilisation d’Azure Webjobs et du Kit de développement logiciel (SDK) WebJobs Azure, consultez la rubrique [Azure Webjobs - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).




<!--HONumber=Nov16_HO3-->


