---
title: Ajout de solutions de gestion Azure Log Analytics | Microsoft Docs
description: "Les solutions de gestion Operations Management Suite (OMS)/Log Analytics représentent une collection de règles logiques, de visualisation et d’acquisition des données qui fournissent des mesures cernant un domaine problématique en particulier."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: c187467e8af7503ea22c5953417d8282be97856c
ms.lasthandoff: 03/17/2017


---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>Ajout de solutions de gestion Azure Log Analytics dans votre espace de travail

Les solutions de gestion Log Analytics représentent une collection de **règles logiques**, de **visualisation** et **d’acquisition des données** qui fournissent des mesures cernant un domaine problématique en particulier. Cet article répertorie les solutions de gestion prises en charge par Log Analytics et vous montre comment les ajouter et les supprimer pour un espace de travail à l’aide du portail Azure. Vous pouvez également ajouter des solutions dans le portail OMS à l’aide de la galerie de solutions.

Les solutions de gestion vous offrent des informations plus approfondies pour :

* étudier et résoudre plus rapidement les problèmes opérationnels
* collecter et corréler les différents types de données machine
* vous aider à être proactif avec des activités exposées par la solution.

> [!NOTE]
> Comme Log Analytics inclut une fonctionnalité Recherche de journal, vous n’avez pas besoin d’installer une solution de gestion pour l’activer. Toutefois, en ajoutant des solutions de gestion dans votre espace de travail, vous obtenez des visualisations de données, des recherches suggérées et des informations.

À l’aide de cet article, vous ajoutez des solutions de gestion à un espace de travail à l’aide du portail Azure Marketplace. Après avoir ajouté une solution, les données sont collectées à partir des serveurs dans votre infrastructure et envoyées au service OMS. Le traitement par le service OMS prend généralement quelques minutes à une heure. Une fois les données traitées par le service, vous pouvez les consulter dans OMS.

Vous pouvez facilement supprimer une solution de gestion quand vous n’en avez plus besoin. Lorsque vous supprimez une solution de gestion, ses données ne sont pas envoyées à OMS. Si vous bénéficiez du niveau tarifaire Gratuit, la suppression d’une solution peut réduire la quantité de données utilisées, ce qui vous évitera de dépasser le quota quotidien de données.

## <a name="view-available-management-solutions"></a>Afficher les solutions de gestion disponibles

Azure Marketplace contient la liste des [solutions de gestion pour Log Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Vous pouvez installer des solutions de gestion à partir d’Azure Marketplace en cliquant sur le lien **Obtenir maintenant** au bas de chaque solution.

## <a name="add-a-management-solution"></a>Ajout d’une solution de gestion
1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre abonnement Azure.
2. Dans le panneau **Nouveau** sous **Marketplace**, sélectionnez **Surveillance + gestion**.
3. Dans le panneau **Surveillance + gestion**, cliquez sur **Afficher tout**.  
    ![Panneau Surveillance + gestion](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. À droite de **Solutions de gestion**, cliquez sur **Plus**.
5. Dans le panneau **Solutions de gestion**, sélectionnez une solution de gestion à ajouter à un espace de travail.  
    ![Panneau Surveillance + gestion](./media/log-analytics-add-solutions/management-solutions.png)  
6. Dans le panneau de la solution de gestion, passez en revue les informations la concernant, puis cliquez sur **Créer**.
7. Dans le panneau *Nom de la solution gestion*, sélectionnez un espace de travail à associer à la solution de gestion.
8. Vous pouvez également modifier les paramètres de l’espace de travail concernant l’abonnement Azure, le groupe de ressources et l’emplacement. Vous pouvez également choisir les **options d’automatisation**. Cliquez sur **Create**.  
    ![espace de travail de la solution](./media/log-analytics-add-solutions/solution-workspace.png)  
9. Pour utiliser la solution de gestion que vous avez ajoutée à votre espace de travail, accédez à **Log Analytics** > **Abonnements** > ***nom de l’espace de travail*** > **Vue d’ensemble**. Une nouvelle mosaïque pour votre solution de gestion s’affiche. Cliquez sur la mosaïque pour l’ouvrir et démarrer la solution une fois les données de la solution recueillies.

## <a name="remove-a-management-solution"></a>Suppression d’une solution de gestion

1. Dans le [portail Azure](https://portal.azure.com), accédez à **Log Analytics** > **Abonnements** > ***nom de l’espace de travail*** puis, dans le panneau ***nom de l’espace de travail***, cliquez sur **Solutions**.
2. Dans la liste des solutions de gestion, sélectionnez la solution à supprimer.
3. Dans le panneau de la solution de votre espace de travail, cliquez sur **Supprimer**.  
    ![supprimer la solution](./media/log-analytics-add-solutions/solution-delete.png)  
4. Cliquez sur **Oui** dans la boîte de dialogue de confirmation.

## <a name="offers-and-pricing-tiers"></a>Offres et niveaux tarifaires

Le tableau suivant indique les solutions de gestion appartenant à chaque offre de sécurité et Operations Management.
Il indique également les niveaux tarifaires disponibles pour chaque solution de gestion.
Toutes les solutions indiquées dans le tableau suivant sont disponibles dans le Portail Azure et la galerie de solutions du portail Log Analytics.

| Solution de gestion                                                                        | Offer                                                                     | Niveaux tarifaires<sup>1</sup>                                                    | Remarques |
| ---                                                                                        | ---                                                                       | ---                                                                                                       | ---   |
| Log Analytics des activités                                                                     | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | 90 jours de données disponibles gratuitement |
| [Évaluation d'AD](log-analytics-ad-assessment.md)                                              | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [État de la réplication AD](log-analytics-ad-replication-status.md)                              | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| Intégrité de l’agent                                                                                  | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [Gestion des alertes](log-analytics-solution-alert-management.md)                              | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| Connecteur Application Insights <br>(version préliminaire)                                                 | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>    | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| Automation Hybrid Worker                                                                     | <ul><li>Automation & Control</li></ul>                                    | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                            | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |
| [Azure Application Gateway Analytics](log-analytics-azure-networking-analytics.md)      | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [Azure Network Security Group Analytics](log-analytics-azure-networking-analytics.md)      | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [Azure SQL Analytics (version préliminaire)](log-analytics-azure-sql.md)                                                          | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br>Par&nbsp;nœud&nbsp;(OMS)                                                                             | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation|
| Sauvegarde                                                                                    | <ul><li>Insight & Analytics</li></ul>                                     | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                            | Nécessite un archivage de sauvegarde classique |
| Capacité et performances <br>(version préliminaire)                                                     | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [Suivi des modifications](log-analytics-change-tracking.md)                                         | <ul><li>Automation & Control</li></ul>                                    | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                            | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |
| [Conteneurs](log-analytics-containers.md)                                                  | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| Surveillance HDInsight HBase <br>(version préliminaire)                                                     | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [Analytique Key Vault](log-analytics-azure-key-vault.md)                      | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| Logic Apps B2B                      | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [Évaluation des logiciels malveillants](log-analytics-malware.md)                                             | <ul><li>Sécurité et conformité</li></ul>                                    | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                               | |
| [Analyseur de performances réseau](log-analytics-network-performance-monitor.md) <br>(version préliminaire)    | <ul><li>Insight & Analytics</li></ul>                                     | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | |
| Office 365 Analytics <br>(version préliminaire)                                                        | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [Sécurité et audit](../operations-management-suite/oms-security-getting-started.md)        | <ul><li>Security&nbsp;&&nbsp;Compliance</li></ul>                        | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                               | La collecte des journaux des événements de sécurité requiert cette solution |
| [Service Fabric](log-analytics-service-fabric.md) <br>(version préliminaire)                    | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [Carte de service](../operations-management-suite/operations-management-suite-service-map.md) <br>(version préliminaire)| <ul><li>Insight & Analytics</li></ul>                         | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | Disponible dans les régions États-Unis de l’Est et Europe de l’Ouest     |
| Site Recovery                                                                                | <ul><li>Insight & Analytics</li></ul>                                     | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                            | Nécessite un coffre Site Recovery classique |
| [Évaluation de SQL](log-analytics-sql-assessment.md)                                            | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| Démarrer/arrêter des machines virtuelles pendant les heures creuses<br>(version préliminaire)                                                 | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |
| [SurfaceHub](log-analytics-surface-hubs.md)                                                    | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| [System Center Operations Manager Assessment](log-analytics-scom-assessment.md) <br>(version préliminaire) | <ul><li>Insight & Analytics</li><li>Log Analytics</li></ul>         | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| Gestion des mises à jour                                                                               | <ul><li>Automation & Control</li></ul>                                    | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                            | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |
| [Conformité de la mise à jour](https://technet.microsoft.com/en-us/itpro/windows/manage/update-compliance-get-started) <br>(version préliminaire)                                                             | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | Pas de frais pour les données ou les nœuds |
| [Préparation à la mise à niveau](https://technet.microsoft.com/itpro/windows/deploy/manage-windows-upgrades-with-upgrade-readiness)                                                             | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | Pas de frais pour les données ou les nœuds |
| [Surveillance VMware](log-analytics-vmware.md) <br>(version préliminaire)                                 | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| Données de communication 2.0<br>(version préliminaire)                                                                   | <ul><li>Insight & Analytics</li></ul>                                      | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | Disponible dans les régions États-Unis de l’Est et Europe de l’Ouest |

<sup>1</sup> Les niveaux tarifaires *Standard* et *Premium (OMS)* sont disponibles uniquement pour les clients qui ont créé leur espace de travail Log Analytics avant le 21 septembre 2016.

### <a name="community-provided-management-solutions"></a>Solutions de gestion fournies par la communauté

Les solutions fournies par la communauté sont disponibles à partir de la [galerie de modèles Azure](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS)) et directement auprès des auteurs.

| Solution de gestion                 | Offer                                                                     | Niveaux de tarification                         | Remarques |
| ---                                 | ---                                                                       | ---                                   | ---   |
| Toutes les solutions fournies par la communauté  | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>     | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)     |    Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |




## <a name="data-collection-details"></a>Détails sur la collecte de données
Les tableaux suivants présentent les méthodes de collecte des données et d’autres informations sur le mode de collecte pour les solutions de gestion Log Analytics et les sources de données. Les tableaux sont classés en fonction des offres de solutions qui sont équivalentes aux [niveaux tarifaires des abonnements](https://go.microsoft.com/fwlink/?linkid=827926). La solution Log Analytics des activités est disponible pour tous les niveaux tarifaires gratuits.

L’agent Windows Log Analytics et l’agent System Center Operations Manager sont pratiquement identiques. L’agent Windows inclut des fonctionnalités supplémentaires lui permettant de se connecter à l’espace de travail OMS et d’acheminer via un proxy. Si vous utilisez un agent Operations Manager, celui-ci doit être ciblé en tant qu’agent OMS pour communiquer avec OMS. Les agents Operations Manager dans ce tableau sont des agents OMS connectés à Operations Manager. Pour plus d’informations sur la connexion de votre environnement Operations Manager existant à OMS, voir [Connexion d’Operations Manager à Log Analytics](log-analytics-om-agents.md).

> [!NOTE]
> Le type d’agent que vous utilisez détermine la façon dont les données sont envoyées à OMS, avec les conditions suivantes :
> - Vous utilisez l’agent Windows ou un agent OMS lié à Operations Manager.
> - Lorsque Operations Manager est requis, les données de l’agent Operations Manager pour la solution sont toujours envoyées à OMS à l’aide du groupe d’administration Operations Manager. De plus, lorsque Operations Manager est requis, seul l’agent Operations Manager est utilisé par la solution.
> - Lorsque Operations Manager n’est pas requis et que le tableau indique que les données de l’agent Operations Manager sont envoyées à OMS à l’aide du groupe d’administration, les données de l’agent Operations Manager sont toujours envoyées à OMS à l’aide de groupes d’administration. Les agents Windows contournent le groupe d’administration et envoient leurs données directement à OMS.
> - Lorsque les données de l’agent Operations Manager ne sont pas envoyées à l’aide d’un groupe d’administration, elles sont envoyées directement à OMS, en contournant le groupe d’administration.

### <a name="insight--analytics--log-analytics"></a>Insight & Analytics / Log Analytics

| Solution de gestion | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Log Analytics des activités | Microsoft Azure | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | sur notification |
| Évaluation d'AD |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 jours |
| État de la réplication AD |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |5 jours |
| Intégrité de l’agent | Windows et Linux | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | 1 minute |
| Gestion des alertes (Nagios) |Linux |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |à l'arrivée |
| Gestion des alertes (Zabbix) |Linux |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |1 minute |
| Gestion des alertes (Operations Manager) |Windows |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |3 minutes |
| Connecteur Application Insights (version préliminaire) | Microsoft Azure | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | sur notification |
| Azure Application Gateway Analytics | Microsoft Azure | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 minutes |
| Azure Network Security Group Analytics | Microsoft Azure | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 minutes |
| Azure SQL Analytics (version préliminaire) |Windows |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 minutes |
| Gestion de la capacité |Windows |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |Toutes les heures |
| Conteneurs | Windows et Linux | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 minutes |
| Analytique Key Vault |Windows |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 minutes |
| Analyseur de performances réseau | Windows | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | Établissements de liaisons TCP toutes les 5 secondes, données envoyées toutes les 3 minutes |
| Office 365 Analytics (version préliminaire) |Windows |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |sur notification |
| Service Fabric Analytics |Windows |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 minutes |
| Carte de service | Windows et Linux | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | 15 secondes |
| Évaluation de SQL |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 jours |
| SurfaceHub |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |à l'arrivée |
| System Center Operations Manager Assessment (version préliminaire) | Windows | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | sept jours |
| Upgrade Analytics (version préliminaire) | Windows | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | 2 jours |
| Surveillance VMware (version préliminaire) | Linux | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 minutes |
| Données de communication |Windows (2012 R2 / 8.1 ou version ultérieure) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | 1 minute |


### <a name="automation--control"></a>Automation & Control

| Solution de gestion | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Automation Hybrid Worker | Windows | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | n/a |
| Suivi des modifications |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |Toutes les heures |
| Suivi des modifications |Linux |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |Toutes les heures |
| Gestion des mises à jour | Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |au moins 2 fois par jour et 15 minutes après l'installation d'une mise à jour |

### <a name="security--compliance"></a>Sécurité et conformité

| Solution de gestion | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Analyse anti-programme malveillant |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |Toutes les heures |
| Sécurité et audit<sup>1</sup> | Windows et Linux | ![Certains](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![Certains](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![Certains](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Certains](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | divers |

<sup>1</sup>La solution Sécurité et audit peut collecter les journaux des agents Windows, Operations Manager et Linux. Consultez [Sources de données](#data-sources) pour obtenir des informations sur la collecte de données concernant :

- syslog
- Journaux des événements de sécurité Windows
- Journaux du pare-feu Windows
- Journaux d’événements Windows



### <a name="protection--recovery"></a>Protection et récupération

| Solution de gestion | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Sauvegarde | Microsoft Azure | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | n/a |
| Azure Site Recovery | Microsoft Azure | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) | n/a |


### <a name="data-sources"></a>Sources de données


| Source de données | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ETW |Windows |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 minutes |
| Journaux IIS |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 minutes |
| Passerelles d’application réseau |Windows |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 minutes |
| Groupes de sécurité réseau |Windows |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 minutes |
| Compteurs de performance |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |comme prévu, minimum de 10 secondes |
| Compteurs de performance |Linux |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |comme prévu, minimum de 10 secondes |
| syslog |Linux |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |depuis Azure Storage : 10 minutes ; à partir de l’agent : à l’arrivée |
| Journaux des événements de sécurité Windows |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |pour Azure Storage : 10 minutes ; pour l'agent : à l'arrivée |
| Journaux du pare-feu Windows |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |à l'arrivée |
| Journaux d’événements Windows |Windows |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Non](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png) |pour Azure Storage : 1 minutes ; pour l'agent : à l'arrivée |



## <a name="preview-management-solutions-and-features"></a>Solutions de gestion et fonctionnalités en version préliminaire
En exécutant un service et en suivant des pratiques de développement, nous pouvons collaborer avec des clients pour développer des solutions et des fonctionnalités.

Pendant la phase de la version préliminaire privée, nous accordons à un petit groupe de clients un accès à une implémentation anticipée de la fonctionnalité ou de la solution pour obtenir des commentaires et apporter des améliorations. Cette implémentation anticipée a des fonctionnalités et des capacités opérationnelles minimales.

Notre objectif est d’effectuer des expérimentations rapides pour trouver ce qui fonctionne et ce qui ne fonctionne pas. Nous ne quittons ce processus que quand, au vu des commentaires des clients utilisant la version préliminaire privée, nous savons que nous pouvons passer à une version préliminaire publique.

Pendant la phase de la version préliminaire publique, nous mettons la fonctionnalité ou la solution à la disposition de tous les utilisateurs pour obtenir d’autres commentaires et valider nos mise à l’échelle et efficacité. Pendant cette phase :

* Les fonctionnalités en version préliminaire apparaissent sous l’onglet Paramètres et peuvent être activées par tout utilisateur.
* Les solutions en version préliminaire sont ajoutées par le biais de la galerie ou à l’aide d’un script.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>Que dois-je savoir sur les fonctionnalités et solutions en version préliminaire ?
Nous sommes motivés par les nouvelles fonctionnalités et solutions de gestion, et à l’idée de travailler avec vous pour les développer.

Les solutions et fonctionnalités en version préliminaire ne sont pas adaptées à chacun. Avant de demander à participer à une version préliminaire privée ou d’activer une version préliminaire publique, soyez certain d’être prêt à utiliser quelque chose qui est en cours de développement.

Quand vous activez une fonctionnalité en version préliminaire par le biais du portail, un avertissement apparaît, vous rappelant que la fonctionnalité est disponible en version préliminaire.

#### <a name="for-both-private-and-public-preview"></a>Pour les versions préliminaires *privées* et *publiques*
Les points suivants s’appliquent aux versions préliminaires publiques et privées :

* Les choses ne marchent pas toujours correctement.
  * Les problèmes vont d’une gêne mineure à quelque chose qui ne fonctionne pas du tout.
* La version préliminaire peut avoir un impact négatif sur vos systèmes et votre environnement.
  * Malgré tous nos efforts, des événements inattendus peuvent parfois affecter les systèmes que vous utilisez avec OMS.
* Une perte ou altération des données peut se produire.
* Nous pouvons vous demander de collecter des journaux de diagnostic ou d’autres données pour nous aider à résoudre les problèmes.
* La fonctionnalité ou la solution peut être supprimée (temporairement ou définitivement).
  * Les connaissances acquises pendant la phase de la version préliminaire peuvent nous amener à ne pas publier la fonctionnalité ou la solution.
* Les versions préliminaires peuvent ne pas fonctionner ou ne pas avoir été testées avec toutes les configurations, et nous pouvons limiter les éléments suivants :
  * Les systèmes d’exploitation utilisables (par exemple, une fonctionnalité peut s’appliquer uniquement à Linux en version préliminaire).
  * Le type d’agent (MMA, Operations Manager) utilisable (par exemple, une fonctionnalité peut ne pas fonctionner avec Operations Manager en version préliminaire).  
* Les solutions et fonctionnalités en version préliminaire ne sont pas couvertes par le Contrat de niveau de service.
* L’utilisation des fonctionnalités en version préliminaire occasionne des frais d’utilisation.
* Des fonctionnalités ou fonctions nécessaires pour la fonctionnalité ou la solution peuvent faire défaut ou être incomplètes.
* Les fonctionnalités et solutions peuvent ne pas être disponibles dans toutes les régions.
* Les fonctionnalités et solutions peuvent ne pas être localisées.
* Les fonctionnalités et solutions peuvent n’être utilisables que par un nombre limité de clients ou d’appareils.
* Vous pouvez être amené à utiliser des scripts pour effectuer une configuration et pour activer la solution ou la fonctionnalité.
* L’interface utilisateur (IU) est inachevée et peut changer d’un jour à l’autre.
* Les versions préliminaires publiques peuvent ne pas être appropriées pour vos systèmes de production ou critiques.

#### <a name="for-private-preview"></a>Pour la version préliminaire *privée*
Outre les points ci-dessus, ce qui suit est propre aux versions préliminaires privées :

* Vous êtes supposé nous faire part de vos commentaires sur votre expérience pour que nous puissions améliorer la fonctionnalité ou la solution.
* Nous pouvons vous contacter pour recueillir vos commentaires au moyen d’enquêtes, par téléphone ou par e-mail.
* Les choses ne fonctionnent pas toujours correctement.
* Nous pouvons imposer un accord de confidentialité pour la participation ou inclure du contenu confidentiel.
  * Avant de vous exprimer dans un billet de blog ou un tweet, ou de communiquer par un autre moyen avec des tiers, contactez le directeur de programmes responsable de la version préliminaire pour connaître les restrictions qui s’appliquent à la divulgation d’informations.
* N’exécutez pas la fonctionnalité ou la solution sur des systèmes de production ou critiques.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Comment puis-je accéder aux fonctionnalités et solutions privées en version préliminaire ?
La nature de la version préliminaire privée détermine la forme de l’invitation.

* En répondant à l’enquête client mensuelle et en nous autorisant à vous suivre, vous augmentez vos chances d’être invité à une version préliminaire privée.
* Votre équipe des comptes Microsoft peut vous désigner.
* Vous pouvez vous inscrire en fonction des détails publiés sur twitter [msopsmgmt](https://twitter.com/msopsmgmt).
* Vous pouvez vous inscrire en fonction des détails partagés à l’occasion d’événements communautaires : recherchez-nous dans les rencontres, conférences et communautés en ligne.

## <a name="next-steps"></a>Étapes suivantes
* [Recherchez dans les journaux](log-analytics-log-searches.md) les informations détaillées collectées par les solutions de gestion.

