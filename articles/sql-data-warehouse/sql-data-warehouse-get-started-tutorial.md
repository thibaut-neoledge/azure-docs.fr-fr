---
title: "Azure SQL Data Warehouse : didacticiel de prise en main | Microsoft Docs"
description: "Didacticiel de prise en main de la solution Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: fe9de0ffad3fe5d4acbf3caf2f08101f6a13daaf
ms.openlocfilehash: 12f500c01671799612b0d1988e0d07bbfda600da


---
# <a name="get-started-with-sql-data-warehouse"></a>Prise en main de SQL Data Warehouse

Didacticiel de prise en main de la solution Azure SQL Data Warehouse. Ce didacticiel vous présente les principes de base de l’approvisionnement et du chargement des données dans un entrepôt SQL Data Warehouse. Il décrit également quelques notions de base sur la mise à l’échelle, la suspension et le paramétrage. 

**Durée estimée :** 75 minutes

## <a name="prerequisites"></a>Composants requis


### <a name="sign-up-for-microsoft-azure"></a>S'inscrire à Microsoft Azure
Si vous ne disposez pas d’un compte Microsoft Azure, vous devez en créer un pour pouvoir utiliser ce service. Si vous avez déjà un compte, vous pouvez ignorer cette étape. 

1. Accédez aux pages de compte à l’adresse [https://azure.microsoft.com/fr-fr/account/](https://azure.microsoft.com/account/).
2. Créer un compte Azure gratuit, ou achetez-en un.
3. Suivez les instructions.

### <a name="install-appropriate-sql-client-driver-and-tools"></a>Installer le pilote du client SQL et les outils appropriés

La plupart des outils du client SQL peuvent se connecter à Azure SQL Data Warehouse à l’aide de JDBC, ODBC ou ADO.net. En raison de la complexité du produit et du grand nombre de fonctionnalités T-SQL prises en charge par SQL Data Warehouse, il se peut qu’une application cliente ne soit pas entièrement compatible avec ce logiciel.

Si vous exécutez un système d’exploitation Windows, nous vous recommandons de recourir à [Visual Studio] ou à [SQL Server Management Studio].


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>Créer un Azure SQL Data Warehouse

> [!NOTE]
> La création d’un entrepôt de données SQL Data Warehouse peut donner lieu à un nouveau service facturable.  Pour en savoir plus, voir [SQL Data Warehouse - Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>


### <a name="create-a-sql-data-warehouse"></a>Créer un entrepôt de données SQL
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **Bases de données** > **SQL Data Warehouse**.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Renseignez les détails relatifs au déploiement.

    **Nom de la base de données** : choisissez le nom de votre choix. Si vous disposez de plusieurs instances SQL Data Warehouse, nous vous recommandons d’inclure dans ce nom des détails tels que la région, l’environnement, etc., par exemple *mydw-westus-1-test*.

    **Abonnement :** votre abonnement Azure.

    **Groupe de ressources** : créez-en un (ou indiquez le groupe existant si vous envisagez d’utiliser Azure SQL Data Warehouse avec d’autres services).
    > [!NOTE]
    > Les services d’un groupe de ressources doivent partager le même cycle de vie. Les groupes de ressources sont utiles pour administrer les ressources (définition de la portée du contrôle d’accès et déploiement basé sur des modèles, par exemple). Découvrez [ici](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) plus d’informations sur les groupes de ressources et meilleures pratiques Azure.
    >

    **Source** : base de données vide.

    **Serveur** : sélectionnez le serveur que vous avez créé dans le cadre de la section [Composants requis].

    **Classement** : conservez le classement par défaut, à savoir SQL_Latin1_General_CP1_CI_AS.

    **Sélectionner le compteur de performance** : nous vous recommandons de conserver le modèle 400DWU standard.

4. Choisissez **Épingler au tableau de bord**
    ![Épingler au tableau de bord](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. Installez-vous confortablement et attendez le déploiement de votre instance Azure SQL Data Warehouse. Il est normal que ce processus prenne plusieurs minutes. Le portail vous envoie une notification lorsque le déploiement de votre instance est terminé. 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>Connexion à Azure SQL Data Warehouse via un serveur SQL Server (serveur logique)

Pour les besoins de ce didacticiel, la connexion à Azure SQL Data Warehouse est effectuée via SQL Server Management Studio (SSMS). Toutefois, vous pouvez utiliser d’autres outils pour vous connecter à Azure SQL Data Warehouse par l’intermédiaire de nos connecteurs pris en charge : ADO.NET, JDBC, ODBC et PHP. N’oubliez pas que les fonctionnalités peuvent être limitées si les outils ne sont pas pris en charge par Microsoft.


### <a name="get-connection-information"></a>Obtenir des informations de connexion

Pour vous connecter à votre instance SQL Data Warehouse, vous devez recourir à un serveur SQL Server (serveur logique) créée dans le cadre de la section [Composants requis].

1. Sélectionnez votre instance SQL Data Warehouse dans le tableau de bord, ou recherchez-la dans vos ressources.

    ![Tableau de bord SQL Data Warehouse](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Recherchez le nom complet du serveur logique.

    ![Sélection du nom du serveur](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Ouvrez SSMS et utilisez l’Explorateur d’objets pour vous connecter à ce serveur en utilisant les informations d’identification que vous avez créées dans le cadre de la section [Composants requis].

    ![Connexion avec SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Si tout se passe correctement, vous devez maintenant être connecté à votre serveur SQL Server (serveur logique). Vous pouvez utiliser ces informations d’identification pour vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données. Toutefois, nous vous recommandons de créer des connexions et utilisateurs distincts pour chaque base de données. La section [Créer un utilisateur pour SQL Data Warehouse](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse) explique comment créer un utilisateur. 

## <a name="create-a-user-for-sql-data-warehouse"></a>Créer un utilisateur pour SQL Data Warehouse

### <a name="why-create-a-separate-user"></a>Pourquoi créer un utilisateur distinct ?

Nous utilisons la connexion au serveur SQL Server (serveur logique) et les informations d’identification du serveur de l’étape précédente pour créer un utilisateur pour notre instance SQL Data Warehouse. Vous pouvez être amené à créer une connexion et un utilisateur distincts pour votre instance SQL Data Warehouse pour deux raisons.

1.  Les utilisateurs de votre organisation sont invités à utiliser un compte différent pour s’authentifier. De cette façon, vous pouvez limiter les autorisations accordées aux applications et réduire les risques d’activité malveillante.

2. Par défaut, la connexion d’administrateur serveur que vous utilisez actuellement a recours à une classe de ressource de taille plus petite. Les classes de ressources vous aident à contrôler l’allocation de mémoire et les cycles de processeur pour une requête. Les utilisateurs de **smallrc** bénéficient d’une plus petite quantité de mémoire et peuvent tirer parti d’une concurrence accrue. En revanche, les utilisateurs affectés à **xlargerc** disposent de grandes quantités de mémoire. Ainsi, une plus faible proportion de leurs requêtes peuvent s’exécuter simultanément. Pour charger les données de manière à optimiser la compression, vous devez vous assurer que l’utilisateur effectuant l’opération fait partie d’une classe de ressource plus étendue. Découvrez plus d’informations sur les classes de ressource [ici](./sql-data-warehouse-develop-concurrency.md#resource-classes) :

### <a name="creating-a-user-of-a-larger-resource-class"></a>Création d’un utilisateur appartenant à une classe de ressource plus étendue

1. Interrogez la base de données **master** de votre serveur.

    ![Nouvelle requête sur Master](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Nouvelle requête sur Master1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Créez un utilisateur et une connexion au serveur

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. En interrogeant la base de données SQL Data Warehouse, créez un utilisateur de base de données reposant sur la connexion au serveur. 
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. Accordez à l’utilisateur le contrôle sur la base de données.
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Si le nom de votre base de données comporte des traits d’union, veillez à le placer entre crochets. 
    >

5. Ajoutez votre utilisateur de base de données au rôle de classe de ressource **xlargerc**.
    ```sql
    EXEC sp_addrolemember 'xlargerc', 'LoadingUser';
    ```

6. Connectez-vous à votre base de données en utilisant vos nouvelles informations d’identification.

    ![Connexion avec le nouveau compte de connexion](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>Chargement des données

### <a name="defining-external-data"></a>Définition des données externes
1. Créez une clé principale et de définir une source de données externe.

    ```sql
    CREATE MASTER KEY;

    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. Définissez les formats des fichiers externes.

    La commande ```CREATE EXTERNAL FILE FORMAT``` est utilisée pour spécifier le format des données externes à partir duquel vous effectuez le chargement. Ainsi, nous avons utilisé deux formats pour stocker les données publiques relatives aux taxis de New York dans le stockage Blob Azure.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  Créez un schéma pour le format de fichier externe.

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. Créez les tables externes. Ces tables font référence à des données stockées dans HDFS ou dans le Stockage Blob Azure. 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
    (
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
    )
    WITH
    (
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>Create Table as Select (CTAS)

5. Chargez vos données à partir de tables externes dans votre instance SQL Data Warehouse. 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > Vous chargez plusieurs gigaoctets de données et les compressez au sein d’index de cluster columnstore hautes performances. Exécutez la requête DMV qui suit et installez-vous confortablement pendant qu’Azure SQL Data Warehouse se charge des tâches les plus difficiles.
    >

6. Créez une requête et observez l’arrivée des données grâce à cette vue DMV (Dynamic Management View).

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. Affichez toutes les requêtes du système.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. Vous pouvez constater que vos données sont efficacement chargées dans votre entrepôt Azure SQL Data Warehouse.

    ![Affichage des données chargées](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>Interrogation des données 

### <a name="scan-query-with-scaling"></a>Analyser la requête avec mise à l’échelle

Nous allons nous faire une bonne idée de l’effet de la mise à l’échelle sur la vitesse des requêtes.

Avant de commencer, réduisons la mise à l’échelle notre opération à 100 DWU, afin de déterminer de manière générale l’efficacité d’un nœud de calcul pris séparément.

1. Accédez au portail et sélectionnez votre instance SQL Data Warehouse.

2. Sélectionnez l’échelle dans le panneau SQL Data Warehouse. 

    ![Mise à l’échelle de l’instance SQL Data Warehouse dans le portail](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Effectuez la descente en puissance de la barre de performance jusqu’à atteindre 100 DWU, puis cliquez sur Enregistrer.

    ![Mise à l’échelle et enregistrement](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Attendez que l’opération de mise à l’échelle se termine.

    > [!NOTE]
    > Notez que les opérations de mise à l’échelle **tuent** vos requêtes en cours d’exécution et empêchent le lancement de nouvelles requêtes.
    >
    
5. Lancez une opération d’analyse sur les données de voyage, en sélectionnant le premier million d’entrées pour toutes les colonnes. Si vous souhaitez avancer rapidement, sélectionnez moins de lignes.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

Prenez note de la durée d’exécution de cette opération.

6. Faites monter votre instance en puissance, jusqu’à atteindre 400 DWU. Rappelez-vous que chaque tranche de 100 DWU entraîne l’ajout d’un autre nœud de calcul dans votre instance Azure SQL Data Warehouse.

7. Exécutez la requête à nouveau. Vous devriez remarquer une différence importante. 

> [!NOTE]
> Azure SQL Data Warehouse est une architecture à traitement massivement parallèle (MPP, Massively Parallel Processing). Les requêtes et les opérations capables de paralléliser les tâches parmi les différents nœuds peuvent tirer parti de la réelle puissance de la solution Azure SQL Data Warehouse.
>

### <a name="join-query-with-statistics"></a>Requête de jointure avec des statistiques

1. Exécutez une requête joignant la table des dates avec celles des voyages.

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    Comme vous pouvez l’imaginer, la requête est beaucoup plus longue lorsque vous répartissez les données entre les différents nœuds, en particulier dans un scénario de jointure, comme cette requête.

2. Voyons en quoi cette requête est différente lorsque nous créons des statistiques concernant la colonne que nous allons joindre, en exécutant les commandes suivantes :

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > Azure SQL Data Warehouse ne gère pas automatiquement les statistiques pour vous. Or, ces statistiques sont importantes pour déterminer les performances des requêtes. Il est donc fortement recommandé de créer et de mettre à jour les statistiques.
    > 
    > **Vous bénéficiez de performances optimales en lançant des statistiques sur les colonnes impliquées dans les jointures, celles utilisées dans la clause WHERE et celles figurant dans GROUP BY.**
    >

3. Exécutez à nouveau la requête depuis Composants requis et notez les différences en termes de performances. Certes, elles ne sont pas aussi visibles que dans le cas de la montée en puissance, mais vous devriez noter une nette accélération. 

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à lancer des requêtes et explorer les résultats. Découvrez nos meilleures pratiques et nos conseils !

Si vous avez terminé votre exploration, n’oubliez pas d’interrompre votre instance. Dans un environnement de production, vous pouvez réaliser des économies importantes en interrompant vos requêtes et en les mettant à l’échelle en fonction de vos besoins métier.

![Suspendre](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Documents utiles

[Gestion de la concurrence et des charges de travail]

[Meilleures pratiques pour Azure SQL Data Warehouse]

[surveillance des requêtes]

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse] (10 meilleures pratiques pour la création d’un entrepôt de données relationnelles à grande échelle)

[Migrating Data to Azure SQL Data Warehouse] (Migration de données vers Microsoft Azure SQL Data Warehouse)


[Gestion de la concurrence et des charges de travail]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Meilleures pratiques pour Azure SQL Data Warehouse]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[surveillance des requêtes]: sql-data-warehouse-manage-monitor.md
[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/ (10 meilleures pratiques pour la création d’un entrepôt de données relationnelles à grande échelle)
[Migrating Data to Azure SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/ (Migration de données vers Microsoft Azure SQL Data Warehouse)



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Composants requis]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO3-->


