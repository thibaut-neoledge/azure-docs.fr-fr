<properties 
	pageTitle="Scénarios courants d'utilisation d'Azure Data Factory" 
	description="Découvrez quelques scénarios courants d'utilisation du service Azure Data Factory" 
	services="data-factory" 	
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Scénarios courants d'utilisation d'Azure Data Factory
Cette section décrit quelques exemples de scénarios actuellement pris en charge par Azure Data Factory. Elle intègrera les scénarios relatifs aux hubs au fil de leur évolution.

> [AZURE.NOTE]Avant de lire cet article, commencez par consulter la [Présentation du service Azure Data Factory][datafactory-introduction].

##Scénario #1 : sources de données pour le concentrateur de données
![Sourcer le concentrateur de données][image-data-factory-introduction-secenario1-source-datahub]

Les entreprises disposent de données de différents types situées dans des sources diverses. La première étape dans la création d'un système de production d'informations consiste à se connecter à toutes les sources nécessaires de données et de traitement des données comme les services SaaS, web, FTP et de partages de fichiers, et déplacer les données en fonction des besoins pour un traitement futur.

Sans Data Factory, les entreprises doivent créer des composants de déplacement de données personnalisés ou écrire des services personnalisés pour intégrer ces sources de données et le traitement des données. L'intégration et l'entretien de tels systèmes sont coûteux et difficiles à effectuer. Le niveau de surveillance et d'alerte de l'entreprise est rarement à la hauteur, ainsi que les contrôles, d'un service entièrement géré.
  
Les services de stockage et de traitement des données d'Azure Data Factory sont rassemblés dans un conteneur « Concentrateur » qui facilite et optimise les activités de calcul et de stockage, permet la gestion de la consommation des ressources unifiées et fournit des services de déplacement des données en fonction des besoins.

##Scénario #2 : rendre la production des informations opérationnelle
Les scénarios de mise en œuvre opérationnelle viennent logiquement après les scénarios liés aux sources de données. Une fois que les données sont présentes dans un concentrateur, vous souhaitez créer et rendre opérationnels des pipelines de données pour produire des données transformées de manière fiable selon une planification contrôlée et facile à gérer pour les environnements de production avec des données approuvées. La transformation des données dans Azure Data Factory s'effectue via le traitement Hive, Pig et C# personnalisé s'exécutant sur Hadoop (Azure HDInsight). Ces transformations peuvent être utilisées pour nettoyer les données, masquer les champs de données critiques et effectuer d'autres opérations sur les données selon un grand nombre de méthodes complexes. Généralement, la mise en œuvre opérationnelle est obtenue via une infrastructure et des services personnalisés à la fois complexes et difficiles à gérer. En outre, elle présente un certain nombre de défis en matière d'implémentation, de gestion, d'extensibilité, de résolution des problèmes et de contrôle de version.
  
Avec le service entièrement géré de Data Factory, les utilisateurs peuvent rendre ces pipelines opérationnels en les définissant avec des planifications récurrentes à usage unique ou complexes et le service Data Factory se charge de tout le reste. Grâce aux hubs, la gestion de clusters pour l'ensemble des données et du traitement au sein d'un hub est effectuée pour le compte de l'utilisateur. Ainsi, les utilisateurs peuvent se consacrer à l'analyse des transformations au lieu de la gestion de l'infrastructure. Azure Data Factory supprime les défis posés par l'utilisation de services personnalisés fragiles, et permet aux entreprises de produire des informations de façon fiable et répétitive.


##Scénario #3 : intégrer la production d'informations à la découverte de données
Les approches et technologies classiques liées au décisionnel, tout en fournissant une « source d'autorité fiable », entraînent presque toujours un effet secondaire grave : un backlog constant des requêtes lié à un processus de changement des requêtes soigneusement contrôlé. Pour s'adapter rapidement aux problèmes liés à l'évolution rapide de l'activité, les entreprises doivent être plus souples et connecter leurs systèmes de production d'informations et leurs systèmes de consommation des informations. Azure Data Factory leur permet de relever le défi de connexion de ces systèmes aux pipelines de données rationalisés pour la production d'informations et le défi de la consommation d'informations en mettant à disposition des données approuvées sous des formes facilement consommables.
  
Azure Data Factory prend en charge les fonctionnalités suivantes pour activer la consommation simple des données produites :

- Déplacez facilement (de manière unique ou planifiée) les ressources de données produites vers des mini-data warehouse relationnels pour la consommation à l'aide d'outils décisionnels existants (Excel, Tableau, etc…).
- Consommez des ressources de données générées par une fabrique de données directement à l'aide de Power Query dans Excel.

Consultez les rubriques suivantes pour consommer des données à l'aide de Power Query :

- [Power Query : connexion au stockage de tables Microsoft Azure][Power-Query-Azure-Table]
- [Power Query : connexion au stockage d’objets blob Microsoft][Power-Query-Azure-Blob]
- [Power Query : connexion à la base de données SQL Microsoft Azure][Power-Query-Azure-SQL]
- [Power Query : connexion au serveur Microsoft SQL Server local][Power-Query-OnPrem-SQL] 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[copy-data-with-adf]: http://azure.microsoft.com/documentation/articles/data-factory-copy-activity/
[use-pig-hive]: http://azure.microsoft.com/documentation/articles/data-factory-pig-hive-activities/
[run-map-reduce]: http://azure.microsoft.com/documentation/articles/data-factory-map-reduce/
[azure-ml-adf]: http://azure.microsoft.com/documentation/articles/data-factory-create-predictive-pipelines/

[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md
[datafactory-introduction]: data-factory-introduction.md

[image-data-factory-introduction-secenario1-source-datahub]: ./media/data-factory-common-scenarios/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]: ./media/data-factory-common-scenarios/Scenario2-OperationalizeInformationProduction.png

<!---HONumber=GIT-SubDir--> 