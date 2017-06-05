---
title: "Sécurité des données Log Analytics | Microsoft Azure"
description: "Découvrez comment Log Analytics protège vos données personnelles et sécurise vos données."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: fa33a031a9e05f3079f1ed68d2ac0902b3070fa6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/04/2017


---
# <a name="log-analytics-data-security"></a>Sécurité des données Log Analytics
Microsoft s'engage à protéger votre vie privée et à sécuriser vos données, tout en proposant des logiciels et des services favorisant la gestion de l'infrastructure informatique de votre organisation. Nous reconnaissons que lorsque vous confiez vos données à d'autres personnes, cette confiance exige une sécurité rigoureuse. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service.

La sécurisation et la protection des données constituent une priorité de premier plan pour Microsoft. Contactez-nous pour toute question, suggestion ou remarque concernant les informations fournies ci-dessous, y compris notre politique de sécurité dans [Options de support Azure](http://azure.microsoft.com/support/options/).

Cet article explique comment les données sont collectées, traitées et sécurisées par Log Analytics dans Operations Management Suite (OMS). Vous pouvez utiliser des agents pour la connexion au service web, utiliser System Center Operations Manager pour collecter des données opérationnelles ou récupérer des données à partir des diagnostics Azure pour une utilisation par Log Analytics. Les données collectées sont envoyées via Internet au service Log Analytics hébergé dans Microsoft Azure, à l’aide d’une authentification basée sur les certificats et du protocole SSL 3. Les données sont compressées par l’agent avant leur envoi.

Le service Log Analytics gère vos données basées sur le cloud en toute sécurité en utilisant les méthodes suivantes :

* ségrégation des données
* conservation des données
* sécurité physique
* gestion des incidents
* conformité
* certifications relatives aux normes de sécurité

## <a name="data-segregation"></a>Ségrégation des données
Les données client sont maintenues séparées logiquement sur chaque composant, dans l’ensemble du Service OMS. Toutes les données sont balisées en fonction de l'organisation. Ce balisage est conservé tout au long du cycle de vie des données, et il est appliqué dans chaque couche du service. Chaque client possède un objet blob Azure dédié qui héberge les données à long terme.

## <a name="data-retention"></a>Conservation des données
Les données indexées de recherche dans les journaux sont stockées et conservées en fonction de votre plan de tarification. Pour plus d’informations, consultez [Tarification - Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft supprime les données client 30 jours après la fermeture de l’espace de travail OMS. Microsoft supprime également le compte de stockage Azure où résident les données. La suppression des données client n’entraîne pas de destruction de lecteurs physiques.

Le tableau suivant répertorie certaines des solutions disponibles dans OMS et fournit des exemples de types de données collectées.

| **Solution** | **Types de données** |
| --- | --- |
| Évaluation de la configuration |Données de configuration, métadonnées et données d'état |
| Planification de la capacité |Données et métadonnées de performances |
| Logiciel anti-programme malveillant |Données et métadonnées de configuration |
| Évaluation des mises à jour du système |Métadonnées et données d'état |
| Gestion du journal |Journaux des événements défini par l’utilisateur, journaux des événements Windows et/ou journaux IIS |
| Suivi des modifications |Inventaire logiciel et métadonnées de service Windows |
| Évaluation de SQL et d'Active Directory |Données WMI, données du Registre, données de performances et résultats de vues de gestion dynamique de SQL Server |

Le tableau suivant répertorie des exemples de types de données :

| **Type de données** | **Champs** |
| --- | --- |
| Alerte |Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuration |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Événement |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Remarque :** quand vous consignez des événements avec des champs personnalisés dans le journal des événements Windows, OMS les collecte. |
| Métadonnées |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Performances |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| État |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Sécurité physique
Le fonctionnement de Log Analytics dans le Service OMS est géré par le personnel de Microsoft. Toutes les activités sont consignées et peuvent faire l’objet d’un audit. Le service s’exécute entièrement dans Azure et satisfait aux critères de conception communs d’Azure. Vous pouvez afficher plus d'informations sur la sécurité physique des ressources Azure à la page 18 du document [Vue d'ensemble de la sécurité de Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Les droits d’accès physique aux zones sécurisées sont modifiés en l’espace d’un jour ouvrable pour toute personne n’assumant plus de responsabilité en relation avec le Service OMS, transfert et arrêt compris. Pour en savoir plus sur notre infrastructure physique globale, découvrez les [Centre de données Microsoft](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Gestion des incidents
OMS inclut un processus de gestion des incidents auquel tous les services Microsoft adhèrent. Pour résumer :

* Nous appliquons un modèle de responsabilité partagée en vertu duquel une partie de la sécurité relève de la responsabilité de Microsoft, et une autre de celle du client.
* Nous gérons les incidents de sécurité liés à Azure.
  * Nous lançons une investigation lors de la détection d’un incident.
  * Nous évaluons l’impact et la gravité d’un incident via l’intervention d’un membre de l’équipe chargée de répondre aux appels relatifs aux incidents. Selon les éléments de preuve apportés, l’évaluation peut ou non entraîner une réaffectation à l’équipe chargée de répondre aux problèmes de sécurité.
  * Nous diagnostiquons un incident en demandant à des experts en réponse aux problèmes de sécurité de mener les investigations informatiques ou techniques nécessaire, et d’identifier des stratégies d’endiguement, d’atténuation et de contournement. Si l’équipe de sécurité estime que des données client risquent d’être exposées à une personne illégitime ou non autorisée, le processus de notification d’incident au client commence en parallèle.  
  * Nous stabilisons l’incident et assurons la récupération. L’équipe de réponse aux incidents crée un plan de récupération pour atténuer le problème. Des mesures d’endiguement de crise telles que la mise en quarantaine des systèmes affectés peuvent être prises immédiatement et parallèlement au diagnostic. Des mesures d’atténuation à plus long terme peuvent être planifiées, qui se interviennent une fois passé le risque immédiat.  
  * Nous clôturons l’incident, puis établissons un bilan. L’équipe de réponse aux incidents dresse un bilan décrivant l’incident en détail, dans le but de réviser les stratégies, procédures et processus de façon appropriée afin d’éviter que l’événement se reproduise.
* Nous informons les clients concernant les incidents de sécurité.
  * Nous identifions les clients affectés, et communiquons à toute personne concernée une notice aussi détaillée que possible.
  * Nous rédigeons une notice pour fournir aux clients des informations suffisamment détaillées pour qu’ils puissent mener une investigation de leur côté et respecter les engagements qu’ils ont envers leurs utilisateurs finaux sans retarder indûment le processus de notification.
  * Nous confirmons et déclarons l’incident si nécessaire.
  * Nous avertissons les clients à l’aide d’une notification d’incident sans délai déraisonnable, et conformément aux obligations légales ou contractuelles applicables. Les notifications d’incidents de sécurité sont adressées à un ou plusieurs des administrateurs du client par tout moyen que Microsoft juge opportun, y compris par courrier électronique.
* Nous assurons la formation et la préparation de notre équipe
  * Le personnel de Microsoft est tenu de suivre une formation en matière de sécurité et de sensibilisation, qui l’aide à identifier et à signaler d’éventuels problèmes liés à la sécurité.  
  * Les opérateurs du service Microsoft Azure sont soumis à des obligations de formation supplémentaires en relation avec leur accès aux systèmes sensibles d’hébergement des données client.
  * Le personnel de réponse aux problèmes de sécurité de Microsoft reçoit une formation spécialisée en relation avec les rôles qui lui sont confiés.

En cas de perte de données client, nous avertissons chaque client dans la journée. Toutefois, aucune perte de données client ne s’est jamais produite avec OMS. Par ailleurs, nous conservons des copies de données créées, qui sont distribuées géographiquement.

Pour plus d’informations sur la façon dont Microsoft répond aux incidents de sécurité, voir [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf) (en anglais).

## <a name="compliance"></a>Conformité
Le programme de sécurité et de gouvernance des informations de l’équipe de maintenance et de développement logiciel du Service OMS prend en charge ses besoins métier, et respecte les lois et réglementations décrites sur les sites [Centre de gestion de la confidentialité Microsoft Azure](https://azure.microsoft.com/support/trust-center/) et [Microsoft Trust Center Compliance](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Ceux-ci expliquent également comment OMS fixe les exigences de sécurité, identifie les contrôles de sécurité, et gère et analyse les risques. Chaque année, nous révisons les stratégies, normes, procédures et directives.

Chaque membre de l’équipe de développement d’OMS reçoit une formation formelle en matière de sécurité des applications. En interne, nous utilisons un système de contrôle de version pour le développement de logiciels. Chaque projet de logiciel est protégé par le système de contrôle de version.

Microsoft dispose d’une équipe dédiée à la sécurité et à la conformité, qui surveille et évalue tous les services en son sein. Les responsables de la sécurité des informations qui constituent l’équipe ne sont pas associés aux services d’ingénierie qui développent OMS. Les responsables de la sécurité ont leur propre chaîne de gestion, et effectuent des évaluations indépendantes des produits et services pour s’assurer de la sécurité et de la conformité de ceux-ci.

Le conseil d’administration de Microsoft est informé via un rapport annuel de l’ensemble des programmes relatifs à la sécurité des informations mis en œuvre au sein de Microsoft.

L’équipe de développement et de maintenance du Service OMS travaille en étroite collaboration avec les équipes Microsoft chargées des aspects juridiques et de conformité, ainsi qu’avec d’autres partenaires du secteur, afin d’acquérir un éventail de certifications.

## <a name="certifications-and-attestations"></a>Certifications et attestations
OMS Log Analytics répond aux exigences suivantes :

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/en-us/blog/iso22301/)
* [Normes de sécurité sur les données de l’industrie des cartes de paiement (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) édictées par le PCI Security Standards Council (conseil de normalisation pour la sécurité des données de l’ICP).
* [SOC 1 Type 1 et SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2).
* [HIPAA et Hi-TECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) pour les organisations qui disposent d’un contrat HIPAA Business Associate Agreement
* Critères de conception communs Windows
* Microsoft Trustworthy Computing
* En tant que service Azure, OMS utilise des composants conformes aux exigences d’Azure. Pour en savoir plus, voir le site du [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).

> [!NOTE]
> Dans certaines certifications/attestations, Log Analytics est mentionné sous son ancien nom, *Operational Insights*.
>
>


## <a name="cloud-computing-security-data-flow"></a>Flux de données de sécurité du cloud computing
Le diagramme suivant montre une architecture de sécurité cloud comme étant le flux d’informations provenant de votre entreprise et la manière dont il est sécurisé à mesure qu’il progresse vers le service Log Analytics, avant son affichage final dans le portail OMS. Des informations complémentaires sur chaque étape sont présentées à suite du diagramme.

![Image de la collecte de données OMS et de la sécurité](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. S’inscrire à Log Analytics et collecter des données
Vous configurez des agents Windows, des agents s’exécutant sur des machines virtuelles Azure ou des agents Linux pour Linux afin que votre organisation envoie des données à Log Analytics. Si vous utilisez des agents Operations Manager, vous utilisez un assistant de configuration dans la console Opérateur pour les configurer. Les utilisateurs (vous-même, d’autres utilisateurs ou un groupe de personnes) doivent créer un ou plusieurs comptes OMS (espaces de travail OMS), et inscrire des agents à l’aide d’un des comptes suivants :

* [ID d'organisation](../active-directory/sign-up-organization.md)
* [Compte Microsoft - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Un espace de travail OMS est l’emplacement où les données sont collectées, agrégées, analysées et présentées. Un espace de travail sert essentiellement à partitionner les données, et chaque espace de travail est unique. Par exemple, vous pouvez souhaiter que vos données de production soient gérées avec un espace de travail OMS et que vos données de test soient gérées avec un autre espace de travail. En outre, les espaces de travail aident un administrateur à contrôler l’accès des utilisateurs aux données. Chaque espace de travail peut être associé à plusieurs comptes d’utilisateur, et chaque compte d’utilisateur peut accéder à plusieurs espaces de travail OMS. Vous créez des espaces de travail en fonction d’une région de centre de données. Chaque espace de travail est répliqué vers d’autres centres de données dans la région, principalement à des fins de disponibilité du Service OMS.

Pour Operations Manager, quand l’assistant de configuration est terminé, chaque groupe d’administration Operations Manager établit une connexion avec le service Log Analytics. Vous utilisez ensuite l'Assistant Ajouter des ordinateurs pour choisir parmi les ordinateurs du groupe d'administration ce qui sont autorisés à envoyer des données au service. Pour les autres types d’agent, chacun se connecte en toute sécurité au service OMS.

Toutes les communications entre les systèmes connectés et le service Log Analytics sont chiffrées.  Le protocole TLS (HTTPS) est utilisé pour le chiffrement.  Le processus Microsoft SDL est suivi pour s’assurer que Log Analytics est à jour avec les dernières avancées en matière de protocoles cryptographiques.

Chaque type d’agent collecte des données pour Log Analytics. Le type des données collectées dépend des types de solutions utilisés. Pour un récapitulatif de la collecte de données, voir [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Par ailleurs, des informations plus détaillées concernant la collecte sont disponibles pour la plupart des solutions. Une solution est un ensemble de vues prédéfinies, de requêtes de recherche de journal, de règles de collecte de données et de logique de traitement. Seuls les administrateurs peuvent utiliser Log Analytics pour importer une solution. Une fois la solution importée, elle est déplacée vers les serveurs d’administration Operations Manager (éventuellement utilisés), puis vers les agents que vous avez choisis. Ensuite, les agents collectent les données.

## <a name="2-send-data-from-agents"></a>2. Envoyer des données à partir d'agents
Vous enregistrez tous les types d’agents avec une clé d’inscription et une connexion sécurisée est établie entre l’agent et le service Log Analytics à l’aide de l’authentification par certificat et SSL avec le port 443. OMS utilise un magasin des secrets pour générer et gérer les clés. Les clés privées sont remplacées tous les 90 jours, stockées dans Azure, et gérées par des opérations Azure qui suivent des pratiques réglementaires et de conformité strictes.

Avec Operations Manager, vous enregistrez un espace de travail auprès du service Log Analytics, puis une connexion HTTPS sécurisée est établie avec le serveur d’administration Operations Manager.

Pour les agents Windows s’exécutant sur des machines virtuelles Azure, une clé de stockage en lecture seule est utilisée pour lire des événements de diagnostic dans des tables Azure.

Si un agent ne peut pas communiquer avec le service pour une raison quelconque, les données collectées sont stockées localement dans un cache temporaire, et le serveur d’administration essaie de renvoyer les données toutes les huit minutes pendant deux heures. Les données de l’agent mises en cache sont protégées par le magasin d’informations d’identification du système d’exploitation. Si le service ne peut pas traiter les données au bout de deux heures, les agents mettent celles-ci en file d’attente. En cas de saturation de la file d’attente, OMS commence à supprimer certains types de données, en commençant par les données de performances. La limite de file d’attente de l’agent est une clé de Registre que vous pouvez modifier si nécessaire. Comme les données collectées sont compressées et envoyées au service en ignorant les bases de données locales, celles-ci ne subissent aucune charge supplémentaire. Une fois que les données collectées sont envoyées, elles sont supprimées du cache.

Comme décrit ci-dessus, les données provenant de vos agents sont envoyées via le protocole SSL aux centres de données Microsoft Azure. Vous pouvez également utiliser ExpressRoute pour sécuriser davantage les données. ExpressRoute vous permet de vous connecter directement à Azure à partir de votre réseau étendu (WAN) existant, comme un VPN MPLS fourni par un fournisseur de services réseau. Pour plus d’informations, voir [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Le service Log Analytics reçoit et traite les données
Le service Log Analytics s’assure que les données entrantes proviennent d’une source approuvée en validant des certificats et l’intégrité des données à l’aide de la certification Azure. Les données brutes non traitées sont ensuite stockées sous la forme d’un objet blob dans [Microsoft Stockage Azure](../storage/storage-introduction.md), et ne sont pas chiffrées. Toutefois, chaque Azure Storage Blob dispose d’un ensemble unique de clés auquel seul cet utilisateur peut accéder. Le type des données stockées dépend des types de solutions qui ont été importées et utilisées pour collecter des données. Ensuite, le service Log Analytics traite les données brutes pour l’Azure Storage Blob.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Utiliser Log Analytics pour accéder aux données
Vous pouvez vous connecter à Log Analytics dans le portail OMS à l’aide du compte professionnel ou d’un compte Microsoft que vous avez précédemment configuré. Tout le trafic entre le portail OMS et Log Analytics dans OMS est envoyé via un canal HTTPS sécurisé. Lorsque vous utilisez le portail OMS, un ID de session est généré sur le client utilisateur (navigateur web), et les données sont stockées dans un cache local jusqu’à la fin de la session. Ensuite, le cache est supprimé. Les cookies côté client qui ne contiennent pas d’informations d’identification personnelle ne sont pas supprimés automatiquement. Les cookies de session sont marqués HTTPOnly et sécurisés. Après une période d’inactivité prédéfinie, il est mis fin à la session du portail OMS.

Le portail OMS vous permet d’exporter des données vers un fichier CSV, et vous pouvez accéder à des données à l’aide d’API de recherche. L’exportation de fichiers CSV est limitée à 50 000 lignes par exportation, et les données API sont limitées à 5 000 lignes par recherche.

## <a name="next-steps"></a>Étapes suivantes
* [Familiarisez-vous avec Log Analytics](log-analytics-get-started.md) pour en savoir plus sur Log Analytics et être opérationnel en quelques minutes.

