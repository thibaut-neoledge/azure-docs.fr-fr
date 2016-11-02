<properties
   pageTitle="Solution de gestion des alertes dans Operations Management Suite (OMS) | Microsoft Azure"
   description="La solution de gestion des alertes dans Log Analytics vous permet d’analyser toutes les alertes qui se produisent dans votre environnement.  Outre la centralisation des alertes générées dans OMS, elle importe les alertes de groupes d’administration System Center Operations Manager (SCOM) connectés dans Log Analytics."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2016"
   ms.author="bwren" />


# <a name="alert-management-solution-in-operations-management-suite-(oms)"></a>Solution de gestion des alertes dans Operations Management Suite (OMS)

![Icône de gestion des alertes](media/log-analytics-solution-alert-management/icon.png) La solution de gestion des alertes vous permet d’analyser toutes les alertes qui se produisent dans votre environnement.  Outre la centralisation des alertes générées dans OMS, elle importe les alertes de groupes d’administration System Center Operations Manager (SCOM) connectés dans Log Analytics.  Dans les environnements comprenant plusieurs groupes d’administration, la solution de gestion des alertes offre une vue centralisée des alertes dans tous les groupes d’administration.

## <a name="prerequisites"></a>Composants requis

- Pour importer des alertes de SCOM, cette solution nécessite une connexion entre votre espace de travail OMS et un groupe d’administration SCOM. Pour établir cette connexion, suivez la procédure décrite dans [Connecter Operations Manager à Log Analytics](log-analytics-om-agents.md).  


## <a name="configuration"></a>Configuration

Ajoutez la solution de gestion des alertes à votre espace de travail OMS en suivant la procédure décrite dans [Ajouter des solutions](log-analytics-add-solutions.md).  Aucune configuration supplémentaire n’est requise.

## <a name="management-packs"></a>Packs d’administration

Si votre groupe d’administration SCOM est connecté à votre espace de travail OMS, les packs d’administration suivants sont installés dans SCOM quand vous ajoutez cette solution.  Ces packs d’administration ne nécessitent aucune opération de configuration ou de maintenance.  

- Microsoft System Center Advisor Alert Management (Microsoft.IntelligencePacks.AlertManagement)

Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager à Log Analytics](log-analytics-om-agents.md).

## <a name="data-collection"></a>Collecte des données

### <a name="agents"></a>Agents

Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution.

| Source connectée | Support | Description |
|:--|:--|:--|
| [Agents Windows](log-analytics-windows-agents.md) | Non | Les agents Windows directs ne génèrent pas d’alertes SCOM. |
| [Agents Linux](log-analytics-linux-agents.md) | Non | Les agents Linux directs ne génèrent pas d’alertes SCOM. |
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | Oui | Les alertes générées sur des agents SCOM sont remises au groupe d’administration, puis transférées à Log Analytics.<br><br>Une connexion directe entre l’agent SCOM et Log Analytics n’est pas obligatoire. Les données des alertes sont transférées du groupe d’administration au référentiel OMS. |
| [Compte Azure Storage](log-analytics-azure-storage.md) | Non | Les alertes SCOM ne sont pas stockées dans les comptes Azure Storage. |

### <a name="collection-frequency"></a>Fréquence de collecte

Les alertes générées dans OMS sont immédiatement accessibles à la solution.  Les données des alertes sont envoyées du groupe d’administration SCOM à Log Analytics toutes les 3 minutes.  

## <a name="using-the-solution"></a>Utilisation de la solution

Quand vous ajoutez la solution de gestion des alertes à votre espace de travail OMS, la vignette **Gestion des alertes** est ajoutée à votre tableau de bord OMS.  Cette vignette affiche le nombre d’alertes actuellement actives qui ont été générées au cours des dernières 24 heures, ainsi qu’une représentation graphique.  Vous ne pouvez pas modifier cet intervalle de temps.

![Vignette de gestion des alertes](media/log-analytics-solution-alert-management/tile.png)

Cliquez sur la vignette **Gestion des alertes** pour ouvrir le tableau de bord **Gestion des alertes**.  Le tableau de bord comprend les colonnes figurant dans le tableau suivant.  Chaque colonne répertorie les dix premières alertes (classées par nombre d’alertes) correspondant aux critères de cette colonne pour l’étendue et l’intervalle de temps spécifiés.  Vous pouvez exécuter une recherche dans les journaux qui fournit la liste complète. Pour cela, cliquez sur **Afficher tout** en bas de la colonne ou cliquez sur l’en-tête de colonne.

| Colonne| Description |
|:--|:--|
| Alertes critiques | Toutes les alertes ayant le niveau de gravité Critique, regroupées selon le nom de l’alerte.  Cliquez sur un nom d’alerte pour exécuter une recherche dans les journaux retournant tous les enregistrements pour cette alerte. |
| Alertes d'avertissement | Toutes les alertes ayant le niveau de gravité Avertissement, regroupées selon le nom de l’alerte.  Cliquez sur un nom d’alerte pour exécuter une recherche dans les journaux retournant tous les enregistrements pour cette alerte. |
| Alertes SCOM actives | Toutes les alertes SCOM n’ayant pas l’état *Fermé* , regroupées selon la source ayant généré l’alerte. |
| Toutes les alertes actives | Toutes les alertes, quel que soit leur niveau de gravité, regroupées selon le nom de l’alerte. Inclut uniquement les alertes SCOM n’ayant pas l’état *Fermé*.|

Si vous faites défiler l’écran vers la droite, le tableau de bord répertorie plusieurs requêtes courantes sur lesquelles vous pouvez cliquer pour effectuer une [recherche dans les journaux](log-analytics-log-searches.md) afin d’obtenir les données des alertes.

![Tableau de bord de gestion des alertes](media/log-analytics-solution-alert-management/dashboard.png)

## <a name="scope-and-time-range"></a>Étendue et intervalle de temps

Par défaut, l’étendue des alertes analysées dans la solution de gestion des alertes couvre tous les groupes d’administration connectés qui ont été générés au cours des 7 derniers jours.  

![Étendue de la gestion des alertes](media/log-analytics-solution-alert-management/scope.png)

- Pour modifier les groupes d’administration inclus dans l’analyse, cliquez sur **Étendue** en haut du tableau de bord.  Sélectionnez **Global** pour tous les groupes d’administration connectés ou **Par groupe d’administration** pour choisir un seul groupe d’administration.

- Pour modifier l’intervalle de temps des alertes, sélectionnez **Données basées sur** en haut du tableau de bord.  Vous pouvez sélectionner les alertes générées durant les 7 derniers jours, la journée précédente ou les 6 dernières heures.  Vous pouvez également sélectionner **Personnalisé** et spécifier une plage de dates personnalisée.

## <a name="log-analytics-records"></a>Enregistrements Log Analytics

La solution de gestion des alertes analyse tous les enregistrements de type **Alerte**.  Elle importe également les alertes de SCOM et crée pour chacune d’entre elles un enregistrement correspondant avec comme propriétés Type et SourceSystem les valeurs **Alert** et **OpsManager**, respectivement.  Les propriétés de ces enregistrements sont décrites dans le tableau suivant.  

| Propriété | Description |
|:--|:--|
| Type | *Alert* |
| SourceSystem | *OpsManager* |
| AlertContext | Détails de l’élément de données à l’origine de l’alerte générée au format XML. |
| AlertDescription | Description détaillée de l’alerte. |
| AlertId | GUID de l’alerte. |
| AlertName | Nom de l’alerte. |
| AlertPriority | Niveau de priorité de l’alerte. |
| AlertSeverity | Niveau de gravité de l’alerte. |
| AlertState | Dernier état de résolution de l’alerte. |
| LastModifiedBy | Nom de l’utilisateur ayant apporté la dernière modification à l’alerte. |
| ManagementGroupName | Nom du groupe d’administration dans lequel l’alerte a été générée. |
| RepeatCount | Nombre de fois que l’alerte a été générée pour le même objet analysé depuis sa résolution. |
| ResolvedBy | Nom de l’utilisateur ayant résolu l’alerte. Vide si l’alerte n’a pas encore été résolue. |
| SourceDisplayName | Nom d’affichage de l’objet d’analyse ayant généré l’alerte. |
| SourceFullName | Nom complet de l’objet d’analyse ayant généré l’alerte. |
| TicketId | ID de ticket de l’alerte si l’environnement SCOM est intégré à un processus d’affectation de tickets pour les alertes.  Vide si aucun ID ticket n’est affecté. |
| TimeGenerated | Date et heure de la création de l’alerte. |
| TimeLastModified | Date et heure de la dernière modification de l’alerte. |
| TimeRaised | Date et heure de la génération de l’alerte. |
| TimeResolved | Date et heure de la résolution de l’alerte. Vide si l’alerte n’a pas encore été résolue. |

## <a name="sample-log-searches"></a>Exemples de recherches dans les journaux

Le tableau suivant fournit des exemples de recherches dans les journaux pour les enregistrements d’alerte collectés par cette solution.  

| Requête | Description |
|:--|:--|
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR | Alertes critiques déclenchées au cours des dernières 24 heures |
| Type=Alert AlertSeverity=warning TimeRaised>NOW-24HOUR | Alertes d’avertissement déclenchées au cours des dernières 24 heures  |
| Type=Alert SourceSystem=OpsManager AlertState!=Closed TimeRaised>NOW-24HOUR &#124; measure count() as Count by SourceDisplayName | Sources avec des alertes actives déclenchées au cours des dernières 24 heures |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR AlertState!=Closed | Alertes critiques déclenchées au cours des dernières 24 heures et toujours actives |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-24HOUR AlertState=Closed | Alertes déclenchées au cours des dernières 24 heures et désormais fermées |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; measure count() as Count by AlertSeverity | Alertes déclenchées au cours de la journée précédente et regroupées selon leur niveau de gravité |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; sort RepeatCount desc | Alertes déclenchées au cours de la journée précédente et triées selon leur valeur de répétition |

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Alertes dans Log Analytics](log-analytics-alerts.md) pour obtenir des informations sur la génération d’alertes à partir de Log Analytics.



<!--HONumber=Oct16_HO2-->


