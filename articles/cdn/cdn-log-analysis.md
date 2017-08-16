---
title: Analyse des journaux pour Azure CDN | Microsoft Docs
description: "Le client peut activer l’analyse des journaux pour Azure CDN."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 03ff74ae4e40d3f2279caaf4f73e9b4aac6a2ebb
ms.contentlocale: fr-fr
ms.lasthandoff: 08/08/2017

---

# <a name="diagnostics-logs-for-azure-cdn"></a>Journaux de diagnostic pour Azure CDN

Après avoir activé le CDN pour votre application, vous souhaiterez probablement surveiller l’utilisation du CDN, vérifier l’intégrité de votre application et corriger les éventuels problèmes. Azure CDN fournit ces fonctionnalités avec [l’Analytique principale CDN](cdn-analyze-usage-patterns.md) et les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="cdn-core-analytics"></a>Analytique principale CDN
En tant qu’utilisateur d’Azure CDN actuel avec un profil Verizon standard ou premium, vous pouvez déjà afficher les analyses de base dans le portail supplémentaire accessible via l’option « Gérer » à partir du portail Azure. 

## <a name="azure-diagnostic-logs"></a>Journaux de diagnostic Azure

Avec cette nouvelle fonctionnalité, vous pouvez maintenant afficher l’analytique principale et l’enregistrer dans une ou plusieurs destinations, dont les suivantes :

 - Compte de Stockage Azure
 - Hubs d'événements Azure
 - [Référentiel OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
 Cette fonctionnalité est disponible pour tous les points de terminaison CDN appartenant à des profils CDN Verizon (standard et premium) et Akamai (Standard).

Les journaux de diagnostic vous permettent d’exporter des métriques d’utilisation de base de votre point de terminaison CDN vers diverses sources afin de pouvoir les utiliser de façon personnalisée. Vous pouvez par exemple exécuter les types suivants d’exportation de données :

- Exporter les données vers un stockage d’objets blob, exporter au format CSV et générer des graphiques dans Excel.
- Exporter des données vers des concentrateurs d’événements et mettre les données en corrélation avec d’autres services Azure.
- Exporter des données vers Log Analytics et afficher les données dans votre propre espace de travail OMS

L’illustration suivante montre une vue des données au moyen de l’analytique principale CDN classique.

![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figure 1 : vue avec l’analytique principale CDN*

La procédure suivante passe par le schéma des données d’analytique principale, les étapes d’activation de la fonctionnalité et la livraison à différentes destinations, et la consommation à partir de ces destinations.

## <a name="enable-logging-with-azure-portal"></a>Activation de la journalisation avec le portail Azure

> [!NOTE]
> Les journaux de diagnostic sont **désactivés** par défaut. 

Suivez les étapes ci-dessous pour activer la journalisation avec l’analytique principale CDN :

Connectez-vous au [portail Azure](http://portal.azure.com). Si vous n’avez pas activé CDN pour votre flux de travail, [activez Azure CDN](cdn-create-new-endpoint.md) avant de continuer.

1. Dans le portail, accédez à **Profil CDN**.
2. Sélectionnez un profil CDN, puis sélectionnez le point de terminaison CDN pour lequel vous souhaitez activer les **journaux de diagnostic**.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Accédez au panneau **Journaux de diagnostic** sous la section **Surveillance**, puis modifiez l’état sur **Activé**.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Activation de la journalisation avec Stockage Azure
    
Pour utiliser Stockage Azure afin de stocker les journaux, sélectionnez **Archiver dans un compte de stockage**, sélectionnez la durée de conservation en jours, puis cliquez sur **Analytique principale** sous **Journal**.

![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*Figure 2 : activation de la journalisation avec Stockage Azure*

### <a name="logging-with-oms-log-analytics"></a>Journalisation avec OMS Log Analytics

Pour stocker les journaux à l’aide d’OMS Log Analytics, effectuez les étapes suivantes :

1. Dans le panneau **Journaux de diagnostic** sous **Surveillance**, sélectionnez **Envoyer à Log Analytics**. 

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Configurez la journalisation Log Analytics en cliquant sur Configurer. Une boîte de dialogue s’ouvre, où vous pouvez sélectionner un espace de travail antérieur ou en créer un.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Cliquez sur **Créer un espace de travail**.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/07_Create-new.png)

4. Ensuite, vous devez sélectionner un nouveau nom d’espace de travail, un abonnement existant, un groupe de ressources (nouveau ou existant), un emplacement et un niveau tarifaire. Vous pouvez épingler cette configuration à votre tableau de bord. Cliquez sur OK pour achever la configuration.

    Votre espace de travail doit ensuite apparaître, avec vos noms de groupe de ressources et d’espace de travail OMS. Les noms doivent être uniques et ne peuvent contenir que des lettres, des chiffres et des traits d’union. Les espaces et les traits de soulignement ne sont pas autorisés. 

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5. Ensuite, vous obtenez un bref message indiquant que votre espace de travail a été créé, puis l’écran de configuration de la journalisation réapparaît. Vous pouvez vérifier le nom de votre espace de travail Log Analytics.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    Après avoir défini la configuration Log Analytics, pensez à cocher la case Analytique principale pour la journalisation CDN.

6. Si tout vous convient, cliquez sur le bouton **Enregistrer** en haut de la boîte de dialogue de configuration.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/10_Save-me.png)

    Le bouton **Enregistrer** n’est plus actif, et le bouton d’activation/de désactivation est désormais en position Activé, et apparaît en bleu, et non plus en violet.

7. Si vous souhaitez voir votre nouvel espace de travail OMS, accédez à votre tableau de bord dans le portail Azure et cliquez sur le nom de votre espace de travail Log Analytics. Votre espace de travail apparaît alors (vérifiez qu’Espace de travail OMS est mis en surbrillance sur la gauche). Cliquez sur la vignette Portail OMS pour voir votre espace de travail dans le référentiel OMS. 

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Votre référentiel OMS est maintenant prêt à enregistrer des données. Pour utiliser ces données, vous devez recourir à une [solution OMS](#consuming-oms-log-analytics-data), comme indiqué plus loin dans cet article.

Vous trouverez plus d’informations sur les retards des données de journal [ici](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Activation de la journalisation avec PowerShell

Voici un exemple d’activation et d’obtention des journaux de diagnostic via les applets de commande Azure PowerShell.

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>Activation des journaux de diagnostic dans un compte de stockage

Commencez par vous connecter et sélectionner un abonnement :

    Login-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Pour activer les journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Pour activer les journaux de diagnostic dans un espace de travail OMS, utilisez cette commande :

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Utilisation des journaux de diagnostic à partir de Stockage Azure
Cette section décrit le schéma de l’analytique principale CDN et leur organisation à l’intérieur d’un compte de stockage Azure, et fournit un exemple de code pour télécharger les journaux dans un fichier CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Utilisation de l’explorateur de stockage Microsoft Azure
Pour pouvoir accéder aux données d’analyse de base à partir du compte de stockage Azure, vous avez d’abord besoin d’un outil pour accéder au contenu dans un compte de stockage. Si plusieurs outils sont disponibles sur le marché, celui que nous recommandons est l’Explorateur de stockage Microsoft Azure. Vous pouvez télécharger l’outil [ici](http://storageexplorer.com/). Après le téléchargement et l’installation du logiciel, configurez-le pour utiliser le même compte de stockage Azure que celui qui a été configuré en tant que destination pour les journaux de diagnostic CDN.

1.  Ouvrez **l’explorateur de stockage Microsoft Azure**
2.  Localiser le compte de stockage
3.  Accédez au nœud **« Conteneurs d’objets Blob »** sous ce compte de stockage, puis développez le nœud
4.  Sélectionnez le conteneur nommé **« journaux-insights-coreanalytics »** et double-cliquez dessus.
5.  Les résultats s’affichent dans le volet droit, en commençant par le premier niveau, qui ressemble à **« resourceId= »**. Continuez à cliquer jusqu'à ce que vous voyiez le fichier **PT1H.json**. Consultez la remarque ci-dessous pour une explication du chemin d’accès.
6.  Chaque objet blob **PT1H.json** représente les journaux d’analyse pendant une heure pour un point de terminaison CDN spécifique ou son domaine personnalisé.
7.  Le schéma du contenu de ce fichier JSON est décrit dans la section Schéma des journaux Core Analytics


> [!NOTE]
> **Format du chemin d’accès des objets blob**
> 
> Les journaux Core Analytics sont générés toutes les heures. Toutes les données pendant une heure sont collectées et stockées à l’intérieur d’un objet blob Azure en tant que charge utile JSON. Le chemin d’accès à cet objet blob Azure apparaît comme s’il existait une structure hiérarchique. En effet, l’outil Explorateur de stockage interprète '/' comme un séparateur de répertoire, et affiche la hiérarchie pour des raisons pratiques. En fait, le chemin d’accès complet représente simplement le nom de l’objet blob. Ce nom d’objet blob suit la convention d’affectation de noms suivante   
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Description des champs :**

|value|description|
|-------|---------|
|Identifiant d’abonnement    |ID de l’abonnement Azure. Cette valeur est au format Guid.|
|Ressource |Nom de groupe   Nom du groupe de ressources auquel les ressources CDN appartiennent.|
|Nom de profil |Nom du profil CDN|
|Nom du point de terminaison |Nom du point de terminaison CDN|
|Year|  Représentation à 4 chiffres de l’année, par exemple, 2017|
|Mois| Représentation à 2 chiffres du mois de l’année. 01 = Janvier ... 12 = Décembre|
|jour|   Représentation à 2 chiffres du jour du mois|
|PT1H.json| Fichier JSON où sont effectivement stockées les données d’analyse|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportation des données de Core Analytics vers un fichier CSV

Pour faciliter l’accès à Core Analytics, nous fournissons un exemple de code pour un outil qui permet de télécharger les fichiers JSON dans un format de fichier plat avec séparation par virgules, qui peut être utilisé pour créer facilement des graphiques ou autres agrégations.

Voici comment vous pouvez utiliser l’outil :

1.  Consultez le lien GitHub : [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  Téléchargement du code
3.  Suivez les instructions pour compiler et configurer
4.  Exécuter l’outil
5.  Le fichier CSV résultant présente les données d’analyse dans une hiérarchie plate simple.

## <a name="consuming-diagnostics-logs-from-an-oms-log-analytics-repository"></a>Utilisation des journaux de diagnostics à partir d’un référentiel OMS Log Analytics
Log Analytics est un service d’Operations Management Suite (OMS) qui surveille vos environnements cloud et locaux et assure leur disponibilité et leurs performances. Il collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils d’analyse pour fournir une analyse sur plusieurs sources. 

Pour utiliser Log Analytics, vous devez [activer la journalisation](#enable-logging-with-azure-storage) dans le référentiel OMS Log Analytics (voir plus haut dans cet article).

### <a name="using-the-oms-repository"></a>Utilisation du référentiel OMS

 Le diagramme suivant illustre l’architecture des entrées et sorties du référentiel :

![Référentiel OMS Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figure 3 : référentiel OMS Log Analytics*

Vous pouvez afficher les données de plusieurs façons à l’aide des solutions de gestion. Vous pouvez obtenir des solutions de gestion à partir de la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Vous pouvez installer des solutions de gestion à partir de la Place de marché Azure en cliquant sur le lien **Obtenir maintenant** au bas de chaque solution.

### <a name="adding-an-oms-cdn-management-solution"></a>Ajout d’une solution de gestion CDN d’OMS

Suivez ces étapes pour ajouter une solution de gestion :

1.   Si ce n’est pas déjà fait, connectez-vous au portail Azure à l’aide de votre abonnement Azure et accédez à votre tableau de bord.
    ![Tableau de bord Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Dans le panneau **Nouveau** sous **Marketplace**, sélectionnez **Surveillance + gestion**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Dans le panneau **Surveillance + gestion**, cliquez sur **Afficher tout**.

    ![Afficher tout](./media/cdn-diagnostics-log/15_See-all.png)

4.  Recherchez CDN dans la zone de recherche.

    ![Afficher tout](./media/cdn-diagnostics-log/16_Search-for.png)

5.  Sélectionnez **Analytique principale Azure CDN**. 

    ![Afficher tout](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  Après avoir cliqué sur **Créer**, vous êtes invité à créer un espace de travail OMS ou à en utiliser un existant. 

    ![Afficher tout](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  Sélectionnez l’espace de travail que vous avez créé. Vous devez ensuite ajouter un compte Automation.

    ![Afficher tout](./media/cdn-diagnostics-log/19_Add-automation.png)

8. L’écran suivant montre le formulaire de compte Automation que vous devez remplir. 

    ![Afficher tout](./media/cdn-diagnostics-log/20_Automation.png)

9. Après avoir créé le compte Automation, vous êtes prêt à ajouter votre solution. Cliquez sur le bouton **Créer** .

    ![Afficher tout](./media/cdn-diagnostics-log/21_Ready.png)

10. Votre solution a maintenant été ajoutée à votre espace de travail. Revenez à votre tableau de bord dans le portail Azure.

    ![Afficher tout](./media/cdn-diagnostics-log/22_Dashboard.png)

    Cliquez sur l’espace de travail Log Analytics que vous avez créé pour accéder à votre espace de travail. 

11. Cliquez sur la vignette **Portail OMS** pour afficher votre nouvelle solution dans le portail OMS.

    ![Afficher tout](./media/cdn-diagnostics-log/23_workspace.png)

12. Votre portail OMS doit maintenant ressembler à la capture d’écran suivante :

    ![Afficher tout](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Cliquez sur une des vignettes pour afficher plusieurs vues de vos données.

    ![Afficher tout](./media/cdn-diagnostics-log/25_Interior-view.png)

    Vous pouvez faire défiler l’écran vers la gauche ou la droite pour voir d’autres vignettes représentant des vues des données. 

    Vous pouvez cliquer sur une des vignettes pour obtenir des détails supplémentaires sur vos données.

     ![Afficher tout](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Offres et niveaux tarifaires

Vous pouvez voir des offres et des niveaux tarifaires pour les solutions de gestion OMS [ici](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Personnalisation des vues

Vous pouvez personnaliser la vue de vos données à l’aide du **Concepteur de vues**. Accédez à votre espace de travail OMS, puis cliquez sur la vignette **Concepteur de vues** pour commencer la conception.

![Concepteur de vues](./media/cdn-diagnostics-log/27_Designer.png)

Vous pouvez faire glisser et déposer des types de graphiques à partir de la gauche et renseigner le détail des données que vous souhaitez analyser à gauche.

![Concepteur de vues](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Retards des données de journal

Retards des données de journal Verizon | Retards des données de journal Akamai
--- | ---
Les données de journal Verizon sont retardées de 1 heure et ont besoin de jusqu'à 2 heures pour commencer à apparaître après l’achèvement de la propagation du point de terminaison. | Les données de journal Akamai sont retardées de 24 heures et ont besoin de jusqu'à 2 heures pour commencer à apparaître si la création remonte à plus de 24 heures. Si elle a été effectuée récemment, il peut falloir jusqu’à 25 heures pour que les journaux commencent à apparaître.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Types de journaux de diagnostic pour l’analytique principale CDN

Nous proposons uniquement les journaux de l’analytique principale, qui contiennent les métriques affichant les statistiques de réponse HTTP et de sortie, comme dans les points de présence/périmètres CDN.

### <a name="core-analytics-metrics-details"></a>Détails des métriques de Core Analytics
Le tableau suivant répertorie les métriques disponibles dans les journaux de Core Analytics. Toutes les métriques ne sont pas disponibles auprès tous les fournisseurs, même si ces différences sont minimes. Le tableau suivant indique également si une métrique donnée est disponible à partir d’un fournisseur particulier. Veuillez noter que les métriques sont disponibles uniquement pour les points de terminaison CDN recevant du trafic.


|Mesure                     | Description   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Nombre total d’occurrences de requêtes pendant cette période| Oui  |Oui   |
| RequestCountHttpStatus2xx |Nombre de toutes les requêtes qui ont abouti à un code 2xx (par ex. 200, 202)              | Oui  |Oui   |
| RequestCountHttpStatus3xx | Nombre de toutes les requêtes qui ont abouti à un code 3xx (par ex. 300, 302)              | Oui  |Oui   |
| RequestCountHttpStatus4xx |Nombre de toutes les requêtes qui ont abouti à un code 4xx (par ex. 400, 404)               | Oui   |Oui   |
| RequestCountHttpStatus5xx | Nombre de toutes les requêtes qui ont abouti à un code 5xx (par ex. 500, 504)              | Oui  |Oui   |
| RequestCountHttpStatusOthers |  Nombre d’occurrences de tous les autres codes HTTP (en dehors de 2xx-5xx) | Oui  |Oui   |
| RequestCountHttpStatus200 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 200              |Non   |Oui   |
| RequestCountHttpStatus206 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 206              |Non   |Oui   |
| RequestCountHttpStatus302 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 302              |Non   |Oui   |
| RequestCountHttpStatus304 |  Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 304             |Non   |Oui   |
| RequestCountHttpStatus404 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 404              |Non   |Oui   |
| RequestCountCacheHit |Nombre de toutes les requêtes qui ont abouti à un accès au cache. Cela signifie que la ressource a été traitée directement du point de présence vers le client.               | Oui  |Non   |
| RequestCountCacheMiss | Nombre de toutes les requêtes qui ont abouti à un échec de cache. Cela signifie que la ressource est introuvable sur le point de présence le plus proche du client et a donc été récupérée à l’origine.              |Oui   | Non  |
| RequestCountCacheNoCache | Nombre de requêtes vers une ressource empêchées d’être mises en cache en raison d’une configuration de l’utilisateur sur le périmètre.              |Oui   | Non  |
| RequestCountCacheUncacheable | Nombre de requêtes de ressources empêchées d’être mises en cache par les en-têtes Cache-Control et Expires, qui indiquent qu’elles ne doivent pas être mises en cache sur un point de présence ou par le client HTTP                |Oui   |Non   |
| RequestCountCacheOthers | Nombre de toutes les requêtes avec un état du cache non traité ci-dessus.              |Oui   | Non  |
| EgressTotal | Transfert de données sortantes en Go              |Oui   |Oui   |
| EgressHttpStatus2xx | Transfert de données sortantes * pour les réponses avec des codes d’état HTTP 2xx en Go            |Oui   |Non   |
| EgressHttpStatus3xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 3xx en Go              |Oui   |Non   |
| EgressHttpStatus4xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 4xx en Go               |Oui   | Non  |
| EgressHttpStatus5xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 5xx en Go               |Oui   |  Non |
| EgressHttpStatusOthers | Transfert de données sortantes pour les réponses avec d’autres codes d’état HTTP en Go                |Oui   |Non   |
| EgressCacheHit |  Transfert de données sortantes pour les réponses qui ont été livrées directement depuis le cache CDN sur les points de présence/périmètres CDN  |Oui   |  Non |
| EgressCacheMiss | Transfert des données sortantes pour les réponses introuvables sur le serveur le plus proche et récupérées à partir du serveur d’origine              |Oui   |  Non |
| EgressCacheNoCache | Transferts de données sortants pour les ressources empêchés d’être mis en cache en raison d’une configuration de l’utilisateur sur le périmètre.                |Oui   |Non   |
| EgressCacheUncacheable | Transferts de données sortants pour les ressources empêchés d’être mis en cache par les en-têtes Cache-Control et/ou Expires, qui indiquent qu’elles ne doivent pas être mises en cache sur un point de présence ou par le client HTTP                    |Oui   | Non  |
| EgressCacheOthers |  Transfère les données sortantes pour d’autres scénarios de cache.             |Oui   | Non  |

*Le transfert de données sortantes fait référence au trafic produit des serveurs POP CDN vers le client.


### <a name="schema-of-the-core-analytics-logs"></a>Schéma des journaux Core Analytics 

Tous les journaux sont stockés au format JSON, et chaque entrée comprend des champs de chaîne au schéma présenté ci-après :

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Où « time » représente l’heure de début des limites d’heure pour lesquelles les statistiques sont rapportées. Quand une métrique n’est pas prise en charge par un fournisseur CDN, il y a une valeur null au lieu d’un entier ou d’un double. Cette valeur null indique l’absence de métrique, et diffère de la valeur 0. Notez également qu’il y aura un ensemble de ces métriques par domaine configuré sur le point de terminaison.

Exemple de propriétés ci-dessous :

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Journaux de diagnostic Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Core Analytics via le portail supplémentaire Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure OMS Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-overview)
* [API REST Azure Log Analytics](https://docs.microsoft.com/en-us/rest/api/loganalytics)








