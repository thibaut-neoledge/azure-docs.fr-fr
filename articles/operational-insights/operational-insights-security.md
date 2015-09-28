<properties
	pageTitle="Sécurité Operational Insights"
	description="Découvrez comment Operational Insights protège vos données personnelles et sécurise vos données."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="dev-center-name"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/10/2015"
	ms.author="banders"/>

# Sécurité Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft s'engage à protéger votre vie privée et à sécuriser vos données, tout en proposant des logiciels et des services favorisant la gestion de l'infrastructure informatique de votre organisation. Nous reconnaissons que lorsque vous confiez vos données à d'autres personnes, cette confiance exige une sécurité rigoureuse. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service.

La sécurisation et la protection des données constituent une priorité de premier plan pour Microsoft. N'hésitez pas à nous contacter pour toute question, suggestion ou remarque concernant les informations fournies ci-dessous, y compris notre politique de sécurité à la page [Options de support Azure](http://azure.microsoft.com/support/options/).

Cet article explique comment les données sont collectées, traitées et sécurisées dans OMS (Operations Management Suite). Ce service s'appelait auparavant Microsoft Azure Operational Insights. Vous pouvez utiliser les deux agents pour vous connecter directement au service web ou vous pouvez utiliser System Center Operations Manager pour collecter des données opérationnelles pour le service OMS. Les données collectées sont envoyées via Internet au service OMS, qui est hébergé dans Microsoft Azure.

Le service OMS gère vos données en toute sécurité en utilisant les méthodes suivantes :

**Ségrégation des données :** les données client sont maintenues séparées logiquement sur chaque composant, dans l'ensemble du service OMS. Toutes les données sont balisées en fonction de l'organisation. Ce balisage est conservé tout au long du cycle de vie des données, et il est appliqué dans chaque couche du service.

Chaque client possède un objet blob Azure dédié qui héberge les données à long terme. Cet objet blob est chiffré à l'aide de clés uniques propres au client, qui sont modifiées tous les 90 jours.

**Rétention des données :** des métriques agrégées pour certaines solutions (anciennement appelées « packs d’analyse décisionnelle »), par exemple la gestion de la capacité, sont stockées dans une base de données SQL hébergée par Microsoft Azure. Ces données sont stockées 390 jours. Les données indexées de recherche dans les journaux sont stockées et conservées en fonction du plan de tarification. Pour plus d’informations, reportez-vous à la [page de tarification](http://azure.microsoft.com/pricing/details/operational-insights/).

**Sécurité physique :** le fonctionnement du service OMS est assuré par le personnel de Microsoft et toutes les activités sont consignées et peuvent faire l'objet d'un audit. Le service OMS s'exécute entièrement dans Azure et satisfait aux critères de conception communs d'Azure. Vous pouvez afficher plus d'informations sur la sécurité physique des ressources Azure à la page 18 du document [Vue d'ensemble de la sécurité de Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf).

**Conformité et certifications :** l'équipe de développement et de maintenance du service OMS travaille en étroite collaboration avec les équipes Microsoft chargées des aspects juridiques et de conformité, ainsi qu'avec d'autres partenaires du secteur, afin d'acquérir un éventail de certifications, notamment ISO.

Les normes de sécurité suivantes sont actuellement satisfaites :

- Critères de conception communs Windows
- Certification Trustworthy Computing de Microsoft


## Sécurité des flux de données
Le diagramme suivant illustre le flux d'informations provenant de votre entreprise et la manière dont il est sécurisé à mesure qu'il progresse vers le service OMS, avant son affichage final dans OMS. Des informations complémentaires sur chaque étape sont présentées à suite du diagramme.

![Image de la collecte de données OMS et de la sécurité](./media/operational-insights-security/security.png)

### 1\. S'inscrire au service OMS et collecter des données

Pour que votre organisation puisse envoyer des données au service OMS, vous devez configurer des agents Microsoft Monitoring lorsque vous vous connectez directement au service web ou utilisez un Assistant de configuration dans la console Operations d'Operations Manager. Les utilisateurs (vous-même, d'autres utilisateurs ou un groupe de personnes) doivent créer un ou plusieurs comptes OMS et inscrire chaque agent directement connecté ou leur environnement Operations Manager à l'aide d'un des comptes suivants :


- [ID d'organisation](../sign-up-organization.md)

- [Compte Microsoft - Outlook, Office Live, MSN](../sign-up-organization.md)

Un compte OMS est l'emplacement où les données sont collectées, agrégées, analysées et présentées. Un compte OMS sert essentiellement à partitionner les données, et chaque compte OMS est unique. Par exemple, vous pouvez souhaiter que vos données de production soient gérées avec un compte OMS et que vos données de test soient gérées avec un autre compte. En outre, les comptes aident un administrateur à contrôler l'accès des utilisateurs aux données. Chaque compte OMS peut être associé à plusieurs comptes d'utilisateur et chaque compte d'utilisateur peut posséder plusieurs comptes OMS.

Quand l'Assistant de configuration est terminé, chaque groupe d'administration Operations Manager établit une connexion avec le service OMS. Vous utilisez ensuite l'Assistant Ajouter des ordinateurs pour choisir parmi les ordinateurs du groupe d'administration ce qui sont autorisés à envoyer des données au service.

Les deux types d'agents collectent des données pour OMS. Le type de données collectées dépend des types de solutions utilisés. Une solution est un ensemble de vues prédéfinies, de requêtes de recherche de journal, de règles de collecte de données et de logique de traitement. Seuls les administrateurs OMS peuvent utiliser OMS pour importer une solution. Une fois la solution importée, elle est déplacée vers les serveurs d'administration Operations Manager (éventuellement utilisés), puis vers les agents Operations Manager que vous avez choisis. Ensuite, les agents collectent les données.

Le tableau suivant répertorie les solutions disponibles dans OMS et les types de données qu'elles recueillent.


|**Solution**|**Types de données**|
|---|---|
|Évaluation de la configuration|Données de configuration, métadonnées et données d'état|
|Planification de la capacité|données de performances, métadonnées et données d'état|
|Logiciel anti-programme malveillant|Données de configuration, métadonnées et données d'état|
|Évaluation des mises à jour du système|Métadonnées et données d'état|
|Gestion du journal|Journaux des événements définis par l'utilisateur|
|Suivi des modifications|Inventaire logiciel et métadonnées de service Windows|
|Évaluation de SQL et d'Active Directory|Données WMI, données du Registre, données de performances et résultats de vues de gestion dynamique de SQL Server|



Le tableau suivant répertorie des exemples de types de données :

|**Type de données**|**Champs**|
|---|---|
|Alerte|Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount|
|Configuration|CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate|
|Événement|EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded **Remarque :** *si vous consignez des événements avec des champs personnalisés dans le journal des événements Windows, OMS collecte ces événements.*|
|Metadata|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Performances|ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded|
|État|StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|


### 2\. Envoyer des données à partir d'agents

Dans le cas d'un agent qui se connecte directement au service web, enregistrez-le avec une clé, puis une connexion sécurisée est établie entre l'agent et le service OMS via le port 443.

Avec Operations Manager, vous enregistrez un compte auprès du service OMS, puis une connexion HTTPS sécurisée est établie entre le serveur d'administration Operations Manager et le service OMS via le port 443. Si Operations Manager ne peut pas communiquer avec le service pour une raison quelconque, les données collectées sont stockées dans un cache temporaire et le serveur d'administration essaie de renvoyer les données toutes les 8 minutes pendant 2 heures. Comme les données collectées sont compressées et envoyées au service OMS en ignorant les bases de données locales, celles-ci ne subissent aucune charge supplémentaire. Une fois que les données collectées sont envoyées, elles sont supprimées du cache.

### 3\. Le service OMS reçoit et traite les données

Le service OMS s'assure que les données entrantes proviennent d'une source approuvée en validant des certificats et l'intégrité des données. Les données brutes non traitées sont ensuite stockées sous la forme d'un objet blob dans [Microsoft Azure Storage](http://azure.microsoft.com/documentation/services/storage/). Chaque utilisateur OMS dispose d'un objet blob Azure dédié, auquel lui seul a accès. Le type de données stockées dépend des types de solutions qui ont été importés et utilisés pour collecter des données.

Le service OMS traite les données brutes, puis les données traitées agrégées sont stockées dans une base de données SQL. La communication entre le service OMS et la base de données SQL s'appuie sur l'authentification de base de données SQL.

### 4\. Utiliser OMS pour accéder aux données

Vous pouvez vous connecter à OMS en utilisant le compte que vous avez défini précédemment. Tout le trafic entre OMS et le service OMS est envoyé via un canal HTTPS sécurisé.

<!---HONumber=Sept15_HO3-->