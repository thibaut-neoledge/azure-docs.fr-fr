<properties 
	pageTitle="Exécution d'une tâche Hadoop avec DocumentDB et HDInsight | Microsoft Azure" 
	description="Découvrez comment exécuter une tâche Hive, Pig ou MapReduce simple avec DocumentDB et Azure HDInsight."
	services="documentdb" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="mimig"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="anhoh"/>

#<a name="DocumentDB-HDInsight"></a>Exécution d’une tâche Hadoop avec DocumentDB et HDInsight

Ce didacticiel vous montre comment exécuter des tâches [Apache Hive][apache-hive], [Apache Pig][apache-pig] et [Apache Hadoop][apache-hadoop] MapReduce dans Azure HDInsight avec le connecteur Hadoop de DocumentDB. Le connecteur Hadoop de DocumentDB permet à DocumentDB d’agir en tant que source et récepteur pour les tâches Hive, Pig et MapReduce. Ce didacticiel utilise DocumentDB en tant que source de données et destination pour les tâches Hadoop. 

Après avoir terminé ce didacticiel, vous serez en mesure de répondre aux questions suivantes :

- Comment charger des données à partir de DocumentDB à l'aide d'une tâche Hive, Pig ou MapReduce ?
- Comment stocker des données dans DocumentDB à l'aide d'une tâche Hive, Pig ou MapReduce ?

Nous vous recommandons de commencer par visionner la vidéo suivante, où nous détaillons l'exécution d'une tâche Hive avec DocumentDB et HDInsight.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Revenez ensuite à cet article, où vous obtiendrez des détails complets sur l'exécution de tâches d'analyse sur vos données DocumentDB.

> [AZURE.TIP]Ce didacticiel part du principe que vous avez déjà utilisé Apache Hadoop, Hive et/ou Pig. Si vous débutez avec Apache Hadoop, Hive et Pig, nous vous recommandons de commencer par consulter la [documentation Apache Hadoop][apache-hadoop-doc]. Ce didacticiel suppose également que vous avez déjà utilisé DocumentDB et que vous possédez un compte DocumentDB. Si vous n’avez jamais utilisé DocumentDB ou si vous ne possédez pas de compte DocumentDB, consultez notre page [Prise en main][getting-started].

Vous n'avez pas le temps de suivre le didacticiel et vous souhaitez seulement obtenir des scripts PowerShell d'exemple pour Hive, Pig et MapReduce ? Ce n’est pas un problème. Vous pouvez les obtenir [ici][documentdb-hdinsight-samples]. Le téléchargement contient également les fichiers hql, pig et java pour ces exemples.

## <a name="NewestVersion"></a>Version la plus récente

<table border='1'>
	<tr><th>Version du connecteur Hadoop</th>
		<td>1.1.0</td></tr>
	<tr><th>URI du script</th>
		<td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v03.ps1</td></tr>
	<tr><th>Date de modification</th>
		<td>07/20/2015</td></tr>
	<tr><th>Versions de HDInsight prises en charge</th>
		<td>3.1, 3.2.</td></tr>
	<tr><th>Journal des modifications</th>
		<td>Kit de développement logiciel (SDK) Java de DocumentDB mis à jour vers la version&#160;1.1.0</br>
			Paramètre de sortie supplémentaire supprimé pour les chemins d’accès d’indexation personnalisés</br>
			Paramètre facultatif ajouté pour la précision de chaîne personnalisée (-1 par défaut)</br>
			6/11/2015</br>
			Compatibilité du connecteur corrigée avec le <a href="https://www.microsoft.com/download/details.aspx?id=40886">pilote&#160;ODBC Microsoft Hive</a></br>
			Ajout de la possibilité de modifier le type d’offre de collection de sortie (S3 par défaut)</br>
		</td></tr>
</table>

## <a name="Prerequisites"></a>Configuration requise
Avant de suivre les instructions de ce didacticiel, assurez-vous de disposer des éléments suivants :

- Un compte DocumentDB, une base de données et une collection de documents. Pour plus d’informations, consultez la rubrique [Prise en main de DocumentDB][getting-started]. Importez des exemples de données dans votre compte DocumentDB avec l’[outil d’importation de DocumentDB][documentdb-import-data].
- Débit. Les lectures et écritures à partir de HDInsight seront comptabilisées sur vos unités de demande allouées pour vos collections. Pour plus d’informations, consultez la rubrique [Débit configuré, unités de demande et opérations de base de données][documentdb-manage-throughput].
- Capacité pour une procédure stockée supplémentaire dans chaque collection de sortie. Les procédures stockées sont utilisées pour le transfert des documents résultants. Pour plus d’informations, consultez la rubrique [Collections et débit configuré][documentdb-manage-document-storage].
- Capacité pour les documents obtenus à partir des tâches Hive, Pig ou MapReduce. Pour plus d’informations, consultez la rubrique [Gestion des capacités et performances de DocumentDB][documentdb-manage-collections].
- [*Facultatif*] Capacité pour une collection supplémentaire. Pour plus d’informations, consultez la rubrique [Stockage de documents configuré et surcharge d’index][documentdb-manage-document-storage].
	
> [AZURE.WARNING]Pour empêcher la création d'une collection au cours d'une des tâches, vous pouvez imprimer les résultats vers stdout, enregistrer la sortie dans votre conteneur WASB ou sélectionner une collection existante. Si vous spécifiez une collection existante, des documents seront créés au sein de la collection et les documents existants ne seront affectés qu’en cas de conflit dans *ids*. **Le connecteur écrasera automatiquement les documents existants présentant des conflits d’ID**. Vous pouvez désactiver cette fonctionnalité en réglant l'option upsert sur false. Si l'option upsert est réglée sur false et qu'un conflit se produit, la tâche Hadoop échoue et une erreur liée à un conflit ID est renvoyée.

## <a name="CreateStorage"></a>Étape 1 : Création d’un compte Azure Storage

> [AZURE.IMPORTANT] Si vous disposez **déjà** d’un compte Azure Storage et souhaitez créer un conteneur d’objets blob dans ce compte, vous pouvez passer à l’[Étape 2 : Création d’un cluster HDInsight personnalisé](#ProvisionHDInsight).

Azure HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Il s'intitule *WASB* ou *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du HDFS sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Lors de l'approvisionnement d'un cluster HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d'objets blob spécifique de ce compte est désigné en tant que système de fichiers par défaut, comme dans HDFS. Le cluster HDInsight est approvisionné par défaut dans le même centre de données que le compte de stockage que vous spécifiez.

**Pour créer un compte Azure Storage**

1. Connectez-vous au [portail de gestion Azure][azure-classic-portal].
	
	> [AZURE.NOTE]Azure HDInsight est actuellement pris en charge dans le portail de gestion Azure tandis qu’Azure DocumentDB existe uniquement dans le portail Microsoft Azure.

2. Cliquez sur **+ NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES** et sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.
	![Portail Azure où vous pouvez utiliser l'option Création rapide pour configurer un nouveau compte de stockage.][image-storageaccount-quickcreate]

3. Entrez l’**URL**, sélectionnez les valeurs **EMPLACEMEN**T et **RÉPLICATION**, puis cliquez sur **CRÉER UN COMPTE DE STOCKAGE**. Les groupes d'affinités ne sont pas pris en charge. 
	
	Le nouveau compte de stockage figure dès lors dans la liste de stockage.

	> [AZURE.IMPORTANT] Pour obtenir des performances optimales, assurez-vous que votre compte de stockage, le cluster HDInsight et le compte DocumentDB se trouvent dans la même région Azure. Les régions Azure qui prennent en charge les trois services sont : **Asie orientale**, **Asie du Sud-Est**, **Europe du Nord**, **Europe de l’Ouest**, **Est des États-Unis** et **Ouest des États-Unis**.

4. Attendez que le **statut** du nouveau compte de stockage passe à **Online**.

## <a name="ProvisionHDInsight"></a>Étape 2 : Création d’un cluster HDInsight personnalisé
Ce didacticiel utilise une action de script à partir du portail de gestion Azure pour personnaliser votre cluster HDInsight. Dans ce didacticiel, nous allons utiliser le portail de gestion Azure pour créer votre cluster personnalisé. Pour connaître les instructions liées à l’utilisation des applets de commande PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight, consultez l’article [Personnaliser les clusters HDInsight à l’aide d’une action de script][hdinsight-custom-provision].

1. Connectez-vous au [portail de gestion Azure][azure-classic-portal]. Il est possible que vous vous soyez déjà connecté à l'étape précédente.

2. Dans la partie inférieure de la page, cliquez sur **+ NEW**, sur **DATA SERVICES**, sur **HDINSIGHT**, puis sur **CUSTOM CREATE**.

3. Sur la page **Détails du cluster**, entrez ou sélectionnez les valeurs suivantes :

	![Fournir des détails du cluster initial Hadoop HDInsight][image-customprovision-page1]

	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom du cluster</td><td>Nom du cluster.<br/>
			Le nom DNS doit commencer et finir par un caractère alphanumérique et peut contenir des traits d'union.<br/>
			Le champ doit être une chaîne comportant entre 3 et 63&#160;caractères.</td></tr>
		<tr><td>Nom d'abonnement</td>
			<td>Si vous possédez plusieurs abonnements Azure, sélectionnez l’abonnement correspondant au compte de stockage de l’<strong>étape&#160;1</strong>. </td></tr>
		<tr><td>Type du cluster</td>
			<td>Pour le type du cluster, sélectionnez <strong>Hadoop</strong>.</td></tr>
		<tr><td>Système d'exploitation</td>
			<td>Pour le système d’exploitation, sélectionnez <strong>Windows Server&#160;2012&#160;R2 Datacenter</strong>.</td></tr>
		<tr><td>Version de HDInsight</td>
			<td>Choisissez la version. </br>Sélectionnez <Strong>HDInsight version&#160;3.1</Strong>.</td></tr>
		</table>

	<p>Entrez ou sélectionnez les valeurs, comme indiqué dans le tableau, puis cliquez sur la flèche de droite.</p>

4. Sur la page **Configurer le cluster**, entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Nœuds de données</td><td>Nombre de nœuds de données que vous souhaitez déployer. </br>Notez que les nœuds de données de HDInsight sont associés aux performances et à la tarification.</td></tr>
	<tr><td>Région/Réseau virtuel</td><td>Choisissez la même région que pour le <strong>compte de stockage</strong> que vous venez de créer et votre <strong>compte DocumentDB</strong>. </br> HDInsight requiert en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu'un compte de stockage situé dans la région spécifiée ici.</td></tr>
	</table>

	Cliquez sur la flèche droite.

5. Sur la page **Configuration de l'utilisateur du cluster**, entrez les valeurs suivantes :

    <table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom d'utilisateur</td>
			<td>Spécifiez le nom d'utilisateur de cluster HDInsight.</td></tr>
		<tr><td>Mot de passe/Confirmer le mot de passe</td>
			<td>Spécifiez le mot de passe de cluster HDInsight.</td></tr>
	</table>

    Cliquez sur la flèche droite.
    
6. Sur la page **Compte de stockage**, entrez les valeurs suivantes :

	![Fournir un compte de stockage pour le cluster Hadoop HDInsight][image-customprovision-page4]

	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Compte de stockage</td>
			<td>Spécifiez le compte de stockage Azure qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Vous pouvez choisir l’une de ces trois&#160;options&#160;: Utiliser le stockage existant, Créer un stockage ou Utiliser le stockage d’un autre abonnement.</br></br>
			Sélectionnez <strong>Utiliser le stockage existant</strong>.
			</td>
			</td></tr>
		<tr><td>Nom du compte</td>
			<td>
			Pour <strong>Nom du compte</strong>, sélectionnez le compte créé à l’<strong>étape&#160;1</strong>. La liste déroulante répertorie uniquement les comptes de stockage sous le même abonnement Azure situés dans le centre de données sur lequel vous voulez approvisionner le cluster.
			</td></tr>
		<tr><td>Conteneur par défaut</td>
			<td>Permet de spécifier le conteneur par défaut du compte de stockage qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Si vous sélectionnez l’option <strong>Utiliser le stockage existant</strong> pour le champ <strong>Compte de stockage</strong> et qu’il n’existe aucun conteneur existant dans ce compte, le conteneur est créé par défaut avec le même nom que celui du cluster. Si un conteneur portant le nom du cluster existe déjà, un numéro de séquence est ajouté au nom de conteneur.
        </td></tr>
		<tr><td>Comptes de stockage supplémentaires</td>
			<td>HDInsight prend en charge plusieurs comptes de stockage. Le nombre de comptes de stockage supplémentaires pouvant être utilisés par un cluster n'est pas limité. Toutefois, si vous créez un cluster via le portail Azure, la limite est établie à sept en raison de contraintes liées à l’interface utilisateur. Chaque compte de stockage supplémentaire que vous spécifiez dans ce champ ajoute une page Compte de stockage supplémentaire vers l'Assistant vous permettant de spécifier les informations de compte.</td></tr>
	</table>

	Cliquez sur la flèche droite.

7. Dans la page **Actions de script**, cliquez sur **Ajouter une action de script** pour fournir des détails sur le script PowerShell que vous souhaitez exécuter pour personnaliser votre cluster, pendant la création du cluster. Le script PowerShell installera le connecteur Hadoop DocumentDB sur vos clusters HDInsight pendant la création des clusters.
	
	![Configuration de l’action de script pour personnaliser un cluster HDInsight][image-customprovision-page5]

	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom</td>
			<td>Indiquez un nom pour l'action de script.</td></tr>
		<tr><td>URI du script</td>
			<td>Spécifiez l'URI du script appelé pour personnaliser le cluster.</br></br>
			Veuillez entrer&#160;: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v03.ps1</strong>.</td></tr>
		<tr><td>Type de nœud</td>
			<td>Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de travail uniquement</b>.</br></br>
			Sélectionnez <strong>Tous les nœuds</strong>.</td></tr>
		<tr><td>Paramètres</td>
			<td>Spécifiez les paramètres, si le script le demande.</br></br>
			<strong>Aucun paramètre requis</strong>.</td></tr>
	</table>

	Cliquez sur la coche pour terminer la création du cluster.

## <a name="InstallCmdlets"></a>Étape 3 : Installation et configuration d’Azure PowerShell

1. Installez Azure PowerShell. Vous trouverez des instructions [ici][powershell-install-configure].

	> [AZURE.NOTE] Pour les requêtes Hive, vous pouvez également utiliser l'éditeur Hive en ligne de HDInsight. Pour ce faire, connectez-vous au [portail de gestion Azure][azure-classic-portal], cliquez sur **HDInsight** dans le volet gauche pour afficher la liste de vos clusters HDInsight. Cliquez sur le cluster sur lequel vous souhaitez exécuter des requêtes Hive, puis sur **Console de requête**.

2. Ouvrez l'environnement de script intégré Azure PowerShell :
	- Sur un ordinateur exécutant au moins Windows 8 ou Windows Server 2012, vous pouvez utiliser l’outil de recherche intégré. Dans l’écran d’accueil, tapez **powershell ise**, puis cliquez sur **Entrée**. 
	- Sur un ordinateur exécutant une version antérieure à Windows 8 ou Windows Server 2012, utilisez le menu Démarrer. Dans la zone de recherche du menu Démarrer, tapez **Invite de commandes**, puis, dans la liste des résultats, cliquez sur **Invite de commandes**. Dans l’invite de commandes, tapez **powershell_ise**, puis cliquez sur **Entrée**.

3. Ajoutez votre compte Azure.
	1. Dans le volet de la console, tapez **Add-AzureAccount**, puis cliquez sur **Entrée**. 
	2. Tapez l’adresse de messagerie associée à votre abonnement Azure, puis cliquez sur **Continuer**. 
	3. Tapez le mot de passe de votre abonnement Azure. 
	4. Cliquez sur **Se connecter**.

4. Le schéma suivant identifie les éléments importants de votre environnement de script Azure PowerShell.

	![Diagramme d’Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>Étape 4 : Exécution d’une tâche Hive à l’aide de DocumentDB et HDInsight

> [AZURE.IMPORTANT] Toutes les variables indiquées par < > doivent être renseignées à l’aide de vos paramètres de configuration.

1. Définissez les variables suivantes dans le volet Script PowerShell.

		# Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide the HDInsight cluster name where you want to run the Hive job.
		$clusterName = "<HDInsightClusterName>"

2. 
	<p>Commençons à construire votre chaîne de requête. Nous allons écrire une requête Hive qui prend les horodatages générés par le système (_ts) et les ID uniques (_rid) de tous les documents d'une collection DocumentDB, comptabilise tous les documents à la minute et stocke les résultats dans une nouvelle collection DocumentDB. </p>

    <p>Commençons par créer une table Hive à partir de notre collection DocumentDB. Ajoutez l’extrait de code suivant dans le volet Script PowerShell <strong>après</strong> l’extrait de code&#160;1. Veillez à inclure le paramètre DocumentDB.query facultatif pour réduire vos documents à _ts et _rid. </p>

    > [AZURE.NOTE]**L’attribution du nom DocumentDB.inputCollections n’était pas une erreur.** Oui, nous autorisons l’ajout de plusieurs collections en tant qu’entrée : </br>
    '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*<DocumentDB Input Collection Name 2>*' </br> Les noms de collection sont séparés sans espace, en utilisant uniquement une virgule.


		# Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
		$queryStringPart1 = "drop table DocumentDB_timestamps; "  + 
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " + 
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
 
3.  Créons maintenant une table Hive pour la collection de sortie. Les propriétés des documents de sortie seront le mois, le jour, l'heure, les minutes et le nombre total d'occurrences.

	> [AZURE.NOTE]**Une fois encore, l’attribution du nom DocumentDB.outputCollections n’était pas une erreur.** Oui, nous autorisons l’ajout de plusieurs collections en tant que sortie : </br>
    '*DocumentDB.ouputCollections*' = '*\<DocumentDB Output Collection Name 1\>*,*\<DocumentDB Output Collection Name 2\>*' </br> Les noms de collection sont séparés sans espace, en utilisant uniquement une virgule. </br></br>
    Les documents seront distribués en tourniquet (round robin), sur plusieurs collections. Un lot de documents sera stocké dans une collection, puis un deuxième lot de documents sera stocké dans la collection suivante, etc.

		# Create a Hive table for the output data to DocumentDB.
	    $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " + 
                              "tblproperties ( " + 
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Pour terminer, nous allons calculer les documents par mois, jour, heure et minutes et insérer les résultats dans la table Hive de sortie.

		# GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Ajoutez l'extrait de script suivant pour créer une définition de tâche Hive à partir de la requête précédente.

		# Create a Hive job definition.
		$queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	Vous pouvez également utiliser le commutateur -File pour spécifier un fichier de script HiveQL sur HDFS.

6. Ajoutez l'extrait suivant pour enregistrer l'heure de début et soumettre la tâche Hive.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Ajoutez le script suivant pour attendre la fin de la tâche Hive.

		# Wait for the Hive job to complete.
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Ajoutez le code suivant pour imprimer la sortie standard et les heures de début et de fin.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Exécutez** votre nouveau script ! **Cliquez** sur le bouton d’exécution vert.

10. Vérifiez les résultats. Connectez-vous au [portail Azure en version préliminaire][azure-portal].
	1. Cliquez sur <strong>Parcourir</strong> dans le panneau gauche. </br>
	2. Cliquez sur <strong>Tout</strong> en haut à droite du volet de navigation. </br>
	3. Recherchez les <strong>comptes DocumentDB</strong> et cliquez dessus. </br>
	4. Recherchez ensuite votre <strong>compte DocumentDB</strong>, puis votre <strong>base de données DocumentDB</strong> et votre <strong>collection DocumentDB</strong> associés à la collection de sortie spécifiée dans votre requête Hive.</br>
	5. Pour finir, cliquez sur <strong>Explorateur de documents</strong> sous <strong>Outils de développement</strong>.</br></p>

	Vous verrez les résultats de votre requête Hive.

	![Résultats de la requête Hive][image-hive-query-results]

## <a name="RunPig"></a>Étape 5 : Exécution d’une tâche Pig à l’aide de DocumentDB et HDInsight

> [AZURE.IMPORTANT] Toutes les variables indiquées par < > doivent être renseignées à l’aide de vos paramètres de configuration.

1. Définissez les variables suivantes dans le volet Script PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>Commençons à construire votre chaîne de requête. Nous allons écrire une requête Pig qui accepte les horodatages générés par le système de tous les documents (DTS) et des identificateurs uniques (_rid) à partir d'une collection DocumentDB, comptabilise tous les documents à la minute, puis stocke les résultats de la sauvegarde dans une nouvelle collection DocumentDB.</p>
    <p>Chargez d'abord des documents DocumentDB dans HDInsight. Ajoutez l’extrait de code suivant dans le volet Script PowerShell <strong>après</strong> l’extrait de code&#160;1. Veillez à ajouter une requête DocumentDB au paramètre de requête DocumentDB facultatif pour réduire vos documents à _ts et _rid.</p>

    > [AZURE.NOTE]Oui, nous autorisons l’ajout de plusieurs collections en tant qu’entrée : </br>
    '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' </br> Les noms de collection sont séparés sans espace, en utilisant uniquement une virgule. </b>

	Les documents seront distribués en séquence, sur plusieurs collections. Un lot de documents sera stocké dans une collection, puis un deuxième lot de documents sera stocké dans la collection suivante, etc.

		# Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Nous allons ensuite calculer les documents par mois, jour, heure et minutes et le nombre total d'occurrences.

		# GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Pour terminer, nous allons stocker les résultats dans notre nouvelle collection de sortie.

    > [AZURE.NOTE] Oui, nous autorisons l’ajout de plusieurs collections en tant que sortie: </br>
    '\<DocumentDB Output Collection Name 1\>,\<DocumentDB Output Collection Name 2\>' </br> Les noms de collection sont séparés sans espace, en utilisant uniquement une virgule. </br>
    Les documents seront distribués en séquence, sur plusieurs collections. Un lot de documents sera stocké dans une collection, puis un deuxième lot de documents sera stocké dans la collection suivante, etc.

		# Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Ajoutez l'extrait de script suivant pour créer une définition de tâche Pig à partir de la requête précédente.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

	Vous pouvez également utiliser le commutateur -File pour spécifier un fichier de script Pig sur HDFS.

6. Ajoutez l'extrait suivant pour enregistrer l'heure de début et soumettre la tâche Pig.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Ajoutez le script suivant pour attendre la fin de la tâche Pig.

		# Wait for the Pig job to complete.
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Ajoutez le code suivant pour imprimer la sortie standard et les heures de début et de fin.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
		
9. **Exécutez** votre nouveau script ! **Cliquez** sur le bouton d’exécution vert.

10. Vérifiez les résultats. Connectez-vous au [portail Azure en version préliminaire][azure-portal].
	1. Cliquez sur <strong>Parcourir</strong> dans le panneau gauche. </br>
	2. Cliquez sur <strong>Tout</strong> en haut à droite du volet de navigation. </br>
	3. Recherchez les <strong>comptes DocumentDB</strong> et cliquez dessus. </br>
	4. Recherchez ensuite votre <strong>compte DocumentDB</strong>, puis votre <strong>base de données DocumentDB</strong> et votre <strong>collection DocumentDB</strong> associés à la collection de sortie spécifiée dans votre requête Pig.</br>
	5. Pour finir, cliquez sur <strong>Explorateur de documents</strong> sous <strong>Outils de développement</strong>.</br></p>

	Vous verrez les résultats de votre requête Pig.

	![Résultats de la requête Pig][image-pig-query-results]

## <a name="RunMapReduce"></a>Étape 6 : Exécution d’une tâche MapReduce à l’aide de DocumentDB et HDInsight

1. Définissez les variables suivantes dans le volet Script PowerShell.
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
2. Nous allons exécuter une tâche MapReduce qui compte le nombre d'occurrences de chaque propriété de document de votre collection DocumentDB. Ajoutez cet extrait de script **après** l’extrait ci-dessus.

		# Define the MapReduce job.
		$TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

	> [AZURE.NOTE]TallyProperties-v01.jar est fourni avec l'installation personnalisée du connecteur Hadoop DocumentDB.

3. Ajoutez la commande suivante pour soumettre la tâche MapReduce.

		# Save the start time and submit the job.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	En plus de la définition de la tâche MapReduce, fournissez également le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce, ainsi que les informations d'identification. Start-AzureHDInsightJob est un appel asynchrone. Pour vérifier que la tâche est terminée, utilisez la cmdlet *Wait-AzureHDInsightJob*.

4. Ajoutez la commande suivante pour déterminer si l'exécution de la tâche MapReduce génère des erreurs.
	
		# Get the job output and print the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green 

5. **Exécutez** votre nouveau script ! **Cliquez** sur le bouton d’exécution vert.

6. Vérifiez les résultats. Connectez-vous au [portail Azure en version préliminaire][azure-portal].
	1. Cliquez sur <strong>Parcourir</strong> dans le panneau gauche.
	2. Cliquez sur <strong>Tout</strong> en haut à droite du volet de navigation.
	3. Recherchez les <strong>comptes DocumentDB</strong> et cliquez dessus.
	4. Recherchez ensuite votre <strong>compte DocumentDB</strong>, puis votre <strong>base de données DocumentDB</strong> et votre <strong>collection DocumentDB</strong> associés à la collection de sortie spécifiée dans votre tâche MapReduce.
	5. Pour finir, cliquez sur <strong>Explorateur de documents</strong> sous <strong>Outils de développement</strong>.

	Vous verrez les résultats de votre tâche MapReduce.

	![Résultats de la requête MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Étapes suivantes

Félicitations ! Vous venez d'exécuter vos premières tâches Hive, Pig et MapReduce à l'aide d'Azure DocumentDB et HDInsight.

Le code source de notre connecteur Hadoop est disponible gratuitement. Si vous êtes intéressé, vous pouvez apporter votre contribution sur [GitHub][documentdb-github].

Pour en savoir plus, consultez les articles suivants :

- [Développement d’une application Java avec DocumentDB][documentdb-java-application]
- [Développement de programmes MapReduce en Java pour Hadoop dans HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Prise en main de Hadoop avec Hive dans HDInsight pour analyser l’utilisation des téléphones mobiles][hdinsight-get-started]
- [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce]
- [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
- [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
- [Personnaliser des clusters HDInsight à l'aide d'une action de script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-classic-portal]: https://manage.windowsazure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png
[azure-portal]: https://portal.azure.com/

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-customprovision-page4]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page4.png
[image-customprovision-page5]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page5.png
[image-storageaccount-quickcreate]: ./media/documentdb-run-hadoop-with-hdinsight/storagequickcreate.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../install-configure-powershell.md
 

<!-----HONumber=August15_HO6-->