---
title: Solution d’évaluation de la configuration dans Log Analytics | Microsoft Docs
description: La solution d’évaluation de la configuration dans Log Analytics fournit des informations détaillées sur l’état actuel de votre infrastructure serveur System Center Operations Manager lors de l’utilisation d’agents Operations Manager ou d’un groupe d’administration Operations Manager.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="configuration-assessment-solution-in-log-analytics"></a>Solution d’évaluation de la configuration dans Log Analytics
La solution d’évaluation de la configuration dans Log Analytics permet d’identifier les problèmes potentiels liés à la configuration des serveurs au moyen d’alertes et de recommandations de la Base de connaissances.

Cette solution nécessite System Center Operations Manager. Elle n’est pas disponible si vous utilisez uniquement des agents connectés directement.

L’affichage de certaines informations dans la solution d’évaluation de la configuration exige que votre navigateur soit doté du plug-in Silverlight.

> [!NOTE]
> Depuis le 5 juillet 2016, la solution d’évaluation de la configuration ne pouvait plus être ajoutée aux espaces de travail Log Analytics, et elle a cessé d’être disponible pour des utilisateurs existants après le 1er août 2016. Aux clients qui utilisaient cette solution pour SQL Server ou Active Directory, nous recommandons d’utiliser les solutions [SQL Server Assessment](log-analytics-sql-assessment.md), [Active Directory Assessment](log-analytics-ad-assessment.md) et [Active Directory Replication Status](log-analytics-ad-replication-status.md). Aux clients utilisant la solution d’évaluation de la configuration pour Windows, Hyper-V et System Center Virtual Machine Manager, nous recommandons d’utiliser les fonctionnalités de collecte d’événements et de suivi des modifications pour acquérir une vision globale des problèmes survenant dans leurs environnements.
> 
> 

![Vignette d’évaluation de la configuration](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

Les données de configuration sont collectées sur les serveurs analysés, puis envoyées au service cloud OMS pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. Les données traitées pour les serveurs sont présentées pour les domaines suivants :

* **Alertes :** affiche les alertes proactives liées à la configuration qui ont été générées pour vos serveurs surveillés. Celles-ci sont produites par des règles créées par l’organisation Microsoft Customer and Support Service (CSS) à partir de meilleures pratiques élaborées sur le terrain.
* **Recommandations de la Base de connaissances :** affiche les articles de la Base de connaissances Microsoft recommandés pour les charges de travail dans votre infrastructure ; ces articles sont suggérés automatiquement en fonction de votre configuration, par l’intermédiaire de Machine Learning.
* **Serveurs et charges de travail analysés :** affiche les serveurs et les charges de travail analysés par OMS.

![Tableau de bord d’évaluation de la configuration](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### <a name="technologies-you-can-analyze-with-configuration-assessment"></a>Technologies pouvant être analysées avec la solution d’évaluation de la configuration
La solution d’évaluation de la configuration OMS analyse les charges de travail suivantes :

* Windows Server 2012 et Microsoft Hyper-V Server 2012
* Windows Server 2008 et Windows Server 2008 R2, y compris :
  * Active Directory
  * Hôte Hyper-V
  * Système d’exploitation général
* SQL Server 2008 et versions ultérieures
  * Moteur de base de données SQL Server
* Microsoft SharePoint 2010
* Microsoft Exchange Server 2010 et Microsoft Exchange Server 2013
* Microsoft Lync Server 2013 et Lync Server 2010
* System Center 2012 SP1 – Virtual Machine Manager

Pour SQL Server, les éditions 32 bits et 64 bits suivantes sont prises en charge pour l’analyse :

* SQL Server 2016 (toutes les éditions)
* SQL Server 2014 (toutes les éditions)
* SQL Server 2008 et 2008 R2 (toutes les éditions)

Le moteur de base de données SQL Server est analysé sur toutes les éditions prises en charge. En outre, l’édition 32 bits de SQL Server est prise en charge lors de l’exécution dans l’implémentation WOW64.

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

* La solution d’évaluation de la configuration nécessite Operations Manager.
* Vous devez disposer d’un agent Operations Manager sur chaque ordinateur où vous souhaitez évaluer sa configuration.
* Ajoutez la solution d’évaluation de la configuration à votre espace de travail OMS en suivant la procédure décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).  Aucune configuration supplémentaire n’est requise.

## <a name="configuration-assessment-data-collection-details"></a>Détails de la collecte de données d’évaluation de la configuration
L’évaluation de la configuration collecte les données d’état, les métadonnées et les données de configuration à l’aide des agents que vous avez activés.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte en vue de l’évaluation de la configuration.

| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Non](./media/log-analytics-configuration-assessment/oms-bullet-red.png) |![Oui](./media/log-analytics-configuration-assessment/oms-bullet-green.png) |![Non](./media/log-analytics-configuration-assessment/oms-bullet-red.png) |![Oui](./media/log-analytics-configuration-assessment/oms-bullet-green.png) |![Oui](./media/log-analytics-configuration-assessment/oms-bullet-green.png) |deux fois par jour |

Le tableau suivant répertorie des exemples de types de données collectées par l’évaluation de la configuration :

| **Type de données** | **Champs** |
| --- | --- |
| Configuration |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| State |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="configuration-assessment-alerts"></a>Alertes d’évaluation de la configuration
Vous pouvez afficher et gérer les alertes de la solution d’évaluation de la configuration dans la page Alertes. Les alertes indiquent le problème qui a été détecté, sa cause et comment le résoudre. Elles fournissent également des informations sur les paramètres de configuration de votre environnement qui peuvent nuire aux performances.

![vue des alertes](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

> [!NOTE]
> Les alertes d’évaluation de la configuration sont différentes des autres alertes générées dans Log Analytics. L’affichage des alertes nécessite un plug-in Silverlight pour votre navigateur.
> 
> 

Quand vous sélectionnez un élément ou une catégorie d’éléments dans la page Alertes, vous voyez une liste de serveurs ou de charges de travail avec les alertes qui s’y rapportent.

![liste d’alertes](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

Chaque alerte inclut un lien vers un article de la Base de connaissances Microsoft. Ces articles fournissent des informations supplémentaires sur l’alerte.

> [!TIP]
> Par défaut, le nombre maximal d’alertes affichées est de 2 000. Pour afficher davantage d’alertes, cliquez sur la barre de notification au-dessus de la liste des alertes.
> 
> 

Vous pouvez cliquer sur n’importe quel élément de la liste pour afficher l’article de la Base de connaissances, qui peut vous aider à résoudre la cause du problème ayant généré l’alerte.

![article de la Base de connaissances](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

Vous pouvez gérer les règles d’alerte pour ignorer des règles spécifiques ou une classe de règles.

![gérer les règles d’alerte](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## <a name="knowledge-recommendations"></a>Recommandations de la Base de connaissances
Quand vous affichez les recommandations de la Base de connaissances, les résultats de la recherche de journal répertorient des articles de la Base de connaissances Microsoft. Ceux-ci, recommandés pour des charges de travail et des ordinateurs spécifiques, fournissent des informations supplémentaires sur l’alerte.

![résultats de la recherche pour les recommandations de la Base de connaissances](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## <a name="servers-and-workloads-analyzed"></a>Serveurs et charges de travail analysés
Quand vous affichez les recommandations de la Base de connaissances, les résultats de la recherche de journal répertorient l’ensemble des serveurs et des charges de travail provenant d’Operations Manager qui sont connus d’OMS.

![Serveurs et charges de travail](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## <a name="next-steps"></a>Étapes suivantes
* Utilisez les [Recherches de journaux dans Log Analytics](log-analytics-log-searches.md) pour afficher les données détaillées de l’évaluation de la configuration.

<!--HONumber=Oct16_HO2-->


