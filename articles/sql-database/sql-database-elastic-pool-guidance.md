<properties 
	pageTitle="Considérations sur les prix et performances pour des pools élastiques de bases de données SQL Azure" 
	description="Un pool de bases de données élastique est un ensemble de ressources disponibles partagé par un groupe de bases de données élastiques. Ce document fournit des conseils pour vous aider à évaluer la pertinence de l'utilisation d'un pool de bases de données élastique pour un groupe de bases de données." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/12/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Considérations sur les prix et performances pour un pool de bases de données élastique


Ce document fournit des conseils visant à vous aider à évaluer si l’utilisation d’un pool de bases de données élastique pour un groupe de bases de données est économique selon les modèles d'utilisation de base de données et les différences de prix entre un pool élastique de bases de données et des bases de données uniques. Des conseils supplémentaires sont également fournis pour vous aider à déterminer la taille de pool actuelle requise pour un ensemble existant de bases de données SQL.

- Pour une vue d'ensemble des pools de bases de données élastiques, consultez [Pools de bases de données élastiques Base de données SQL](sql-database-elastic-pool.md).
- Pour en savoir plus sur les pools de bases de données élastiques, consultez [Référence de pools de bases de données élastiques Base de données SQL](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs de base de données SQL V12.

## Pool de bases de données élastique

Les ISV (éditeurs de logiciels indépendants) SaaS développent des applications qui reposent sur des niveaux de données à grande échelle composés de plusieurs bases de données. Un modèle d'application commun permet à chacune de ces bases de données d’avoir différents clients avec des modèles d'utilisation particulièrement variables et imprévisibles. Il peut être difficile pour l’ISV de prévoir les besoins en ressources de chaque base de données individuellement. Dans ces circonstances, l’ISV peut surprovisionner les ressources moyennant un coût considérable pour garantir un débit et des temps de réponse favorables pour toutes les bases de données. Ou l’ISV peut réduire ses dépenses, ce qui risque d’entraîner une expérience de performances médiocres pour ses clients.

Les pools de base de données élastiques dans Base de données SQL Azure permettent à l’ISV SaaS d’optimiser le rapport performance/prix pour un groupe de bases de données dans un budget prescrit tout en offrant une élasticité des performances pour chaque base de données. Les pools élastiques de bases de données permettent à l’ISV d’acheter des eDTU (unités de débit de base de données élastiques) pour un pool partagé par plusieurs bases de données afin de s’adapter aux périodes imprévisibles d’une utilisation par les bases de données. Le besoin en eDTU d’un pool est déterminé par l'utilisation globale de ses bases de données. La quantité d’eDTU disponible pour le pool est contrôlée par le budget de l’ISV. Les pools élastiques de bases de données permettent à l’ISV de déduire plus facilement l’incidence du budget sur les performances et vice versa pour son pool. L’ISV ajoute simplement des bases de données au pool, définit les garanties ou capacités en eDTU requises pour les bases de données, puis définit les eDTU du pool en fonction de son budget. En utilisant des pools élastiques de bases de données, l’ISV peut faire évoluer en toute transparence son service en passant d'une lean startup à une entreprise mature à une vitesse sans cesse croissante.
  


## Quand envisager un pool élastique de la base de données

Les pools élastiques de bases de données sont idéals en cas de nombreuses bases de données avec des modèles d'utilisation spécifique. Pour une base de données indiquée, ce modèle se caractérise par une faible utilisation moyenne avec des pics d'utilisation relativement rares.

Plus vous pouvez ajouter de bases de données à un pool et plus vous ferez des économies. Toutefois, en fonction de votre modèle d'utilisation de l'application, il est possible de réaliser des économies rien qu’avec 4 bases de données S3.

Les sections suivantes vous aideront à comprendre comment savoir si votre collection de bases de données spécifique tirera profit de l’utilisation d’un pool élastique de bases de données. Les exemples utilisent les pools élastiques de bases de données Standard, mais ces principes s'appliquent également aux pools De base et Premium.

### Évaluation des modèles d'utilisation de base de données

La figure suivante montre l’exemple d'une base de données qui est très souvent inactive, mais qui connaît de temps en temps des pics d’activité. Il s'agit d'un modèle d'utilisation qui est particulièrement adapté à un pool élastique de bases de données :
 
   ![une base de données][1]

Pour la période d'une heure illustrée ci-dessus, DB1 culmine à 90 DTU, mais son utilisation moyenne totale est < 5 DTU. Un niveau de performance S3 est requis pour exécuter cette charge de travail dans une base de données unique. Cependant, cela laisse la plupart des ressources inutilisées pendant les périodes de faible activité.

Un pool élastique de bases de données permet de partager ces DTU inutilisées entre plusieurs bases de données et réduit donc la quantité totale de DTU nécessaires et le coût global.

En s'appuyant sur l'exemple précédent, supposons qu'il y ait des bases de données supplémentaires avec des modèles d'utilisation similaires comme DB1. Dans les deux figures ci-dessous, l'utilisation de 4 bases de données et celle de 20 bases de données sont représentées sur le même graphique pour montrer que leur utilisation ne se chevauche pas dans le temps :

   ![quatre bases de données][2]

   ![vingt bases de données][3]

L'utilisation globale des DTU pour les 20 bases de données est illustrée par la ligne noire dans la figure ci-dessus. Cela montre que l'utilisation globale des DTU ne dépasse jamais 100 DTU et que les 20 bases de données peuvent partager 100 eDTU sur cette période. Le nombre de DTU est diminué par 20 et le prix par 6 par rapport au placement de chacune des bases de données dans des niveaux de performances S3 pour les bases de données uniques.


Cet exemple est idéal pour les raisons suivantes :

- Il existe de grandes différences entre les pics d’utilisation et l'utilisation moyenne par base de données.  
- Les pics d’utilisation de chaque base de données se produisent à différents moments dans le temps. 
- Les eDTU sont partagés entre plusieurs bases de données.


Le prix d'un pool élastique de bases de données dépend des eDTU du pool et du nombre de bases de données qu'il contient. Alors que le prix unitaire d’une eDTU pour un pool à la tarification selon la disponibilité générale est 3 fois supérieur au prix unitaire d’une DTU pour une base de données unique, les **eDTU de pool peuvent être partagées par plusieurs bases de données et donc un nombre moins important d’eDTU est nécessaire au total**. Ces différences en matière de prix et de partage des eDTU constituent la base du potentiel d'économies que les pools peuvent présenter.

<br>

Les règles élémentaires suivantes, relatives au nombre de bases de données et à l'utilisation des bases de données, permettent de s'assurer qu'un pool élastique de bases de données coûte moins cher que l’utilisation de niveaux de performance pour des bases de données uniques. Ce guide est basé sur les prix selon la disponibilité générale. Notez que les prix selon la disponibilité générale bénéficient d'une remise de 50 % lors de la version préliminaire et que ces règles générales doivent donc être considérées comme relativement classiques.


### Nombre minimal de bases de données

Avec la tarification selon la disponibilité générale, un pool élastique de bases de données devient davantage un choix de performances économiques si 1 eDTU peut être partagée par plus de 3 bases de données. Cela signifie que le nombre de DTU des niveaux de performances pour les bases de données uniques est égale à plus de 3 fois le nombre d’eDTU du pool. Pour les tailles disponibles, consultez la rubrique [Limites relatives aux eDTU et au stockage pour les pools de bases de données et bases de données élastiques](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


***Exemple***<br> Au moins 4 bases de données S3 ou au moins 36 bases de données S0 sont nécessaires pour qu’un pool élastique de bases de données de 100 eDTU soit plus rentable que l'utilisation de niveaux de performances pour des bases de données uniques. (Notez qu’avec les prix en version préliminaire, le seuil de rentabilité sur la tarification selon le nombre de bases de données baisse à 2 bases de données S3 ou 17 bases de données S0.)



### Nombre maximal de bases de données connaissant un pic simultané

En partageant les eDTU, toutes les bases de données d’un pool ne peuvent pas utiliser simultanément les eDTU jusqu'à la limite disponible lors de l'utilisation de niveaux de performances pour les bases de données uniques. Plus le nombre de bases de données connaissant un pic simultané est faible, plus le nombre d’eDTU du pool peut être revu à la baisse et plus le pool devient rentable. En général, pas plus de 1/3 des bases de données du pool doivent connaître un pic simultané à leur limite d’eDTU.

***Exemple***<br> Pour réduire les coûts pour 4 bases de données S3 dans un pool de 200 eDTU, au moins 2 de ces bases de données peuvent connaître un pic simultané au niveau de leur utilisation. Sinon, si plus de 2 de ces 4 bases de données S3 connaissent un pic simultané, le pool devra être redimensionné à plus de 200 eDTU. Et si le pool est redimensionné à plus de 200 eDTU, plusieurs bases de données S3 devront être ajoutées au pool pour maintenir des coûts inférieurs aux niveaux de performances pour les bases de données unique.


Notez que cet exemple ne tient pas compte de l'utilisation des autres bases de données dans le pool. Si toutes les bases de données connaissent une utilisation à un moment donné, moins de 1/3 des bases de données peuvent connaître un pic simultané.


### Utilisation de DTU par base de données

Une différence importante entre le pic d’utilisation et l'utilisation moyenne d’une base de données indique de longues périodes de faible utilisation et de courtes périodes d'utilisation intensive. Ce modèle d'utilisation est idéal pour partager des ressources entre les bases de données. Une base de données doit être envisagée pour un pool quand son pic d’utilisation est environ 3 fois supérieur à son utilisation moyenne.

    
***Exemple***<br> Une base de données S3 qui culmine à 100 DTU et qui utilise en moyenne 30 DTU ou moins est un bon candidat pour le partage d’eDTU au sein d’un pool élastique de bases de données. Ou une base de données S1 qui culmine à 20 DTU et qui utilise en moyenne 7 DTU ou moins est un bon candidat à un pool élastique de bases de données.
    

## Heuristique pour comparer la différence de prix entre un pool élastique de bases de données et les bases de données uniques 

L'heuristique suivante peut aider à estimer si un pool élastique de bases de données est plus économique que l'utilisation des bases de données uniques.

1. Estimez les eDTU nécessaires pour le pool comme suit :
    
    MAX (*nombre total de BD* \* *utilisation moyenne en DTU par BD*, *nombre de BD connaissant un pic simultané* \* *pic d’utilisation en DTU par BD*)

2. Sélectionnez la valeur eDTU la plus petite disponible pour le pool qui est supérieure à l'estimation de l'étape 1. Pour les choix d’eDTU disponibles, consultez les valeurs valides pour les eDTU répertoriées ici : [Limites relatives aux DTU et au stockage pour les pools de bases de données et bases de données élastiques](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


3. Calculez le prix du pool comme suit :

    prix du pool = (*eDTU du pool* \* *prix unitaire d’eDTU de pool*) + (*nombre total de BD* \* *prix unitaire de BD de pool*)

    Pour plus d’informations sur la tarification, consultez la rubrique [Tarification - Base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/).


4. Comparez le prix du pool trouvé à l'étape 3 avec celui de l'utilisation des niveaux de performance appropriés pour les bases de données uniques.



## Détermination de la meilleure taille en eDTU du pool pour les bases de données SQL existantes 

La taille optimale pour un pool élastique de bases de données dépend du nombre global d’eDTU et des ressources de stockage dont ont besoin toutes les bases de données du pool. Cela implique de déterminer la plus grande des deux quantités suivantes :

* Nombre maximal de DTU que se partagent toutes les bases de données du pool.
* Nombre maximal d’octets de stockage que se partagent toutes les bases de données du pool. 

Notez que, pour le niveau de service Standard, 1 Go de stockage est autorisé pour chaque eDTU configuré pour le pool. Par exemple, si un pool est configuré avec 200 DTU, sa limite de stockage est de 200 Go.

Le tableau suivant indique la quantité de stockage par eDTU pour chaque niveau de tarification :

| Niveau | eDTU | Stockage |
| :--- | :--- | :--- |
| De base | 1 | 100 Mo |
| Standard | 1 | 1 Go |
| Premium | 1 | .5 Go |


### Utilisez STA (Service Tier Advisor) et DMV (Dynamic Management Views) pour les recommandations de dimensionnement   

STA et DMV fournissent diverses options et fonctionnalités pour le redimensionnement d'un pool élastique de bases de données. Indépendamment de l'outil utilisé, l'estimation du dimensionnement doit uniquement être utilisée pour l’évaluation initiale et la création de pools élastiques de bases de données. Après la création d'un pool, son utilisation des ressources doit être correctement surveillée et les paramètres de performance du pool doivent être ajustés vers le haut et le bas selon les besoins.

**STA**<br>STA est un outil intégré à la [version préliminaire du portail](https://portal.azure.com) qui évalue automatiquement l’historique d’utilisation en ressources des bases de données dans un serveur de base de données SQL existant et recommande une configuration de pool élastique de bases de données approprié. Pour en savoir plus, consultez la rubrique [Recommandations relatives aux niveaux de tarification du pool élastique de bases de données](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations).

**Outil de dimensionnement DMV**<br>L’outil de dimensionnement DMV est fourni en tant que script PowerShell et permet de personnaliser les estimations de dimensionnement d’un pool élastique de bases de données pour les bases de données existantes dans un serveur.

### Choix entre l’outil STA et l’outil DMV 

Sélectionnez l'outil convenant à l’analyse de votre application spécifique. Le tableau suivant résume les différences entre ces 2 outils de dimensionnement :

| Fonctionnalité | STA | DMV |
| :--- | :--- | :--- |
| Granularité des exemples de données utilisés dans l'analyse. | 15 secondes | 15 secondes |
| Prend en compte les différences de prix entre un pool et les niveaux de performances pour les bases de données uniques. | Oui | Non |
| Permet la personnalisation de la liste des bases de données analysées dans un serveur. | Non | Oui |
| Permet la personnalisation de la période de temps utilisée pour l'analyse. | Non | Oui |


### Estimation de la taille du pool élastique à l'aide de STA  

STA évalue l’historique d’utilisation des bases de données et recommande un pool élastique de bases de données lorsque ce dernier est plus rentable que l’utilisation des niveaux de performances pour les bases de données uniques. Si un pool est recommandé, cet outil fournit la liste des bases de données recommandées, ainsi que la quantité recommandée d’eDTU du pool et les paramètres eDTU min/max pour chaque base de données élastique. Pour qu’une base de données soit considérée comme candidate à un pool, elle doit exister depuis au moins 7 jours.

STA est disponible dans la version préliminaire du portail Azure lorsque vous ajoutez un pool élastique de bases de données à un serveur existant. Si des recommandations pour un pool élastique de bases de données sont disponibles pour ce serveur, elles sont affichées dans la page de création « Pool de bases de données élastique ». Les clients peuvent toujours modifier les configurations recommandées pour créer leur propre regroupement pool élastique de bases données.

Pour en savoir plus, consultez la rubrique [Recommandations relatives aux niveaux de tarification du pool élastique de bases de données](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations)

### Estimation de la taille du pool élastique à l'aide des DMV (Dynamic Management Views) 

La DMV [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) mesure l’utilisation en ressources d’une base de données unique. Cette DMV indique l’UC, les E/S, le journal et l'utilisation du journal d'une base de données sous la forme d’un pourcentage de la limite de niveau de performances de la base de données. Ces données peuvent être utilisées pour calculer l'utilisation en DTU d'une base de données dans un intervalle de 15 secondes donné.

L'utilisation globale en eDTU d’un pool élastique de bases de données dans un intervalle de 15 secondes peut être estimée en additionnant l'utilisation en eDTU de toutes les bases de données candidates pendant cette période. Selon les objectifs de performances spécifiques, il peut être judicieux d'ignorer un faible pourcentage des exemples de données. Par exemple, une valeur de 99e centile du nombre global d’eDTU sur tous les intervalles de temps peut être appliquée pour exclure les observations aberrantes et fournir un nombre d’eDTU de pool élastique de bases de données pour convenir à 99 % des intervalles montrés en exemple.

## Script PowerShell pour estimer l’utilisation globale en eDTU de vos bases de données

Un exemple de script PowerShell est fourni ici pour estimer les valeurs d’eDTU globales pour les bases de données utilisateur sur un serveur de base de données SQL.

Le script collecte uniquement les données en cours d'exécution. Pour une charge de travail de production habituelle, vous devez exécuter le script pendant au moins un jour, bien qu'une semaine ou une plus longue période donne probablement une estimation plus précise. Exécutez le script pour une durée de temps qui représente votre charge de travail habituelle en ce qui concerne les bases de données.

> [AZURE.IMPORTANT]Vous devez conserver la fenêtre PowerShell ouverte pendant l'exécution du script. Ne fermez pas la fenêtre PowerShell tant que vous n’avez pas exécuté le script pendant une durée suffisante et que vous n’avez pas capturé suffisamment de données pour représenter votre charge de travail habituelle couvrant la durée d'utilisation normale et la durée d’utilisation maximale.

### Configuration requise pour le script 

Installez les éléments suivants avant d'exécuter le script :

- les derniers [outils de ligne de commande Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) ;
- le [pack de fonctionnalités SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).


### Détails du script


Vous pouvez exécuter le script à partir de votre ordinateur local ou d’un ordinateur virtuel sur le cloud. Lorsque vous l’exécutez à partir de votre ordinateur local, vous risquez de subir des frais de sortie de données, car le script a besoin de télécharger des données depuis vos bases de données cibles. Vous trouverez ci-dessous une estimation du volume de données en fonction du nombre de bases de données cibles et de la durée d'exécution du script. Pour découvrir les coûts de transfert de données Azure, consultez la rubrique [Détails de la tarification de transfert de données](http://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 base de données par heure = 38 Ko
 -     1 base de données par jour = 900 Ko
 -     1 base de données par semaine = 6 Mo
 -     100 bases de données par jour = 90 Mo
 -     500 bases de données par semaine = 3 Go

Le script exclut certaines bases de données qui ne sont pas de bons candidats à l'offre de version préliminaire publique actuelle de la couche Pool élastique standard. Si vous avez besoin d'exclure d’autres bases de données du serveur cible, vous pouvez modifier le script pour qu’il réponde à vos critères. Par défaut, le script ne compile pas d'informations pour les éléments suivants :

* Les bases de données élastiques (bases de données déjà présentes dans un pool élastique).
* La base de données maîtresse du serveur.

Le script a besoin d’une base de données de sortie pour stocker les données intermédiaires en vue de l'analyse. Vous pouvez utiliser une base de données nouvelle ou existante. Bien qu’elle ne soit pas techniquement nécessaire pour exécuter l'outil, la base de données de sortie doit se trouver sur un autre serveur pour ne pas affecter le résultat de l'analyse. Suggérez que le niveau de performance de la base de données de sortie soit au moins à S0 ou à un niveau supérieur. Lors de la collecte d'une longue période de données pour un grand nombre de bases de données, vous pouvez envisager de mettre à niveau votre base de données de sortie vers un niveau de performance supérieur.

Le script a besoin que vous fournissiez les informations d'identification permettant de se connecter au serveur cible (le candidat au pool de bases de données élastique) avec le nom complet du serveur comme « abcdef.database.windows.net ». Actuellement, le script ne prend pas en charge l’analyse de plusieurs serveurs à la fois.


Après avoir soumis les valeurs du jeu de paramètres initial, vous êtes invité à vous connecter à votre compte Azure. Il s'agit de vous connecter à votre serveur cible, et non de vous connecter au serveur de la base de données de sortie.
	
Si vous rencontrez les avertissements suivants lors de l'exécution du script, vous pouvez les ignorer :

- AVERTISSEMENT : l'applet de commande Switch-AzureMode est déconseillée.
- AVERTISSEMENT : impossible d'obtenir des informations sur le service SQL Server. Une tentative de connexion à WMI sur « Microsoft.Azure.Commands.Sql.dll » a échoué avec l'erreur suivante : le serveur RPC n'est pas disponible.

Une fois le script terminé, il génère le nombre d’eDTU estimé nécessaire pour qu’un pool élastique puisse contenir toutes les bases de données candidates sur le serveur cible. Cette estimation du nombre d’eDTU peut être utilisée pour créer et configurer un pool élastique de bases de données contenant ces bases de données. Une fois que le pool est créé et que les bases de données ont été déplacées dans le pool, il doit être étroitement surveillé pendant quelques jours et les réglages de la configuration des eDTU du pool doivent être réalisés en fonction des besoins.


Pour sélectionner la totalité du script en vue de le copier, cliquez 3 fois (triple clic) sur n'importe quelle partie de texte dans le script.

    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
    Write-Host "Collecting metrics..." 
    foreach ($db in $ListOfDBs)
    {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    WHEN 'P1' THEN 125
    WHEN 'P2' THEN 250
    WHEN 'P3' THEN 1000
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100
    WHEN 'P2' THEN 200
    WHEN 'P3' THEN 800
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
    }
    
    $start_time = $start_time.AddMinutes($Waittime)
    $end_time = $end_time.AddMinutes($Waittime)
    Write-Host $start_time
    Write-Host $end_time
    do {
    Start-Sleep 1
       }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    Write-Host "Analyzing the collected metrics...."
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output 
    WHERE slo LIKE '
    
    $sql2 = '
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
    ELSE 
    SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
    
    #check if there are any web/biz edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Shared*")
    {
    write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any basic edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Basic*")
    {
    write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Basic%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]} 
    }
    
    #check if there are any standard edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "S*")
    {
    write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any premium edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "P*")
    {
    write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'P%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
        

## Résumé

Toutes les bases de données uniques ne sont pas de parfaits candidats à un pool de bases de données élastique. Les bases de données avec des modèles d'utilisation qui se caractérisent par une faible utilisation moyenne et des pics d'utilisation relativement rares constituent d'excellents candidats aux pools élastiques de bases de données. Comme les modèles d'utilisation applicatifs sont dynamiques, utilisez les informations et outils décrits dans cet article pour réaliser une évaluation initiale permettant de déterminer si un pool de bases de données élastique est un bon choix pour une partie ou la totalité de vos bases de données. Cet article est simplement un point de départ pour faciliter votre décision quant à la pertinence d’un pool de bases de données élastique. N'oubliez pas que vous devez surveiller en permanence l’historique d’utilisation des ressources (à l'aide de STA ou de DMV) et réévaluer constamment les niveaux de performance de toutes vos bases de données. N'oubliez pas que vous pouvez facilement déplacer des bases de données dans et en dehors des pools élastiques de bases de données et que si vous avez un très grand nombre de bases de données, vous pouvez avoir plusieurs pools de tailles différentes dans lesquels vous pouvez répartir vos bases de données.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=August15_HO7-->