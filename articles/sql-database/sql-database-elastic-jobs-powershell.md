<properties 
	pageTitle="Création et gestion des tâches de bases de données élastiques à l'aide de PowerShell" 
	description="Gérer la base de données SQL Azure avec PowerShell" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="ddove; sidneyh" />

# Création et gestion des tâches de bases de données SQL élastiques à l'aide de PowerShell (version préliminaire)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)

## Vue d’ensemble

La fonctionnalité **Tâches de bases de données élastiques** (version préliminaire) vous permet d'exécuter un script Transact-SQL (T-SQL) ou d’appliquer un DACPAC dans un groupe de bases de données, notamment une collection de bases de données personnalisée, toutes les bases de données dans un [pool de bases de données élastique (version préliminaire)](sql-database-elastic-pool.md) ou un ensemble de partitions (créé à l’aide d’une [bibliothèque cliente d’une base de données élastique](sql-database-elastic-database-client-library.md)). Dans la version préliminaire, la fonctionnalité **Tâches de bases de données élastiques** est actuellement un service cloud Azure hébergé par le client qui permet l'exécution des tâches administratives ad hoc et planifiées, appelées tâches. L’utilisation de cette fonction vous permet de gérer facilement, et de manière fiable, un grand nombre de bases de données SQL Azure à grande échelle en exécutant des scripts Transact-SQL pour réaliser des opérations administratives telles que les modifications de schéma, la gestion des informations d’identification, les mises à jour de données de référence, la collecte des données de performances ou la collecte télémétrique du locataire (client). Pour plus d’informations sur la fonctionnalité Tâches de bases de données élastiques, consultez [Vue d’ensemble des tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md).

Avec les APIs PowerShell pour les **tâches de bases de données élastiques**, vous avez la possibilité de définir quels scripts seront exécutés pour quel groupe de bases de données. Actuellement, la fonctionnalité **Tâches de base de données élastique** via le portail Azure a réduit l’ensemble des fonctionnalités et est limitée à l’exécution sur des pools de bases de données élastiques.

La fonctionnalité **Tâches de base de données élastique** (version préliminaire) utilise plusieurs composants Azure pour définir les tâches à effectuer, définir le moment d’exécution des tâches, exécuter les tâches, suivre la réussite ou l'échec de ces tâches et éventuellement préciser une destination de résultats pour les résultats qui renvoient des requêtes. Étant donné que les API Powershell incluses dans cette version préliminaire contiennent des fonctionnalités supplémentaires par rapport à la version préliminaire initiale via le portail, nous vous invitons à installer les derniers composants de **Tâches de bases de données élastiques**. Si ceux-ci sont déjà installés, vous pouvez simplement mettre à niveau les composants des **tâches de bases de données élastiques**. Pour plus d'informations sur l'installation à partir de [Nuget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.Jobs), consultez[Installation des composants de tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md).

Cet article vous explique comment créer tout ce dont vous avez besoin pour créer et configurer des **tâches de bases de données élastiques**, à l’exception de l’abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur VERSION D'ÉVALUATION GRATUITE en haut de cette page, puis continuez la lecture de cet article. Cette rubrique développe l’exemple de la rubrique qui se trouve dans [Prise en main des outils de bases de données élastiques](sql-database-elastic-scale-get-started.md). Après cette étape, vous allez découvrir comment créer et gérer des tâches pour effectuer des opérations administratives sur un groupe de bases de données partagées définies par un **ensemble de partitions** et également des collections de bases de données personnalisées.

## Composants requis
* Un abonnement Azure. Pour obtenir un essai gratuit, voir [Version d'évaluation d'un mois gratuite](http://azure.microsoft.com/pricing/free-trial/).
* Le package PowerShell de **Tâches de bases de données élastiques** doit d’abord être téléchargé, importé et les composants des tâches de bases de données élastiques installés. Suivez ces étapes : [Installation de tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md).
* Azure PowerShell, version >= 0.8.16. Installez la dernière version (0.9.5) via [Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).

### Sélectionner votre abonnement Azure

Pour sélectionner l’abonnement, vous avez besoin de votre ID d’abonnement (**-SubscriptionId**) ou votre nom d’abonnement (**-SubscriptionName**). Si vous avez plusieurs abonnements, vous pouvez exécuter l'applet de commande **Get-AzureSubscription** et copier les informations d'abonnement souhaitées affichées dans les résultats. Une fois que vous disposez de vos informations d’abonnement, exécutez l’applet de commande pour définir l’abonnement en tant qu’abonnement par défaut, à savoir, la cible pour créer et gérer des tâches :

	Select-AzureSubscription -SubscriptionId {SubscriptionID}

Il est recommandé d’utiliser [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) pour développer et exécuter des scripts PowerShellsur les tâches de bases de données élastiques.

## Objets des tâches de bases de données élastiques

Le tableau suivant répertorie les types d'objets des **tâches de bases de données élastiques** ainsi que leur description et les API PowerShell correspondantes.

<table style="width:100%">
  <tr>
    <th>Type d'objet</th>
    <th>Description</th>
    <th>API PowerShell correspondantes</th>
  </tr>
  <tr>
    <td>Informations d'identification</td>
    <td>Nom d'utilisateur et mot de passe à utiliser lors de la connexion aux bases de données pour l'exécution de scripts ou l'application d’un DACPAC. <p>Le mot de passe est chiffré avant l’envoi et le stockage dans la base de données des tâches de bases de données élastiques. Le mot de passe est déchiffré par le service des tâches de bases de données élastiques via les informations d’identification créées et téléchargées à partir du script d’installation.</td>
	<td><p>Get-AzureSqlJobCredential</p>
	<p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script Transact-SQL à utiliser pour l’exécution dans les bases de données. Le script doit être créé pour être idempotent puisque le service réessaiera l’exécution du script en cas de défaillances.
	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>Get-AzureSqlJobContentDefinition</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td>Package d’<a href="https://msdn.microsoft.com/library/ee210546.aspx">application de la couche Données </a> pour être appliquées sur les bases de données

	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>
  <tr>
    <td>Base de données cible</td>
    <td>Nom de la base de données et du serveur pointant vers une base de données Azure&#160;SQL.

	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
  <tr>
    <td>Carte de partitions cible</td>
    <td>Combinaison d’une cible de base de données et d’une information d’identification à utiliser pour déterminer les informations stockées dans une carte de partitions de base de données élastique.
	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	<p>Set-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Cible de la collection personnalisée</td>
    <td>Groupe défini de bases de données à utiliser ensemble pour l’exécution.</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Cible enfant de la collection personnalisée</td>
    <td>Cible de la base de données qui est référencée à partir d'une collection personnalisée.</td>
	<td>
	<p>Add-AzureSqlJobChildTarget</p>
	<p>Remove-AzureSqlJobChildTarget</p>
	</td>
  </tr>

<tr>
    <td>Job</td>
    <td>
	<p>Définition des paramètres d'une tâche qui peut être utilisée pour déclencher l'exécution ou pour répondre à une planification.</p>
	</td>
	<td>
	<p>Get-AzureSqlJob</p>
	<p>New-AzureSqlJob</p>
	<p>Set-AzureSqlJob</p>
	</td>
  </tr>

<tr>
    <td>Exécution des tâches</td>
    <td>
	<p>Conteneur de tâches nécessaire pour pouvoir exécuter un script ou appliquer un DACPAC à une cible à l’aide des informations d’identification pour les connexions de bases de données avec des défaillances gérées suivant une stratégie d’exécution.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Exécution d’une tâche</td>
    <td>
	<p>Unité de travail unique pour effectuer une tâche.</p>
	<p>Si une tâche ne parvient pas à s’exécuter correctement, le message d’exception qui en résulte sera journalisé et une nouvelle tâche correspondante sera créée et exécutée suivant la stratégie d’exécution indiquée.</p></p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Stratégie d’exécution des tâches</td>
    <td>
	<p>Contrôle les délais d’expiration d’une tâche, les limites du nombre de tentatives et les intervalles entre les tentatives.</p>
	<p>Les tâches de bases de données incluent une stratégie d’exécution de tâche par défaut qui entraîne essentiellement un nombre infini de nouvelles tentatives de défaillances de tâches avec une interruption exponentielle des intervalles entre chaque tentative.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecutionPolicy</p>
	<p>New-AzureSqlJobExecutionPolicy</p>
	<p>Set-AzureSqlJobExecutionPolicy</p>
	</td>
  </tr>

<tr>
    <td>Planification</td>
    <td>
	<p>Indication temporelle pour que l’exécution ait lieu à un intervalle récurrent ou à un moment précis.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobSchedule</p>
	<p>New-AzureSqlJobSchedule</p>
	<p>Set-AzureSqlJobSchedule</p>
	</td>
  </tr>

<tr>
    <td>Déclencheur de tâches</td>
    <td>
	<p>Un mappage entre une tâche et une planification pour déclencher l’exécution de la tâche suivant la planification.</p>
	</td>
	<td>
	<p>New-AzureSqlJobTrigger</p>
	<p>Remove-AzureSqlJobTrigger</p>
	</td>
  </tr>
</table>

## Types de groupe des tâches de bases de données élastiques prises en charge
La fonctionnalité **Tâches de bases de données élastiques** permet l'exécution des scripts Transact-SQL (T-SQL) ou l'application de DACPAC sur un groupe de bases de données. Lorsqu’un travail est soumis pour être exécuté sur un groupe de bases de données, les tâches de bases de données élastiques vont développer la tâche dans des tâches enfants dans lesquelles chaque tâche effectue l’exécution requise sur une seule base de données du groupe.
 
Voici la liste des types de groupe actuellement pris en charge :

* [Carte de partitions](sql-database-elastic-scale-shard-map-management.md) : lorsqu’une tâche est soumise pour cibler une carte de partitions, les tâches interrogent d’abord la carte de partitions afin de déterminer l’ensemble de ses partitions actuelles et de développer la tâche dans des tâches enfants correspondant à chaque partition dans la carte de partitions.
* Collection personnalisée : elle est indiquée pour pointer vers un ensemble défini de bases de données personnalisées. Lorsqu’une tâche est soumise pour cibler une collection personnalisée, les tâches développent cette tâche dans des tâches enfants qui correspondent à chaque base de données actuelle définie dans la collection personnalisée.

## Définition de la connexion des tâches de bases de données élastiques
Après le chargement du module PowerShell, la connexion doit être définie sur la *base de données de contrôle* des tâches de bases de données élastiques avant l’utilisation des API des tâches. L’appel de cet applet de commande affichera une fenêtre d’informations d’identification demandant le nom d’utilisateur et le mot de passe fournis lors de l’installation des tâches de bases de données. Tous les exemples fournis dans cette rubrique partent du principe que cette première étape a déjà été effectuée.

Ouvrez une connexion des tâches de bases de données élastiques :

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## Informations d'identification chiffrées dans les tâches de bases de données élastiques
Les informations d’identification de la base de données peuvent être insérées dans la *base de données de contrôle* des tâche de bases de données avec leur mot de passe chiffré. Il est nécessaire de stocker des informations d’identification pour activer des tâches à exécuter plus tard, notamment à l’aide de la planification de tâche.
 
Le chiffrement utilise un certificat créé dans le cadre du script d'installation. Le script d'installation crée et télécharge le certificat dans le Azure Cloud Service pour le déchiffrement des mots de passe chiffrés stockés. Azure Cloud Service stocke ultérieurement la clé publique dans la *base de données de contrôle* des tâches de bases de données, ce qui permet à l'interface de l’API PowerShell ou du portail Azure de chiffrer un mot de passe fourni sans que l’installation locale du certificat ne soit requise.
 
Lorsque les mots de passe des informations d’identification sont chiffrés et protégés des utilisateurs avec un accès en lecture seule à des objets de tâches de bases de données élastiques, un utilisateur malveillant avec un accès en lecture seule aux objets des tâches de bases de données élastiques peut récupérer un mot de passe. Les informations d’identification sont conçues pour être réutilisées lors des exécutions de tâches. Les informations d'identification sont transmises aux bases de données cibles lors de l'établissement des connexions. Il n’existe actuellement aucune restriction sur les bases de données cibles utilisées pour chaque information d’identification. Il est donc possible pour un utilisateur malveillant d’ajouter une cible de base de données pour une base de données qu’ils contrôlent et, par conséquent, de commencer une tâche en ciblant cette base de données en vue de connaître les mot de passe des informations d’identification.

Il existe des meilleures pratiques de sécurité pour les tâches de bases de données élastiques :

* Limitez l'utilisation des API aux personnes de confiance.
* Les informations d'identification doivent avoir les privilèges minimaux requis pour effectuer la tâche. Plus d'informations sont disponibles dans l’article MSDN sur SQL Server : [Autorisations](https://msdn.microsoft.com/library/bb669084.aspx).

### Création d'informations d'identification chiffrées pour l'exécution d’une tâche dans des bases de données
Pour créer de nouvelles informations d'identification chiffrées, l'applet de commande Get-Credential demandera un nom d'utilisateur et un mot de passe qui peuvent être transmis à l’applet de commande **New-AzureSqlJobCredential**.

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### Mise à jour des informations d’identification
Pour mettre à jour les informations d'identification existantes pour prendre en charge le cas des modifications de mots de passe, utilisez l’applet de commande **Set-AzureSqlJobCredential** et définissez le paramètre **CredentialName**.

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## Définition d'une cible de carte de partitions de base de données élastique
Exécutez une tâche sur toutes les bases de données dans un ensemble de partitions (créé à l’aide de la [bibliothèque cliente de la base de données élastique](sql-database-elastic-database-client-library.md)) à l’aide d’une carte de partitions en tant que cible de base de données. Cet exemple dépend de si vous créez une application partitionnée à l’aide de la bibliothèque cliente de la base de données élastique. Téléchargez et exécutez l’exemple de la rubrique [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).

###Créez un gestionnaire des cartes de partitions à l’aide de l’exemple d’application

Ici vous allez créer un gestionnaire des cartes de partitions avec plusieurs partitions, puis insérer des données dans les partitions. Si vous disposez déjà des partitions configurées, vous pouvez ignorer ces étapes et passer à la section suivante.

1. Créez et exécutez l’exemple d’application de la rubrique **Prise en main des outils de base de données élastique**. Suivez la procédure jusqu’à l’étape 7 dans la section [Télécharger et exécuter l’exemple d’application](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). À la fin de l’étape 7, vous verrez l’invite de commande suivante :

	![invite de commande][1]

2.  Dans la fenêtre de commande, entrez « 1 » et appuyez sur **Entrée**. Cela crée le gestionnaire des cartes de partitions et ajoute deux partitions sur le serveur. Entrez « 3 », puis appuyez sur **Entrée**. Répétez l’action quatre fois. Cela permet d’insérer des lignes d’exemples de données dans vos partitions.
  
3.  Le [portail Azure en version préliminaire](https://portal.azure.com) doit afficher trois nouvelles bases de données dans votre serveur v12 :

	![Confirmation Visual Studio][2]

Créez maintenant une cible de carte de partitions, à l'aide de l’applet de commande **New-AzureSqlJobTarget**. La base de données du gestionnaire de cartes de partitions doit être définie en tant que base de données cible et la carte de partitions spécifique doit être spécifiée en tant que cible.

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## Créez un script T-SQL pour l’exécuter sur des bases de données

Lorsque vous créez des scripts T-SQL pour l'exécution, il est fortement recommandé de les créer pour être idempotents et résistants aux défaillances. Les tâches de bases de données élastiques vont tenter à nouveau l’exécution d’un script chaque fois que l’exécution rencontre une défaillance, quel que soit le type de défaillance.

Utilisez l’applet de commande **New-AzureSqlJobContent** pour créer et enregistrer un script pour l'exécution, puis définissez les paramètres **- ContentName présent** et **- CommandText**.

	$scriptName = "Create a TestTable"

	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
		CREATE TABLE TestTable(
			TestTableId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Créer un script à partir d'un fichier
Si le script T-SQL est défini dans un fichier, le script suivant peut être utilisé pour importer le script :

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Mettre à jour un script T-SQL pour l’exécution sur des bases de données  

Le script PowerShell suivant peut être utilisé pour mettre à jour le texte de commande T-SQL d’un script existant.

Définissez les variables suivantes pour refléter la définition de script souhaitée à définir :

	$scriptName = "Create a TestTable"
	$scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	CREATE TABLE TestTable(
		TestTableId INT PRIMARY KEY IDENTITY,
		InsertionTime DATETIME2
	);
	END
	GO

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO"

### Mettre à jour la définition d'un script existant

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

##Créer une tâche pour exécuter un script sur une carte de partitions

Le script PowerShell suivant peut être utilisé pour démarrer une tâche pour l'exécution d'un script sur chaque partition dans la carte de partitions d’une infrastructure élastique.

Définissez les variables suivantes pour refléter le script et la cible souhaités :

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$credentialName = "{Credential Name}"
	$shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
	$job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
	Write-Output $job

##Exécuter une tâche 

Le script PowerShell suivant peut être utilisé pour exécuter une tâche existante :

Mettez à jour la variable suivante pour refléter le nom de la tâche souhaitée à exécuter :

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Récupérer l'état de l'exécution d'une tâche unique

Utilisez l’applet de commande **Get-AzureSqlJobExecution** et définissez le paramètre **JobExecutionId** pour afficher l'état de l'exécution de la tâche.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

Utilisez la même applet de commande **Get-AzureSqlJobExecution** avec le paramètre **IncludeChildren** pour afficher l'état de l’exécution des tâches enfants, à savoir l'état spécifique à chaque exécution de tâche sur chaque base de données ciblée par la tâche.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## Afficher l’état sur plusieurs exécutions de tâches

L’applet de commande **Get-AzureSqlJobExecution** dispose de plusieurs paramètres facultatifs qui peuvent être utilisés pour afficher plusieurs exécutions de tâches, filtrées selon les paramètres fournis. L'exemple suivant présente certaines façons d'utiliser Get-AzureSqlJobExecution :

Récupérez toutes les exécutions de tâches de niveau supérieur actives :

	Get-AzureSqlJobExecution

Récupérez toutes les exécutions de tâches de niveau supérieur, y compris les exécutions de tâches inactives :

	Get-AzureSqlJobExecution -IncludeInactive

Récupérez toutes les exécutions de tâches enfants d'un ID d'exécution de tâches fourni, y compris les exécutions de tâches inactives :

	$parentJobExecutionId = "{Job Execution Id}"
	Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Récupérez toutes les exécutions de tâches créées à l'aide d'une planification/combinaison de tâches, y compris les tâches inactives :

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Récupérez toutes les tâches ciblant une carte de partitions spécifiée, y compris les tâches inactives :

	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Récupérez toutes les tâches ciblant une collecte personnalisée spécifiée, y compris les tâches inactives :

	$customCollectionName = "{Custom Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Récupérez la liste des exécutions de tâches de travail dans l'exécution d'une tâche spécifique :

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Write-Output $jobTaskExecutions 

Récupérez les détails d’exécution de tâches de travail :

Le script PowerShell suivant peut être utilisé pour afficher les détails d'une exécution de tâches de travail, qui est particulièrement utile lors du débogage des échecs d'exécution.

	$jobTaskExecutionId = "{Job Task Execution Id}"
	$jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
	Write-Output $jobTaskExecution

## Récupérez des échecs dans les exécutions de tâches de travail

L'objet JobTaskExecution inclut une propriété pour le cycle de vie de la tâche, ainsi qu’une propriété Message. Si une exécution de tâches de travail a échoué, la propriété du cycle de vie est définie sur *Échec* et la propriété Message est définie sur le message d'exception résultant et sa pile. Si une tâche a échoué, il est important d’afficher les détails des tâches de travail qui n'ont pas abouti pour une tâche donnée.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## En attente d’une exécution de tâche à effectuer

Le script PowerShell suivant peut être utilisé pour attendre l’exécution d’une tâche :

	$jobExecutionId = "{Job Execution Id}"
	Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## Créez une stratégie d'exécution personnalisée

Tâches de bases de données prend en charge la création de stratégies d'exécution personnalisées qui peuvent être appliquées lors du démarrage des tâches.
  
Les stratégies d'exécution permettent de définir :

* Le nom : l'identificateur de la stratégie d'exécution.
* Délai d’attente de la tâche : délai avant l’annulation d’une tâche par Tâches de bases de données élastiques.
* Intervalle avant nouvelle tentative initiale : l'intervalle d'attente avant la première nouvelle tentative.
* Intervalle maximal avant nouvelle tentative : plafond des intervalles avant nouvelle tentative à utiliser.
* Coefficient d'interruption de l’intervalle avant nouvelle tentative : ce coefficient permet de calculer le prochain intervalle entre les tentatives. La formule suivante est utilisée : (intervalle avant nouvelle tentative initiale) * Math.pow (coefficient d’interruption de l’intervalle), (nombre de tentatives) - 2). 
* Nombre maximal de tentatives : le nombre maximal de nouvelles tentatives effectuées dans une tâche.

La stratégie d'exécution par défaut utilise les valeurs suivantes :

* Le nom : la stratégie d'exécution par défaut
* Délai d’attente de la tâche : 1 semaine
* Intervalle avant nouvelle tentative initiale : 100 millisecondes
* Intervalle maximal avant nouvelle tentative : 30 minutes
* Coefficient de l'intervalle avant nouvelle tentative : 2
* Nombre maximal de tentatives : 2 147 483 647

Créez la stratégie d'exécution souhaitée :

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 10
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $executionPolicy

### Mettez à jour une stratégie d'exécution personnalisée

Mettez à jour la stratégie d'exécution souhaitée :

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 15
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $updatedExecutionPolicy
 
## Annulation d’une tâche

La fonctionnalité Tâches de bases de données élastiques prend en charge les demandes d'annulation de tâches. Si la fonctionnalité Tâches de bases de données élastiques détecte une demande d'annulation d'une tâche en cours d'exécution, il tente d'arrêter la tâche.

La fonctionnalité Tâches de bases de données élastiques peut effectuer une annulation de deux manières différentes :

1. Annulation des tâches en cours d'exécution : si une annulation est détectée pendant qu’une tâche est en cours d'exécution, l'annulation sera tentée au sein de l'aspect de la tâche en cours d'exécution. Par exemple : si une requête de longue durée s’exécute lorsqu'une annulation est tentée, une tentative d'annulation de la requête sera effectuée.
2. Annulation des tentatives de tâches : si une annulation est détectée par le thread de contrôle avant de lancer l'exécution d'une tâche, le thread de contrôle permettra d’éviter le lancement de la tâche et de déclarer la requête comme étant annulée.

Si une annulation de tâche est demandée pour une tâche parente, la demande d'annulation sera respectée pour la tâche parente et toutes ses tâches enfants.
 
Pour envoyer une demande d'annulation, utilisez l’applet de commande **Stop-AzureSqlJobExecution** et définissez le paramètre **JobExecutionId**.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## Supprimez une tâche via son nom et l'historique de la tâche

Tâches de bases de données élastiques prend en charge la suppression des tâches asynchrone. La suppression d’une tâche peut être signalée et le système supprime la tâche et son historique une fois que toutes les exécutions de tâches auront été effectuées. Le système n'annule pas automatiquement les exécutions de tâches actives.

Au lieu de cela, Stop-AzureSqlJobExecution doit être appelé pour annuler les exécutions de tâches actives.

Pour déclencher la suppression de tâches, utilisez l’applet de commande **Remove-AzureSqlJob** et définissez le paramètre **JobName**.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## Création d’une cible de base de données personnalisée
Les cibles de base de données personnalisées peuvent être définies dans Tâches de bases de données élastiques, qui peut être utilisé directement pour l'exécution ou l’inclusion dans un groupe de base de données personnalisé. Dans la mesure où les **pools élastiques de bases de données** ne sont pas encore pris en charge directement via les API PowerShell, vous créez simplement une cible de base de données personnalisée et une cible de collecte de base de données personnalisée qui englobe toutes les bases de données dans le pool.

Définissez les variables suivantes pour refléter les informations de base de données souhaitées :

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## Créez une cible de collecte de base de données personnalisée
Une cible de collecte de base de données personnalisée peut être définie pour permettre l'exécution sur plusieurs cibles de base de données définies. Une fois le groupe de base de données créé, les bases de données peuvent être associées à la cible de collecte personnalisée.

Définissez les variables suivantes pour refléter la configuration de la cible de collecte personnalisée souhaitée :

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### Ajoutez des bases de données pour une cible de collecte de base de données personnalisée

Les cibles de base de données peuvent être associées à des cibles de collecte de base de données personnalisées pour créer un groupe de bases de données. Chaque fois qu’une tâche, visant une cible de collecte de base de données personnalisée, est créée, celle-ci est développée afin de cibler les bases de données associées au groupe durant l'exécution.

Ajoutez la base de données souhaitée pour une collecte personnalisée :

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Examinez les bases de données dans une cible de collecte de base de données personnalisée

Utilisez l’applet de commande **Get-AzureSqlJobTarget** pour récupérer les bases de données enfants dans une cible de collecte de base de données personnalisée.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Créez une tâche pour exécuter un script sur une cible de collecte de base de données personnalisée

Utilisez l’applet de commande **New-AzureSqlJob** pour créer une tâche sur un groupe de bases de données défini par une cible de collecte de base de données. La fonctionnalité Tâches de bases de données élastiques étendra la tâche en plusieurs tâches enfants correspondant chacune à une base de données associée à la cible de la collecte de base de données personnalisée et s’assurera que le script est exécuté sur chaque base de données. Encore une fois, il est important que les scripts soient idempotents pour résister à de nouvelles tentatives.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Collecte de données sur les bases de données

**Tâches de bases de données élastiques** prend en charge l'exécution d'une requête sur un groupe de bases de données et envoie les résultats de la table d’une base de données spécifiée. Le tableau peut être interrogé une fois les résultats de la requête affichés à partir de chaque base de données. Ceci fournit un mécanisme asynchrone, permettant d’exécuter une requête sur plusieurs bases de données. Les cas d'échec, notamment l’indisponibilité temporaire d’une des bases de données, sont gérés automatiquement par le biais de tentatives.

La table de destination spécifiée sera automatiquement créée s’il n'existe pas encore de table correspondant au schéma du jeu de résultats retourné. Si l'exécution d'un script retourne plusieurs jeux de résultats, la fonctionnalité Tâches de bases de données élastiques enverra uniquement le premier vers la table de destination fournie.

Le script PowerShell suivant peut être utilisé pour exécuter un script qui collecte ses résultats dans une table spécifiée. Ce script part du principe qu'un script T-SQL, qui génère un jeu de résultats unique, et une cible de collecte de base de données personnalisée ont été créés.

Définissez les valeurs suivantes pour refléter le script, les informations d'identification et les cibles d'exécution souhaités :

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$executionCredentialName = "{Execution Credential Name}"
	$customCollectionName = "{Custom Collection Name}"
	$destinationCredentialName = "{Destination Credential Name}"
	$destinationServerName = "{Destination Server Name}"
	$destinationDatabaseName = "{Destination Database Name}"
	$destinationSchemaName = "{Destination Schema Name}"
	$destinationTableName = "{Destination Table Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### Créez et démarrez une tâche pour les scénarios de collecte de données
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## Créez une planification pour l'exécution de tâches à l'aide d'un déclencheur de tâches

Le script PowerShell suivant peut être utilisé pour créer une planification récurrente. Ce script utilise un intervalle de minutes, mais New-AzureSqlJobSchedule prend également en charge les paramètres -DayInterval, - HourInterval, - MonthInterval, et - WeekInterval. Les planifications qui ne s'exécutent qu'une seule fois peuvent être créées en transmettant - OneTime.

Créez une nouvelle planification :

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
	Write-Output $schedule

### Créez un déclencheur de tâches pour une tâche exécutée selon un calendrier

Un déclencheur de tâches peut être défini pour un travail exécuté selon un calendrier. Le script PowerShell suivant peut être utilisé pour créer un déclencheur de tâches.

Définissez les variables suivantes pour qu’elles correspondent à la tâche et à la planification souhaitées :

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
	Write-Output $jobTrigger

### Supprimez une association planifiée pour arrêter une tâche depuis l'exécution de la planification

Pour interrompre l'exécution d’une tâche récurrente via un déclencheur de tâches, le déclencheur de tâches peut être supprimé. Supprimez un déclencheur de tâches pour arrêter une tâche qui s'exécute selon une planification à l'aide de l’applet de commande **Remove-AzureSqlJobTrigger**.

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName

### Récupérer des déclencheurs de tâche liés à un calendrier

Le script PowerShell suivant peut être utilisé pour obtenir et afficher les déclencheurs de tâche enregistrés dans un calendrier particulier.

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### Récupérer des déclencheurs de tâche liés à une tâche 

Le script PowerShell suivant peut être utilisé pour obtenir et afficher des calendriers contenant une tâche enregistrée.

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## Créer un déploiement des applications de la couche Données (DACPAC) pour l'exécution dans des bases de données

Les tâches de base de données élastiques peuvent être utilisées pour déployer une application de la couche Données (DACPAC) dans un groupe de bases de données. Pour créer un DACPAC, reportez-vous à cette documentation. Pour que les tâches de bases de données élastiques déploient un DACPAC dans un groupe de bases de données, le DACPAC doit être accessible au service. Il est recommandé de télécharger un DACPAC créé dans Azure Storage et de créer un URI signé pour le DACPAC.

Le script PowerShell suivant peut être utilisé pour insérer un DACPAC dans les tâches de bases de données élastiques :

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### Mise à jour d’un déploiement des applications de la couche Données (DACPAC) pour l'exécution dans des bases de données

Les DACPAC existants enregistrés dans des tâches de bases de données élastiques peuvent être mises à jour pour pointer vers de nouveaux URI. Le script PowerShell suivant peut être utilisé pour mettre à jour l’URI DACPAC sur un DACPAC existant enregistré :

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## Créer une tâche pour appliquer un déploiement des applications de la couche Données (DACPAC) pour l'exécution dans des bases de données

Après la création d’un DACPAC dans les tâches de bases de données élastiques, une tâche peut être créée pour appliquer le fichier DACPAC dans un groupe de bases de données. Le script PowerShell suivant peut être utilisé pour créer une tâche DACPAC dans une collection de bases de données personnalisées :

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=Sept15_HO4-->