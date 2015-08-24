<properties 
	pageTitle="Prise en main du stockage Azure et des services connectés Visual Studio (projets WebJob)" 
	description="Prise en main du stockage de tables Azure au sein d’un projet Azure WebJobs 5 dans Visual Studio"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags 
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="patshea123"/>

# Prise en main d’Azure Storage (projets de tâche web Azure)

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-webjobs-getting-started-tables.md)
> - [What happened](vs-storage-webjobs-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-webjobs-getting-started-blobs.md)
> - [Queues](vs-storage-webjobs-getting-started-queues.md)
> - [Tables](vs-storage-webjobs-getting-started-tables.md)



## Vue d'ensemble

Cet article fournit des exemples de code C# qui montrent comment utiliser la version 1.x du Kit de développement logiciel (SDK) WebJobs Azure avec le service de stockage de tables Azure. Les exemples de code utilisent le [Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk.md) version 1.x.

Le service de stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles. Pour plus d’informations, voir l’article [Utilisation du stockage de tables à partir de .NET](storage-dotnet-how-to-use-tables.md/#create-a-table "Utilisation du stockage de tables à partir de .NET").

		
Certains extraits de code illustrent l’attribut `Table` utilisé dans des fonctions [appelées manuellement](vs-storage-webjobs-getting-started-blobs.md#manual), c’est-à-dire sans utiliser l’un des attributs de déclenchement.

##Ajout d’une entité à une table

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

##Surveillance en temps réel

Étant donné que les fonctions d’entrée de données traitent souvent des volumes importants de données, le tableau de bord du Kit de développement logiciel (SDK) WebJobs fournit des données d’analyse en temps réel. La section **Journal d’appels** vous signale si la fonction est toujours en cours d’exécution.

![Fonction d’entrée en cours d’exécution](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

La page **Détails des appels** signale la progression de la fonction (c’est-à-dire le nombre d’entités écrites) alors qu’elle s’exécute et vous permet de l’arrêter.

![Fonction d’entrée en cours d’exécution](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Lorsque l’exécution de la fonction se termine, la page **Détails des appels** indique le nombre de lignes écrites.

![Arrêt de la fonction d’entrée](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

##Lecture de plusieurs entrées à partir d’une table

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

###Lecture d’une entité unique à partir d’une table

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

##Utilisation directe de l’API de stockage .NET pour travailler avec une table

Vous pouvez également utiliser l’attribut `Table` avec un objet `CloudTable`, afin de garantir une utilisation plus souple des tables.

L’exemple de code suivant utilise un objet `CloudTable` pour ajouter une entité unique à la table *Entrée*.
 
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

Pour en savoir plus sur l’utilisation de l’objet `CloudTable`, voir [Utilisation du stockage de tables à partir de .NET](../storage-dotnet-how-to-use-tables.md).

##Sujets connexes traités dans l’article de procédure relatif aux files d’attente

Pour en savoir plus sur la gestion du traitement de tables déclenché par un message en file d’attente, ou pour consulter des scénarios relatifs au Kit de développement logiciel (SDK) WebJobs non spécifiques du traitement des tables, voir [Comment utiliser le stockage de la file d’attente Azure avec le Kit de développement logiciel (SDK) WebJobs](vs-storage-webjobs-getting-started-queues.md).



##Étapes suivantes

Cet article a fourni des exemples de code qui montrent comment gérer des scénarios courants pour l’utilisation des tables Azure. Pour plus d’informations sur l’utilisation d’Azure Webjobs et du Kit de développement logiciel (SDK) WebJobs Azure, consultez la rubrique [Azure Webjobs - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=August15_HO7-->