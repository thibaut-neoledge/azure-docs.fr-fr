<properties 
	title="Upgrade to the Latest Elastic Scale Client Library" 
	pageTitle="Mise à niveau vers la dernière version de la bibliothèque cliente de l'infrastructure élastique" 
	description="Instructions pour la mise à niveau à l'aide de PowerShell et C#" 
	metaKeywords="sharding,elastic scale, Azure SQL DB sharding" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="stuarto", "sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="stuarto" />

# Mise à niveau vers la dernière version de la bibliothèque cliente de l'infrastructure élastique

Les nouvelles versions de la bibliothèque cliente de l'infrastructure élastique sont disponibles via [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) et l'interface du Gestionnaire de package NuGet dans Visual Studio. Les mises à niveau contiennent des résolutions de bogues et offrent une prise en charge des nouvelles fonctionnalités de l'infrastructure élastique.

## Étapes de la mise à niveau

La mise à niveau implique de régénérer votre application avec la nouvelle bibliothèque, ainsi que de modifier les métadonnées existantes du gestionnaire des cartes de partitions stockées dans vos bases de données SQL Azure pour prendre en charge les nouvelles fonctionnalités.

Suivez la procédure ci-dessous pour mettre à niveau vos applications, la base de données du gestionnaire des cartes de partitions et les métadonnées se trouvant sur chaque partition du gestionnaire des cartes de partitions locales.  Effectuez les étapes de mise à niveau dans l'ordre indiqué afin de garantir que plus aucune version antérieure de la bibliothèque cliente ne reste présente dans votre environnement après la mise à jour des objets de métadonnées. De cette façon, il n'y aura pas d'objets de métadonnées d'une version antérieure qui seront créés après la mise à niveau.   

**1. Mettez à niveau vos applications.** Dans Visual Studio, téléchargez la dernière version de la bibliothèque cliente et ajoutez une référence à cette bibliothèque dans tous vos projets de l'infrastructure élastique. Ensuite, régénérez et déployez les applications. 

 * Dans votre solution Visual Studio, sélectionnez **Outils** --> **Gestionnaire de package NuGet** -->**Gérer les packages NuGet pour la solution**. 
 * Dans le volet gauche, sélectionnez **Mises à jour**, puis cliquez sur le bouton **Mettre à jour** dans le package **Bibliothèque cliente de l'infrastructure élastique de base de données SQL Azure** qui s'affiche dans la fenêtre.
	![Upgrade Nuget Pacakges][1]
 
 * Générez et déployez les applications. 

**2. Mettez à niveau vos scripts.** Si vous utilisez des scripts **PowerShell** pour gérer des partitions, [téléchargez la nouvelle version de la bibliothèque](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) et copiez-la dans le répertoire à partir duquel vous exécutez ces scripts. 

**3. Mettez à niveau votre service de fractionnement/fusion.** Si vous utilisez le service de fractionnement/fusion de l'infrastructure élastique pour réorganiser les données partitionnées, [téléchargez et déployez la dernière version du service](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Vous trouverez plus d'informations sur les étapes de mise à niveau du service [ici](http://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-overview-split-and-merge/). 

**4. Mettez à niveau vos bases de données du gestionnaire des cartes de partitions**. Mettez à niveau les métadonnées de prise en charge de vos cartes de partitions dans la base de données SQL Azure.  Vous pouvez effectuer cette opération avec PowerShell ou C#. Ces deux options sont expliquées ci-dessous.

***Option 1 : mise à niveau des métadonnées à l'aide de PowerShell***

1. Téléchargez [ici](http://nuget.org/nuget.exe) la dernière version de l'utilitaire de ligne de commande de NuGet et enregistrez-la dans un dossier. 

2. Ouvrez une invite de commandes, accédez au dossier choisi et exécutez cette commande :
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Accédez au sous-dossier contenant la nouvelle version de la DLL du client que vous venez de télécharger. Par exemple :
`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.0.8.0\lib\net45`

4. Téléchargez le scriptlet de la mise à niveau du client de l'infrastructure élastique à partir du [centre de scripts](http://go.microsoft.com/?linkid=9876343) et enregistrez-le dans le dossier contenant la DLL.

5. Dans ce dossier, exécutez " PowerShell .\upgrade.ps1 " à partir de l'invite de commandes et suivez les invites.
 
***Option 2 : mise à niveau des métadonnées à l'aide de C#***

Une autre solution consiste à créer une application Visual Studio qui ouvre votre gestionnaire des cartes de partitions, effectue une itération sur toutes les partitions, et met à niveau les métadonnées en appelant les méthodes [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) et [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx). Voici un exemple : 

	ShardMapManager smm =
	   ShardMapManagerFactory.GetSqlShardMapManager
	   (connStr, ShardMapManagerLoadPolicy.Lazy); 
	smm.UpgradeGlobalStore(); 
	
	foreach (ShardLocation loc in
	 smm.GetDistinctShardLocations()) 
	{   
	   smm.UpgradeLocalStore(loc); 
	} 

Ces méthodes de mise à niveau des métadonnées peuvent être appliquées plusieurs fois sans risque. Par exemple, si une version antérieure du client crée une partition à tort après la mise à jour, vous pouvez effectuer une nouvelle mise à niveau des métadonnées de toutes les partitions pour vous assurer que votre infrastructure contient bien la dernière version des métadonnées. 

**Remarque** : les nouvelles versions de la bibliothèque cliente qui ont été publiées jusqu'à présent sont toutes compatibles avec les versions antérieures des métadonnées du gestionnaire des cartes de partitions stockées dans les bases de données SQL Azure, et vice versa. Toutefois, pour tirer pleinement parti de certaines nouvelles fonctionnalités proposées dans la dernière version du client, vous devez mettre à niveau les métadonnées. Notez que les mises à niveau des métadonnées n'ont pas d'incidence sur les données utilisateur ou les données d'application ; elles ne s'appliquent qu'aux objets créés et utilisés par le gestionnaire des cartes de partitions. Les applications continuent de fonctionner selon le processus de mise à niveau décrit ci-dessus.

## Historique des versions du client de l'infrastructure élastique 

**Version 0.8 - mars 2015**

* Ajout de la prise en charge d'Async pour le routage dépendant des données avec les nouvelles méthodes ShardMap.OpenConnectionForKeyAsync 
* Ajout de la propriété KeyType publique à ShardMap 
* Amélioration de la prise en charge des scénarios de récupération d'urgence et de restauration des bases de données pour les partitions 

**Version 0.7 - octobre 2014**

Version préliminaire initiale 


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

<!--HONumber=47-->
