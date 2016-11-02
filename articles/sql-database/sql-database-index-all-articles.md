<properties
    pageTitle="Toutes les rubriques relatives au service Base de données SQL Azure | Microsoft Azure"
    description="Tableau de toutes les rubriques du service Azure nommé Base de données SQL qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>



# <a name="all-topics-for-azure-sql-database-service"></a>Toutes les rubriques relatives au service Base de données SQL Azure

Cette rubrique répertorie toutes les rubriques qui s’appliquent directement au service de **Base de données SQL** d’Azure. Pour trouver les rubriques qui vous intéressent, vous pouvez rechercher des mots clés sur cette page web à l'aide de la combinaison de touches **Ctrl+F**.




## <a name="new"></a>Nouveau

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 1 | [Daxko/CSI a utilisé Azure pour accélérer son cycle de développement et améliorer ses performances et ses services clients](sql-database-implementation-daxko.md) | Découvrez comment Daxko/CSI utilise SQL Database pour accélérer son cycle de développement et améliorer ses performances et ses services clients |
| 2 | [Azure donne à GEP une portée mondiale et une plus grande efficacité](sql-database-implementation-gep.md) | Découvrez comment GEP utilise SQL Database pour atteindre plus de clients à l’international et gagner en efficacité |
| 3 | [Avec Azure, SnelStart a rapidement développé ses services d’entreprise au rythme de 1 000 nouvelles bases de données Azure SQL Database par mois](sql-database-implementation-snelstart.md) | Découvrez comment SnelStart s’est appuyé sur SQL Database pour développer rapidement ses services d’entreprise au rythme de 1 000 nouvelles bases de données Azure SQL Database par mois |
| 4 | [Umbraco utilise Azure SQL Database afin d’approvisionner rapidement des services et de les mettre à l’échelle pour des milliers de locataires dans le cloud](sql-database-implementation-umbraco.md) | Découvrez comment Umbraco utilise SQL Database afin d’approvisionner rapidement des services et de les mettre à l’échelle pour des milliers de locataires dans le cloud |
| 5 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-manage-powershell.md) | Gestion d’Azure SQL Database avec PowerShell. |
| 6 | [Prise en charge de SSMS pour Azure AD MFA avec la base de données SQL et SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) | Utilisez l’authentification multi-facteur avec SSMS pour la base de données SQL et SQL Data Warehouse. |
| 7 | [Explication des unités de transaction de base de données (DTU) et des unités de transaction de base de données élastique (eDTU)](sql-database-what-is-a-dtu.md) | Compréhension du rôle d’une unité de transaction de base de données SQL. |


## <a name="updated-articles,-sql-database"></a>Articles mis à jour, base de données SQL Database

Cette section répertorie les articles qui ont été mis à jour récemment et dont la mise à jour a été volumineuse ou importante. Pour chaque article mis à jour, un extrait de code approximatif du texte markdown ajouté s’affiche. Les articles ont été mis à jour dans la plage de dates allant du **2016-08-22** au **2016-10-05**.

| &nbsp; | Article | Texte mis à jour, extrait de code | Date de mise à jour |
| --: | :-- | :-- | :-- |
| 8 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-command-line-tools.md) | Créez un groupe de ressources pour notre base de données SQL Database et les ressources Azure associées avec l’applet de commande New-AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx). * $resourceGroupName = "resourcegroup1" $resourceGroupLocation = "northcentralus" New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation * Pour plus d’informations, consultez la rubrique Utilisation d’Azure PowerShell avec Azure Resource Manager (../powershell-azure-resource-manager.md). Pour obtenir un exemple de script, consultez la rubrique Créer un script PowerShell de base de données SQL (sql-database-get-started-powershell.md create-a-sql-database-powershell-script). ** Créer un serveur SQL Database** Créez un serveur SQL Database avec l’applet de commande New-AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx). Remplacez *server1* par le nom de votre serveur. Le nom doit être unique pour tous les serveurs Azure SQL Database. Vous obtiendrez une erreur si le nom est déjà utilisé pour un autre serveur. Cette commande peut prendre plusieurs minutes. La ressource | 2016-09-14 |
| 9 | [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) |   Source SQL Database (la base de données existante à copier)  --  $sourceDbName = "db1"  $sourceDbServerName = "server1"  $sourceDbResourceGroupName = "rg1"  Copie SQL Database (la nouvelle base de données à créer)  --  $copyDbName = "db1_copy"  $copyDbServerName = "server2"  $copyDbResourceGroupName = "rg2"  Copier une base de données sur le même serveur  --  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName  Copier une base de données sur un autre serveur  --  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName  Copier une base de données sur un pool de base de données élastique  --  $poolName = "pool1"  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyReso | 2016-09-08 |
| 10 | [Créer un pool de bases de données élastique avec C#](sql-database-elastic-pool-create-csharp.md) |   Console.WriteLine("Server firewall...");  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);  Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);  Console.WriteLine("Elastic pool...");  ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);  Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);  Console.WriteLine("Database...");  DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);  Console.WriteLine("Database: " + dbr.Database.Id);  Console.WriteLine("Press any key to continue...");  Console.ReadKey();  }  static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa | 2016-09-14 |
| 11 | [Script PowerShell pour identifier les bases de données adaptées à un pool de bases de données élastique](sql-database-elastic-pool-database-assessment-powershell.md) | ** Pour les candidats de pool de base de données élastique, nous excluons la base de données master et toutes les bases de données qui se trouvent déjà dans un pool. Vous pouvez ajouter d’autres bases de données à la liste ci-dessous en fonction de vos besoins** $ListOfDBs = Get-AzureRmSqlDatabase -ServerName $servername.Split('.') 0  -ResourceGroupName $ResourceGroupName  /  Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")} $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword" $destinationTableName = "resource_stats_output" ** Créer une table dans la base de données de sortie pour la collecte des mesures** $sql = " IF  NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U')) BEGIN Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_ | 2016-09-29 |
| 12 | [Didacticiel sur SQL Database : Créer une base de données SQL Database en quelques minutes à l’aide du portail Azure](sql-database-get-started.md) |   ! Nouvelle base de données SQL Database 1 (. / media/sql-database-get-started/sql-database-new-database-1.png) 3. Cliquez sur **SQL Database** pour ouvrir le panneau SQL Database. Le contenu de ce panneau varie selon le nombre de vos abonnements et vos objets existants (tels que les serveurs existants).  ! Nouvelle base de données SQL Database 2 (. / media/sql-database-get-started/sql-database-new-database-2.png) 4. Dans la zone de texte **Nom de la base de données** , entrez un nom pour votre première base de données, tel que « ma-base-de-données ». Une coche verte indique que vous avez fourni un nom valide.  ! Nouvelle base de données SQL Database 3 (. / media/sql-database-get-started/sql-database-new-database-3.png) 5. Si vous avez plusieurs abonnements, sélectionnez-en un. 6. Sous **Groupe de ressources**, cliquez sur **Créer** et fournissez un nom pour votre premier groupe de ressources, tel que « mon-groupe-de-ressources ». Une coche verte indique que vous avez fourni un nom valide.  ! Nouvelle base de données SQL Database 4 (. / media/sql-database-get-started/sql-database-new-database-4.png) 7. Sous **Sélectionner une source* | 2016-09-08 |
| 13. | [Essayer la Base de données SQL : Utiliser C# pour créer une Base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md) | ** Créer un principal du service pour accéder aux ressources** Le script PowerShell suivant crée l’application Active Directory (AD) et le principal du service dont nous avons besoin pour authentifier notre application C. Le script génère les valeurs dont nous avons besoin pour l’exemple C  précédent. Pour plus de détails, consultez la page Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell (../resource-group-authenticate-service-principal.md).  Connectez-vous à Azure.  Add-AzureRmAccount  Si vous disposez de plusieurs abonnements, supprimez les commentaires et définissez l’abonnement que vous souhaitez utiliser.  $subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"  Set-AzureRmContext -SubscriptionId $subscriptionId  Fournissez ces valeurs pour votre nouvelle application AAD.  $appName est le nom d’affichage de votre application ; il doit être unique dans votre répertoire.  $uri n’a pas besoin d’être une uri réelle.  $secret est un mot de passe que vous créez.  $appName = "{app-name}"  $uri = "http://{app-name}"  $secret = "{app-password}"  Créer une application AAD  $azureAdApplication = New-AzureRmADApp | 2016-09-23 |
| 14 | [Gestion des bases de données SQL Azure au moyen du portail Azure](sql-database-manage-portal.md) | Pour afficher ou mettre à jour vos paramètres de base de données, cliquez sur le paramètre souhaité dans le panneau Base de données SQL : ! Paramètres SQL Database (./ media/sql-database-manage-portal/settings.png) ** Comment rechercher le nom complet du serveur d’une base de données SQL Database ?** Pour afficher le nom de votre serveur de bases de données, cliquez sur **Vue d’ensemble** dans le panneau **Base de données SQL**. Notez le nom du serveur : ! Paramètres SQL Database (./ media/sql-database-manage-portal/server-name.png) ** Comment gérer les règles de pare-feu pour contrôler l’accès à mon serveur et à ma base de données SQL ?** Pour afficher, créer ou mettre à jour les règles de pare-feu, cliquez sur **Définir le pare-feu du serveur** dans le panneau **Base de données SQL**. Pour plus d’informations, voir Configurer une règle de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure (sql-database-configure-firewall-settings.md). ! règles de pare-feu (./media/sql-database-manage-portal/sql-database-firewall.png) ** Comment modifier le niveau de service ou de performances de ma base de données SQL Database ?** Pour mettre à jour le niveau de service ou de performances d’une base de données SQL, cliquez sur **Niveau tarifaire ( | 2016-09-20 |





## <a name="get-started"></a>Prise en main

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 15 | [Créer des applications mutualisées isolées et efficaces avec la base de données SQL Azure](sql-database-build-multi-tenant-apps.md) | Découvrez comment créer des applications mutualisées avec le service Base de données SQL Azure |
| 16 | [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md) | Découvrez comment vous connecter à Base de données SQL sur Azure avec SQL Server Management Studio (SSMS). Ensuite, exécutez un exemple de requête à l’aide de Transact-SQL (T-SQL). |
| 17 | [Connexion à SQL Database à l’aide de .NET (C#)](sql-database-develop-dotnet-simple.md) | Utilisez l'exemple de code dans cette prise en main pour créer une application moderne avec C# et soutenue par une base de données relationnelle puissante dans le cloud avec la base de données SQL Azure. |
| 18 | [Créer un pool de base de données élastique avec le portail Azure](sql-database-elastic-pool-create-portal.md) | Comment ajouter un pool de base de données élastique évolutive à votre configuration de base de données SQL pour faciliter l’administration et le partage des ressources entre plusieurs bases de données. |
| 19 | [Exploration des didacticiels relatifs à la base de données SQL Azure](sql-database-explore-tutorials.md) | En savoir plus sur les fonctions et capacités de la base de données SQL |
| 20 | [Didacticiel sur SQL Database : Créer une base de données SQL Database en quelques minutes à l’aide du portail Azure](sql-database-get-started.md) | Découvrez comment configurer un serveur logique SQL Database, une règle de pare-feu de serveur, une base de données SQL et des exemples de données. Apprenez également à vous connecter avec les outils clients, à configurer des utilisateurs et à définir une règle de pare-feu de base de données. |
| 21 | [Sécurité et protection avec la base de données SQL Azure](sql-database-helps-secures-and-protects.md) | Découvrez comment le service Base de données SQL renforce la sécurité et la protection des données |
| 22 | [Le service Base de données SQL Azure apprend &amp; s’adapte](sql-database-learn-and-adapt.md) | Découvrez comment le service Base de données SQL Azure apprend et s’adapte |
| 23 | [Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Découvrez l’option SQL Server cloud adaptée à votre application : Azure SQL (PaaS) Database ou SQL Server sur les machines virtuelles Azure. |
| 24 | [Mises à l’échelle instantanées de bases de données SQL Azure](sql-database-scale-on-the-fly.md) | Apprenez comment mettre à l’échelle de manière instantanée les bases de données SQL |
| 25 | [Explorer les démarrages rapides de la solution de base de données SQL Azure](sql-database-solution-quick-starts.md) | Découvrez les solutions de base de données SQL Azure |
| 26 | [Base de données SQL Azure adaptée à votre environnement](sql-database-works-in-your-environment.md) | Découvrez comment les bases de données SQL améliorent, sécurisent et protègent vos données |



## <a name="build-an-app"></a>Générer une application

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 27 | [Diagnostiquer, résoudre et empêcher les erreurs de connexion SQL et les erreurs temporaires de Base de données SQL](sql-database-connectivity-issues.md) | Découvrez comment diagnostiquer, résoudre et empêcher une erreur de connexion SQL ou une erreur temporaire dans Base de données SQL Azure.  |
| 28 | [Se connecter à la base de données SQL avec Visual Studio](sql-database-connect-query.md) | Écrivez un programme en C# pour interroger et vous connecter à une base de données SQL. Informations sur les adresses IP, chaînes de connexion, connexion sécurisée et Visual Studio gratuit. |
| 29 | [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md) | Parfois, les connexions clientes entre ADO.NET et Azure SQL Database V12 ignorent le proxy et interagissent directement avec la base de données. Les ports autres que le port 1433 deviennent importants. |
| 30 | [Codes d’erreur SQL pour les applications clientes SQL Database : erreur de connexion à la base de données et autres problèmes](sql-database-develop-error-messages.md) | En savoir plus sur les codes d’erreur SQL pour les applications clientes SQL Database, tels que les erreurs de connexion de base de données courantes, les problèmes de copie de base de données et les erreurs générales.  |
| 31 | [Connexion à la base de données SQL à l'aide de PHP sous Windows](sql-database-develop-php-simple.md) | Présente un exemple de programme PHP qui se connecte à la base de données SQL Azure à partir d'un client Windows et fournit des liens vers les composants logiciels nécessaires requis par le client. |
| 32 | [Essayer la Base de données SQL : Utiliser C# pour créer une Base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md) | Essayez la base de données SQL pour développer des applications SQL et C# et créez une base de données SQL Azure avec C# à l’aide de la bibliothèque de base de données SQL pour .NET. |
| 33 | [Prise en main des fonctionnalités JSON dans Base de données SQL Azure](sql-database-json-features.md) | Base de données SQL Azure vous permet d’analyser, d’interroger et de mettre en forme des données dans une notation JavaScript Object Notation (JSON). |
| 34 | [Résoudre des problèmes de connexion à la base de données SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Opérations servant à identifier et résoudre les erreurs de connexion courantes pour Azure SQL Database. |
| 35 | [Erreur « La base de données sur le serveur n’est pas disponible actuellement » lors de la connexion à SQL Database](sql-database-troubleshoot-connection.md) | Dépannage de l’erreur « La base de données sur le serveur n’est pas disponible actuellement » quand une application se connecte à SQL Database. |



## <a name="develop"></a>Développement

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 36 | [Obtention des valeurs requises pour l’authentification d’une application en vue d’accéder à une base de données SQL à partir du code](sql-database-client-id-keys.md) | Créez un principal du service pour l’accès à la base de données SQL à partir du code. |
| 37 | [Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows](sql-database-develop-java-simple.md) | Cette rubrique présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L'exemple utilise JDBC et s'exécute sur un ordinateur client Windows. |
| 38 | [Connexion à SQL Database à l’aide de Node.js](sql-database-develop-nodejs-simple.md) | Cette rubrique présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. |
| 39 | [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) | En savoir plus sur les bibliothèques de connectivité disponibles et les meilleures pratiques pour les applications qui utilisent une connexion à la base de données SQL. |
| 40 | [Connexion à SQL Database à l’aide de Python](sql-database-develop-python-simple.md) | Cette rubrique présente un exemple de code Python que vous pouvez utiliser pour vous connecter à une base de données SQL Azure. |
| 41 | [Connexion à SQL Database à l’aide de Ruby](sql-database-develop-ruby-simple.md) | Offre un exemple de code Ruby que vous pouvez exécuter pour vous connecter à la base de données SQL Azure. |
| 42 | [Prise en main des tables temporelles dans Azure SQL Database](sql-database-temporal-tables.md) | Découvrez comment prendre en main les tables temporelles dans Azure SQL Database. |



## <a name="performance-and-scale"></a>Performances et mise à l’échelle

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 43 | [SQL Database Advisor](sql-database-advisor.md) | Azure SQL Database Advisor fournit des recommandations pour vos bases de données SQL existantes afin d’améliorer les performances actuelles des requêtes. |
| 44 | [SQL Database Advisor avec le portail Azure](sql-database-advisor-portal.md) | Vous pouvez utiliser Azure SQL Database Advisor dans le portail Azure afin d’examiner et d’implémenter des recommandations pour vos bases de données SQL existantes et améliorer ainsi les performances actuelles des requêtes. |
| 45 | [Vue d’ensemble du test d’évaluation de la base de données SQL Azure](sql-database-benchmark-overview.md) | Cette rubrique décrit le test d’évaluation de Base de données SQL Azure utilisé pour mesurer les performances de Base de données SQL Azure. |
| 46 | [Quand utiliser un pool de base de données élastique ?](sql-database-elastic-pool-guidance.md) | Un pool de bases de données élastique est un ensemble de ressources disponibles partagé par un groupe de bases de données élastiques. Ce document fournit des conseils pour vous aider à évaluer la pertinence de l'utilisation d'un pool de bases de données élastique pour un groupe de bases de données. |
| 47 | [Prise en main des outils de base de données élastiques](sql-database-elastic-scale-get-started.md) | Explication de base de la fonctionnalité des outils de bases de données élastiques d’Azure SQL Database, y compris un exemple d’application simple à exécuter. |
| 48 | [Forum Aux Questions de base de données SQL](sql-database-faq.md) | Réponses fréquemment posées sur les bases de données du cloud et Azure SQL Database, le système de gestion de base de données relationnelle Microsoft (SGBDR) et la base de données en tant que service dans cloud. |
| 49 | [Prise en main de In-Memory (version préliminaire) dans la base de données SQL](sql-database-in-memory.md) | Les technologies SQL In-Memory améliorent considérablement les performances des charges de travail transactionnelles et analytiques. Apprenez à tirer parti de ces technologies. |
| 50 | [Utiliser OLTP en mémoire (version préliminaire) pour améliorer les performances de votre base de données SQL](sql-database-in-memory-oltp-migration.md) | Adopter In-Memory OLTP pour améliorer les performances transactionnelles dans une base de données SQL existante. |
| 51 | [Surveiller le stockage OLTP In-Memory](sql-database-in-memory-oltp-monitoring.md) | Estimer et surveiller la capacité et l’utilisation du stockage en mémoire XTP ; résoudre l’erreur de capacité 41823 |
| 52 | [Utilisation du magasin de requêtes dans la base de données SQL Azure](sql-database-operate-query-store.md) | Découvrez comment utiliser le magasin de requêtes dans la base de données SQL Azure |
| 53 | [Informations sur les performances des bases de données SQL](sql-database-performance.md) | La base de données SQL Azure fournit des outils de performances pour vous aider à identifier les zones susceptibles d’améliorer les performances actuelles des requêtes. |
| 54 | [Azure SQL Database et performances pour les bases de données uniques](sql-database-performance-guidance.md) | Cet article vous aide à déterminer le niveau de service adapté à votre application. Il vous présente également des moyens de paramétrer votre application pour tirer le meilleur parti de votre instance Azure SQL Database. |
| 55 | [Query Performance Insight pour base de données SQL Azure](sql-database-query-performance.md) | La surveillance des performances des requêtes identifie les requêtes consommant le plus d’UC pour une base de données SQL Azure. |
| 56 | [Modifier les niveaux de service et de performances (niveau tarifaire) d’une base de données SQL](sql-database-scale-up.md) | Cet article explique comment faire monter ou descendre en puissance une base de données SQL. Modification du niveau de tarification d’une base de données SQL Azure |
| 57 | [Analyse des performances d’une base de données dans une base de données SQL Azure](sql-database-single-database-monitor.md) | Découvrez les options d’analyse de votre base de données à l’aide des outils Azure et des vues de gestion dynamique. |
| 58 | [Conseils pour le réglage des performances de SQL Database](sql-database-troubleshoot-performance.md) | Conseils pour le réglage des performances dans Azure SQL Database par le biais de l’évaluation et de l’amélioration. |
| 59 | [Comment utiliser le traitement par lots pour améliorer les performances des applications de base de données SQL](sql-database-use-batching-to-improve-performance.md) | Cette rubrique explique comment le traitement par lots des opérations de base de données contribue à améliorer considérablement la rapidité et l’évolutivité de vos applications de Base de données SQL Azure. Bien que ces techniques de traitement par lot fonctionnent pour les bases de données SQL Server, cet article porte exclusivement sur Azure. |



## <a name="tools-for-scaling-out"></a>Outils pour la montée en charge

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 60 | [Modèles de conception pour les applications SaaS mutualisées et Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | Cet article décrit la configuration requise et les modèles d’architecture de données les plus courants pour les applications de base de données SaaS mutualisées s’exécutant dans un environnement cloud, ainsi que les compromis associés à ces modèles. Il explique également comment Base de données SQL Azure, avec ses outils et ses pools élastiques, permet de satisfaire ces exigences sans aucun compromis. |
| 61 | [Migration de bases de données existantes pour une mise à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md) | Conversion de bases de données partitionnées pour utiliser les outils de base de données élastique en créant un gestionnaire de cartes de partitions |
| 62 | [Conception de bases de données cloud évolutives](sql-database-elastic-database-client-library.md) | Créez des applications de base de données .NET évolutives avec la bibliothèque cliente de bases de données élastiques |
| 63 | [Compteurs de performances pour le Gestionnaire de cartes de partitions](sql-database-elastic-database-perf-counters.md) | Classe ShardMapManager et compteurs de performances pour le routage dépendant des données |
| 64 | [Ajout d’une partition à l’aide des outils de base de données élastique](sql-database-elastic-scale-add-a-shard.md) | Utilisation des API avec infrastructure élastique pour ajouter de nouvelles partitions à un ensemble de partitions. |
| 65 | [Déployer un service de fractionnement et de fusion](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Fractionnement et fusion avec les outils de bases de données élastiques |
| 66 | [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) | Utilisation de la classe ShardMapManager dans des applications .NET pour le routage dépendant des données, une fonctionnalité de base de données élastique pour la base de données SQL Azure |
| 67 | [FAQ sur les outils de bases de données élastiques](sql-database-elastic-scale-faq.md) | Frequently Asked Questions about Azure SQL Database Elastic Scale. |
| 68 | [Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md) | Explication des termes utilisés pour les outils de base de données élastique |
| 69 | [Montée en charge avec Base de données SQL Azure](sql-database-elastic-scale-introduction.md) | Les développeurs de Software as a Service (SaaS) peuvent facilement créer des bases de données élastiques et évolutives dans le cloud à l'aide de ces outils |
| 70 | [Informations d’identification utilisées pour accéder à la bibliothèque cliente de la base de données élastique](sql-database-elastic-scale-manage-credentials.md) | Définition du niveau d’informations d’identification, de celui d’administrateur à celui de la lecture seule, pour les applications de base de données élastique. |
| 71 | [Requête sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md) | Exécutez des requêtes en utilisant la bibliothèque cliente des bases de données élastiques. |
| 72 | [Déplacement de données entre des bases de données cloud mises à l’échelle](sql-database-elastic-scale-overview-split-and-merge.md) | Explique comment manipuler les partitions et déplacer les données via un service auto-hébergé, à l'aide des API de base de données élastique. |
| 73 | [Monter en charge les bases de données avec le Gestionnaire de cartes de partitions](sql-database-elastic-scale-shard-map-management.md) | Utilisation de ShardMapManager, la bibliothèque cliente de base de données élastique |
| 74 | [Configuration de la sécurité du fractionnement et de la fusion](sql-database-elastic-scale-split-merge-security-configuration.md) | Définissez les certificats x 409 pour le chiffrement |
| 75 | [Mettre à niveau une application pour utiliser la dernière version de la bibliothèque cliente de bases de données élastiques](sql-database-elastic-scale-upgrade-client-library.md) | Mettre à niveau des applications et des bibliothèques à l’aide de Nuget |
| 76 | [Bibliothèque cliente de la base de données élastique avec Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Utilisez la bibliothèque cliente de la base de données élastique et Entity Framework pour le codage de bases de données |
| 77 | [Utilisation de la bibliothèque cliente de la base de données élastique avec Dapper](sql-database-elastic-scale-working-with-dapper.md) | Utilisation de la bibliothèque cliente de la base de données élastique avec Dapper. |
| 78 | [Applications multi-locataires avec des outils de base de données élastique et la sécurité au niveau des lignes](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Découvrez comment utiliser les outils de base de données élastique avec la fonction de sécurité au niveau des lignes (RLS) pour générer une application présentant une couche Données hautement évolutive sur la base de données SQL Microsoft Azure qui prend en charge les partitions multi-locataires. |



## <a name="elastic-jobs"></a>Tâches élastiques

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 79 | [Créer et gérer des bases de données SQL Azure avec montée en charge à l’aide de tâches élastiques (version préliminaire)](sql-database-elastic-jobs-create-and-manage.md) | Passez en revue la création et la gestion d'une tâche de base de données élastique. |
| 80 | [Prise en main de Tâches de bases de données élastiques](sql-database-elastic-jobs-getting-started.md) | Utilisation des tâches de bases de données élastiques |
| 81 | [Gestion des bases de données cloud avec montée en charge](sql-database-elastic-jobs-overview.md) | Illustre le service de tâche de base de données élastique |
| 82 | [Création et gestion des tâches de bases de données SQL élastiques à l'aide de PowerShell (version préliminaire)](sql-database-elastic-jobs-powershell.md) | Gérer la base de données SQL Azure avec PowerShell |
| 83 | [Vue d’ensemble de l’installation de Tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md) | Passez en revue l'installation de la fonctionnalité de tâche élastique. |
| 84 | [Désinstallation des composants de Tâches de bases de données élastiques.](sql-database-elastic-jobs-uninstall.md) | Désinstaller l’outil de tâche de base de données élastique |



## <a name="elastic-pools"></a>Pools élastiques

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 85 | [Qu’est-ce qu’un pool élastique Azure ?](sql-database-elastic-pool.md) | Gérez des centaines voire des milliers de bases de données à l’aide d’un pool. Un seul prix pour un ensemble d’unités de performances peut être distribué dans le pool. Déplacez les bases de données à votre convenance. |
| 86 | [Créer un pool de bases de données élastique avec C#](sql-database-elastic-pool-create-csharp.md) | Utilisez les techniques de développement de bases de données C# pour créer un pool de base de données élastique évolutif dans la base de données SQL Azure afin de pouvoir partager des ressources entre plusieurs bases de données. |
| 87 | [Créer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md) | Découvrez comment utiliser PowerShell pour augmenter la taille des instances de ressources de Base de données SQL Azure en créant un pool de base de données élastique évolutif afin de gérer plusieurs bases de données. |
| 88 | [Script PowerShell pour identifier les bases de données adaptées à un pool de bases de données élastique](sql-database-elastic-pool-database-assessment-powershell.md) | Un pool de bases de données élastique est un ensemble de ressources disponibles partagé par un groupe de bases de données élastiques. Ce document fournit un script PowerShell pour vous aider à évaluer la pertinence de l’utilisation d’un pool de bases de données élastique pour un groupe de bases de données. |
| 89 | [Surveiller et gérer un pool de bases de données élastique avec C#](sql-database-elastic-pool-manage-csharp.md) | Utilisez les techniques de développement de bases de données C# pour gérer un pool de bases de données élastique Azure SQL Database. |
| 90 | [Surveiller et gérer un pool de bases de données élastique avec le portail Azure](sql-database-elastic-pool-manage-portal.md) | Découvrez comment utiliser le portail Azure et l’intelligence intégrée de la base de données SQL pour gérer, surveiller et redimensionner un pool de bases de données élastique évolutif pour optimiser les performances de la base de données et gérer les coûts. |
| 91 | [Surveiller et gérer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-manage-powershell.md) | Découvrez comment utiliser PowerShell pour gérer un pool de bases de données élastique. |
| 92 | [Surveiller et gérer un pool de base de données élastique avec Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Utilisez T-SQL pour créer une base de données SQL Azure dans un pool élastique. Ou utilisez T-SQL pour déplacer la base de données dans et en dehors des pools. |
| 93 | [Informations sur la tarification et la facturation du pool de base de données élastique](sql-database-elastic-pool-price.md) | Informations de tarification propres aux pools de bases de données élastiques. |



## <a name="elastic-query"></a>Requête élastique

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 94 | [Créer des rapports sur des bases de données cloud avec montée en charge (version préliminaire)](sql-database-elastic-query-getting-started.md) | comment utiliser des requêtes entre plusieurs bases de données |
| 95 | [Prise en main des requêtes de bases de données croisées (partitionnement vertical) (version préliminaire)](sql-database-elastic-query-getting-started-vertical.md) | comment utiliser une requête de base de données élastique avec des bases de données partitionnées verticalement |
| 96 | [Création de rapports sur des bases de données cloud mises à l’échelle (version préliminaire)](sql-database-elastic-query-horizontal-partitioning.md) | comment configurer des requêtes élastiques sur les partitions horizontales |
| 97 | [Vue d’ensemble de la requête de base de données élastique Azure SQL Database (version préliminaire)](sql-database-elastic-query-overview.md) | Vue d’ensemble de la fonctionnalité de requête élastique |
| 98 | [Interroger des bases de données cloud de schémas différents (version préliminaire)](sql-database-elastic-query-vertical-partitioning.md) | configuration de requêtes de bases de données croisées sur les partitions verticales |



## <a name="elastic-transaction"></a>Transaction élastique

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 99 | [Transactions distribuées entre bases de données cloud](sql-database-elastic-transactions-overview.md) | Vue d’ensemble des transactions de bases de données élastiques avec la base de données SQL Azure |



## <a name="backup-and-recovery"></a>Sauvegarde et récupération

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 100 | [Sauvegardes SQL Database](sql-database-automated-backups.md) | Découvrez-en plus sur les sauvegardes intégrées SQL Database qui vous permettent de restaurer Azure SQL Database à une version antérieure ou de copier une base de données vers une nouvelle base de données dans une zone géographique (jusqu’à 35 jours). |
| 101 | [Vue d’ensemble de la continuité de l’activité avec la base de données Azure SQL](sql-database-business-continuity.md) | Découvrez comment Azure SQL Database prend en charge la continuité des activités cloud et la récupération de base de données et vous aide à maintenir les applications cloud opérationnelles. |
| 102 | [Comment restaurer une table unique à partir d’une sauvegarde Azure SQL Database](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Découvrez comment restaurer une table unique à partir d’une sauvegarde Base de données SQL Azure. |
| 103 | [Concevoir une application pour la récupération d’urgence cloud à l’aide de la géo-réplication active dans une base de données SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Apprenez à concevoir des solutions de récupération d’urgence cloud pour la planification de la continuité des activités à l’aide de la géo-réplication pour la sauvegarde de données d’application avec Azure SQL Database. |
| 104 | [Restaurer une base de données SQL Azure ou basculer vers une base de données secondaire](sql-database-disaster-recovery.md) | Découvrez comment récupérer une base de données en cas de panne d’un centre de données régional grâce aux fonctionnalités de géo-réplication active et de restauration géographique du service Base de données SQL Azure. |
| 105 | [Exécution d'un exercice de récupération d'urgence](sql-database-disaster-recovery-drills.md) | Découvrez des conseils et des meilleures pratiques en matière d'utilisation de la base de données Azure SQL pour effectuer des exercices de récupération d'urgence qui vous aideront à maintenir la résistance aux pannes de vos applications métier stratégiques en cas de défaillances. |
| 106 | [Stratégies de récupération d’urgence pour les applications utilisant le pool élastique de base de données SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Apprenez à concevoir votre solution cloud pour la récupération d’urgence en choisissant le modèle de basculement approprié. |
| 107 | [Utilisation de la classe RecoveryManager pour résoudre les problèmes de correspondance de partitionnement](sql-database-elastic-database-recovery-manager.md) | Utiliser la classe RecoveryManager pour résoudre les problèmes des mappages de partition |
| 108 | [Importer un fichier BACPAC pour créer une base de données SQL Azure](sql-database-import.md) | Créer une base de données SQL Azure en important un fichier BACPAC existant. |
| 109 | [Restaurer une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure](sql-database-point-in-time-restore-portal.md) | Restaurez une base de données SQL Azure à un point antérieur dans le temps. |
| 110 | [Restaurer une base de données SQL Azure à un point dans le temps avec PowerShell](sql-database-point-in-time-restore-powershell.md) | Restaurer une base de données SQL Azure à un point antérieur dans le temps |
| 111 | [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md) | Limite de restauration dans le temps, base de données SQL Microsoft Azure, restaurer une base de données, récupérer une base de données, portail Azure Classic, portail Azure Classic |
| 112 | [Récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées d’une base de données](sql-database-recovery-using-backups.md) | Apprenez-en plus sur la limite de restauration dans le temps, qui vous permet de restaurer une base de données SQL Azure à un point antérieur dans le temps (jusqu’à 35 jours). |
| 113 | [Restaurer une base de données SQL Azure supprimée à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md) | Restaurez une base de données SQL Azure supprimée (portail Azure). |
| 114 | [Restaurer une base de données SQL Azure supprimée avec PowerShell](sql-database-restore-deleted-database-powershell.md) | Restaurez une base de données SQL Azure supprimée (PowerShell). |
| 115 | [Restaurer une base de données à un état antérieur, restaurer une base de données supprimée ou à la suite d’une panne du centre de données](sql-database-troubleshoot-backup-and-restore.md) | Découvrez comment restaurer une base de données suite à des erreurs ou des bannes en utilisant des sauvegardes et des réplicas dans Azure SQL DatabaseBase de données SQL. |



## <a name="migrate"></a>Migrer

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 116 | [Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Base de données SQL Microsoft Azure, migration de base de données, exporter une base de données, exporter un fichier BACPAC, sqlPackage |
| 117 | [Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Base de données SQL Microsoft Azure, migration de base de données, exportation de base de données, exportation de fichier BACPAC, Assistant d’exportation d’application de couche Données |
| 118 | [Importation vers Base de données SQL à partir d’un fichier BACPAC à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, importer un fichier BACPAC, sqlpackage |
| 119 | [Importation depuis BACPAC vers Base de données SQL avec SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Microsoft Azure SQL Database, déployer base de données, migration base de données, importer base de données, exporter base de données, assistant de migration |
| 120 | [Migration d’une base de données SQL Server vers Base de données SQL à l’aide de l’assistant de déploiement de base de données vers Microsoft Azure Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Base de données SQL Microsoft Azure, migration de base de données, Assistant de base de données Microsoft Azure |
| 121 | [Migrer une base de données SQL Server vers une Base de données SQL Azure à l’aide de la réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, réplication transactionnelle |
| 122 | [Déterminer la compatibilité de Base de données SQL à l’aide de SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, SqlPackage |
| 123 | [Utilisez SQL Server Management Studio afin de déterminer la compatibilité de la base de données SQL avant la migration vers une base de données SQL Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, Assistant d’exportation d’application de couche Données |
| 124 | [Utilisation de l’Assistant Migration SQL Azure pour résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers une base de données SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure |
| 125 | [Utilisation de SQL Server Data Tools pour Visual Studio afin de migrer une base de données SQL Server vers une base de données SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure, SSDT |
| 126 | [Résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration vers Base de données SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure |
| 127 | [Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell](sql-database-export-powershell.md) | Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell |
| 128 | [Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell](sql-database-import-powershell.md) | Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell |
| 129 | [Charger des données à partir d’un fichier CSV dans Azure SQL Data Warehouse (fichiers plats)](sql-database-load-from-csv-with-bcp.md) | Pour les données de taille réduite, utilise l’utilitaire de ligne de commande BCP pour importer les données dans la base de données SQL Azure. |
| 130 | [Déplacer des bases de données entre des serveurs, entre des abonnements, au sein et hors d’Azure](sql-database-troubleshoot-moving-data.md) | Étapes à suivre pour copier, déplacer et migrer des données et des bases de données dans Azure SQL Database. |



## <a name="move-data-in-and-out"></a>Déplacer les données

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 131 | [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md) | Découvrez comment migrer une base de données SQL Server locale vers Azure SQL Database dans le cloud. Utilisez les outils de migration de base de données pour tester la compatibilité avant de procéder à la migration de la base de données. |
| 132 | [Copie d'une base de données SQL Azure](sql-database-copy.md) | Création d'une copie d'une base de données SQL Azure |
| 133 | [Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure](sql-database-export.md) | Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure |



## <a name="security"></a>Sécurité

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 134 | [Connexion au service Base de données SQL ou SQL Data Warehouse avec l’authentification Azure Active Directory](sql-database-aad-authentication.md) | Apprenez comment vous connecter à la base de données SQL en utilisant l’authentification Azure Active Directory |
| 135 | [Chiffrement intégral : protéger les données sensibles dans Base de données SQL et stocker vos clés de chiffrement dans le magasin de certificats Windows](sql-database-always-encrypted.md) | Protéger les données sensibles de votre base de données SQL en quelques minutes. |
| 136 | [Chiffrement intégral : Protéger les données sensibles dans Base de données SQL et stocker vos clés de chiffrement dans Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) | Protéger les données sensibles de votre base de données SQL en quelques minutes. |
| 137 | [Base de données SQL - Prise en charge des clients de niveau inférieur et modification des points de terminaison IP à des fins d’audit](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | En savoir plus sur la prise en charge des clients de niveau inférieur de la base de données SQL et sur la modification des points de terminaison IP à des fins d’audit. |
| 138 | [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure. |
| 139 | [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure. |
| 140 | [Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure. |
| 141 | [Prise en main du masquage de données dynamiques de base de données SQL (portail Azure)](sql-database-dynamic-data-masking-get-started.md) | Prise en main du masquage des données dynamiques de base de données SQL dans le portail Azure |
| 142 | [Prise en main du masquage des données dynamiques de base de données SQL (portail Azure Classic)](sql-database-dynamic-data-masking-get-started-portal.md) | Comment prendre en main le masquage de données dynamiques de base de données SQL dans le portail Azure Classic |
| 143 | [Configuration des règles de pare-feu de la base de données SQL Azure \- Vue d’ensemble](sql-database-firewall-configure.md) | Apprenez à configurer un pare-feu de base de données SQL avec des règles de pare-feu au niveau du serveur et au niveau de la base de données pour gérer les accès. |
| 144 | [Didacticiel sur la base de données SQL : créer des comptes d’utilisateurs de base de données SQL pour accéder et gérer une base de données](sql-database-get-started-security.md) | Apprenez à créer des comptes d'utilisateurs pour accéder et gérer une base de données. |
| 145 | [Authentification et autorisation de base de données SQL : octroi de l’accès](sql-database-manage-logins.md) | Découvrez la gestion de la sécurité SQL Database, en particulier la façon de gérer la sécurité d’accès et de connexion aux bases de données par le biais du compte du principal au niveau du serveur. |
| 146 | [Consignes et limitations de sécurité de base de données Azure SQL](sql-database-security-guidelines.md) | Découvrez les instructions et limitations de Microsoft Azure SQL Database relatives à la sécurité. |
| 147 | [Prise en main de Threat Detection pour la base de données SQL](sql-database-threat-detection-get-started.md) | Prise en main l’outil Threat Detection de la base de données SQL dans le portail Azure |
| 148 | [Comment effectuer des tâches d’administration courantes telles que la réinitialisation de mot de passe d’administrateur dans Azure SQL Database](sql-database-troubleshoot-permissions.md) | Explique comment effectuer des tâches d’administration courantes dans SQL Database. Par exemple, la réinitialisation de mot de passe administrateur, l’octroi et la suppression de l’accès. |



## <a name="manage-your-database"></a>Gérer votre base de données

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 149 | [Prise en main de l’audit de base de données SQL](sql-database-auditing-get-started.md) | Prise en main de l’audit de base de données SQL |
| 150 | [Configurer une règle de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent au serveur SQL Azure. |
| 151 | [Copie d’une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md) | Création d'une copie d'une base de données SQL Azure |
| 152 | [Gestion des bases de données SQL Azure à l'aide d'Azure Automation](sql-database-manage-automation.md) | Découvrez comment le service Azure Automation peut être utilisé pour gérer les bases de données SQL Azure à grande échelle. |
| 153 | [Vue d’ensemble des outils de gestion de la base de données SQL](sql-database-manage-overview.md) | Compare les outils et les options de gestion de bases de données SQL Microsoft Azure. |
| 154 | [Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md) | Apprenez à détecter et à diagnostiquer des problèmes de performances courants à l’aide de vues de gestion dynamique pour surveiller une base de données SQL Microsoft Azure. |
| 155 | [Sécurisation de votre base de données SQL](sql-database-security.md) | Découvrez plus d’informations sur la base de données SQL Microsoft Azure et la sécurité de la base de données SQL, y compris les différences entre le cloud et l’installation SQL Server sur site en termes d’authentification, d’autorisations, de sécurité des connexions, de chiffrement et de conformité. |



## <a name="extended-events"></a>Événements étendus

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 156 | [Code cible du fichier d’événements pour les événements étendus dans la base de données SQL](sql-database-xevent-code-event-file.md) | Fournit PowerShell et Transact-SQL pour un exemple de code en deux phases qui montre l’utilisation de la cible du fichier d’événements dans un événement étendu sur Azure SQL Database. Le service Azure Storage est nécessaire pour ce scénario. |
| 157 | [Code cible de la mémoire tampon en anneau pour les événements étendus dans la base de données SQL](sql-database-xevent-code-ring-buffer.md) | Fournit un exemple de code Transact-SQL simple et rapide en utilisant la cible de la mémoire tampon en anneau, dans Azure SQL Database. |
| 158 | [Événement étendus dans la base de données SQL](sql-database-xevent-db-diff-from-svr.md) | Décrit les événements étendus (XEvents) dans la base de données SQL Azure et les différences entre les sessions d’événements dans la base de données SQL Azure et dans Microsoft SQL Server. |



## <a name="geo-replication"></a>Géo-réplication

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 159 | [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md) | Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure |
| 160 | [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec PowerShell](sql-database-geo-replication-failover-powershell.md) | Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec PowerShell |
| 161 | [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL |
| 162 | [Vue d’ensemble : Géo-réplication active de base de données SQL](sql-database-geo-replication-overview.md) | La géo-réplication active permet de configurer 4 réplicas de votre base de données dans un des centres de données Azure. |
| 163 | [Configurer la géoréplication pour Base de données SQL Azure avec le portail Azure](sql-database-geo-replication-portal.md) | Configurer la géoréplication pour Base de données SQL Azure avec le portail Azure |
| 164 | [Configurer la géoréplication pour Base de données SQL Azure avec PowerShell](sql-database-geo-replication-powershell.md) | Configurer la géoréplication active pour Base de données SQL Azure avec PowerShell |
| 165 | [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) | Cette rubrique décrit des considérations relatives à la gestion de la sécurité après un basculement ou une restauration de base de données. |
| 166 | [Configurer la géoréplication pour Base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurer la géoréplication pour Base de données SQL Azure avec Transact-SQL |
| 167 | [Géo-restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide du portail Azure](sql-database-geo-restore-portal.md) | Géo-restaurez une base de données SQL Azure à partir d’une sauvegarde géo-redondante (portail Azure). |
| 168 | [Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide de PowerShell](sql-database-geo-restore-powershell.md) | Restaurer une base de données SQL Azure sur un nouveau serveur à partir d’une sauvegarde géo-redondante |



## <a name="upgrade"></a>Mise à niveau

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 169 | [Amélioration des performances des requêtes avec le niveau de compatibilité 130 dans Base de données SQL Azure](sql-database-compatibility-level-query-performance-130.md) | Procédure et outils pour déterminer le niveau de compatibilité adapté à votre base de données sur Base de données SQL Azure ou Microsoft SQL Server |
| 170 | [Gestion des mises à niveau propagées des applications cloud à l’aide de la géo-réplication active de la base de données SQL](sql-database-manage-application-rolling-upgrade.md) | Découvrez comment utiliser la géo-réplication de la base de données SQL pour prendre en charge les mises à niveau en ligne de votre application cloud. |
| 171 | [Mise à niveau vers Azure SQL Database V12 à l’aide du portail Azure](sql-database-upgrade-server-portal.md) | Explique comment effectuer une mise à niveau vers Azure SQL Database V12, notamment la mise à niveau des bases de données Web et Business et la mise à niveau d’un serveur V11 migrant ses bases de données directement dans un pool de base de données élastique à l’aide du portail Azure. |
| 172 | [Mise à niveau vers Azure SQL Database V12 à l’aide de PowerShell](sql-database-upgrade-server-powershell.md) | Explique comment effectuer une mise à niveau vers Azure SQL Database V12, notamment la mise à niveau des bases de données Web et Business et la mise à niveau d’un serveur V11 migrant ses bases de données directement dans un pool de base de données élastique à l'aide de PowerShell. |
| 173 | [Planifier et préparer la mise à niveau vers la version V12 de la base de données SQL](sql-database-v12-plan-prepare-upgrade.md) | Décrit la préparation et les limitations relatives à la mise à niveau vers la version V12 d’Azure SQL Database. |
| 174 | [Nouveautés de la base de données SQL V12](sql-database-v12-whats-new.md) | Explique pourquoi les systèmes d’entreprise qui utilisent Azure SQL Database dans le cloud profitent de la mise à niveau vers la version 12 (V12). |
| 175 | [Forum aux questions sur la disparition des éditions Web et Business](sql-database-web-business-sunset-faq.md) | Découvrez les fonctionnalités des nouveaux niveaux de service qui remplacent les bases de données Azure SQL Web et Business. |



## <a name="tools"></a>Outils

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 176 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-command-line-tools.md) | Gestion d’Azure SQL Database avec PowerShell. |
| 177 | [Didacticiel sur la base de données SQL : Connectez Excel à une base de données SQL Azure et créez un rapport](sql-database-connect-excel.md) | Découvrez comment connecter Microsoft Excel à la base de données SQL Azure dans le cloud. Importez des données dans Excel pour les rapports et l’exploration des données. |
| 178 | [Créer une base de données SQL et effectuer des tâches courantes d’installation de base de données avec les applets de commande PowerShell](sql-database-get-started-powershell.md) | Apprenez dès maintenant à créer une base de données SQL avec PowerShell. Les tâches courantes d’installation de base de données peuvent être gérées via les applets de commande PowerShell. |
| 179 | [Prise en main d’Azure SQL Data Sync (version préliminaire)](sql-database-get-started-sql-data-sync.md) | Ce didacticiel présente la synchronisation des données SQL Azure (aperçu). |
| 180 | [Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Découvrez comment utiliser SQL Server Management Studio pour gérer des serveurs et les bases de données de la base de données SQL. |
| 181 | [Gestion des bases de données SQL Azure au moyen du portail Azure](sql-database-manage-portal.md) | Découvrez comment utiliser le portail Azure pour gérer une base de données relationnelle dans le cloud. |
| 182 | [Modification des niveaux de service et de performances (niveau de tarification) d’une base de données SQL avec PowerShell](sql-database-scale-up-powershell.md) | Cet article explique comment faire monter ou descendre en puissance une base de données SQL avec PowerShell. Modification du niveau de tarification d’une base de données SQL Azure avec PowerShell. |
| 183 | [Connexion à une base de données SQL à l’aide de SQL Server Management Studio dans Azure RemoteApp](sql-database-ssms-remoteapp.md) | Utilisez ce didacticiel pour apprendre à utiliser SQL Server Management Studio dans Azure RemoteApp pour la sécurité et les performances lors de la connexion à la base de données SQL |



## <a name="reference"></a>Référence

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 184 | [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md) | Répertorie le numéro de version minimal pour chaque pilote que les programmes clients peuvent utiliser lors de la connexion à Azure SQL Database ou à Microsoft SQL Server. Un lien est fourni pour les informations sur les versions des pilotes publiés par la communauté, et non par Microsoft. |
| 185 | [Limites de ressources de base de données SQL Azure](sql-database-resource-limits.md) | Cette page décrit certaines limites de ressources courantes pour une base de données SQL Azure. |
| 186 | [Différences dans le langage Transact-SQL Azure SQL Database.](sql-database-transact-sql-information.md) | Instructions Transact-SQL qui ne sont pas entièrement prises en charge dans Azure SQL Database |



## <a name="miscellaneous"></a>Divers

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 187 | [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) | Création d'une copie d'une base de données SQL Azure à l'aide de PowerShell |
| 188 | [Copier une base de données SQL Azure à l’aide de Transact-SQL](sql-database-copy-transact-sql.md) | Création d'une copie d'une base de données SQL Azure à l'aide de Transact-SQL |
| 189 | [Consignes et limitations générales de base de données SQL Azure](sql-database-general-limitations.md) | Cette page décrit certaines limitations générales de la base de données SQL Azure, ainsi que les zones d’interopérabilité et de prise en charge. |
| 190 | [Recommandations relatives aux niveaux tarifaires des bases de données SQL](sql-database-service-tier-advisor.md) | Lorsque vous modifiez les niveaux tarifaires dans le portail Azure, vous pouvez consulter les recommandations fournies, notamment le niveau le mieux adapté à l’exécution de la charge de travail d’une base de données SQL Azure existante. Les niveaux tarifaires décrivent les niveaux de service et de performances d’une base de données SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Extras

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>Articles connexes d’autres services Azure

- [Sauvegarde de votre application Azure App Service dans Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>Outils de la documentation

- [Mises à jour annoncées pour la base de données SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Recherche de tous les types de documentation Microsoft Azure à l’aide de filtres](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>Graphique du parcours d’apprentissage

- [Graphique du parcours d’apprentissage de tous les services Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Parcours d’apprentissage : utilisation de la base de données SQL Azure](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Parcours d’apprentissage : infrastructure élastique de la base de données SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->





<!--HONumber=Oct16_HO2-->


