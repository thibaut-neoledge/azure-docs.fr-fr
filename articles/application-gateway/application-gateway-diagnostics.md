---
title: "Surveiller les journaux d’accès et des performances, l’intégrité du serveur principal, ainsi que les métriques d’Application Gateway | Microsoft Docs"
description: "Découvrez comment activer et gérer les journaux d’accès et des performances pour Application Gateway"
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager
ms.assetid: 300628b8-8e3d-40ab-b294-3ecc5e48ef98
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: amitsriva
translationtype: Human Translation
ms.sourcegitcommit: d65b354bc972c8268f1b4f072843b5bf4977a7c4
ms.openlocfilehash: 2b37bf92ce8945996eb64477c28bea845b7df516
ms.lasthandoff: 02/09/2017


---
# <a name="backend-health-diagnostics-logging-and-metrics-for-application-gateway"></a>Intégrité du serveur principal, journalisation des diagnostics et métriques pour la passerelle Application Gateway

Azure permet de surveiller les ressources grâce à la journalisation et à des métriques La passerelle Application Gateway propose ces fonctionnalités avec l’intégrité du serveur principal, la journalisation et les métriques.

[**Intégrité du serveur principal** ](#backend-health) - La passerelle Appplication gateway permet de surveiller l’intégrité des serveurs dans les pools principaux via le portail et Powershell. L’intégrité des pools principaux est également accessible via les journaux de diagnostic des performances.

[**Journalisation**](#enable-logging-with-powershell) - La journalisation permet d’enregistrer ou d’utiliser des journaux de performances, d’accès et d’autres journaux à partir d’une ressource à des fins de surveillance.

[**Mesures**](#metrics) - La passerelle Application Gateway possède actuellement une métrique. Cette métrique mesure le débit de la passerelle d’application en octets par seconde.

## <a name="backend-health"></a>Intégrité du serveur principal

La passerelle Appplication gateway permet de surveiller l’intégrité des membres individuels des pools principaux via le portail, PowerShell et l’interface de ligne de commande. Une synthèse de l’intégrité des pools principaux est également accessible via les journaux de diagnostic des performances. Le rapport d’intégrité du serveur principal reflète les résultats de la sonde d’intégrité de la passerelle Application Gateway sur les instances de serveur principal. Une fois la détection réussie et que le trafic peut être orienté vers le serveur principal, celui-ci est considéré comme intègre, dans le cas contraire, il est considéré comme défaillant sur le plan de l’intégrité.

> [!important]
> Si le sous-réseau Application Gateway comporte un groupe de sécurité réseau, les plages de ports 65503-65534 doivent être ouvertes sur les instances Application Gateway.

### <a name="view-backend-health-through-the-portal"></a>Afficher l’intégrité du serveur principal via le portail

Aucune action n’est requise pour afficher l’intégrité du serveur principal. Dans une passerelle d’application existante, accédez à **Analyse** > **Intégrité du serveur principal**. Chaque membre du pool principal est répertorié sur cette page (qu’il s’agisse d’une carte réseau, d’un IP ou d’un nom de domaine complet). Le nom du pool principal, le port, le nom et l’état d’intégrité des paramètres http principaux sont affichés. Les valeurs valides de l’état d’intégrité sont « Healthy » (Intègre), « Unhealthy » (Défaillant) et « Unknown » (Inconnu).

> [!WARNING]
> Si l’état d’intégrité du serveur principal est **Unknown** (Inconnu), assurez-vous que l’accès au serveur principal n’est pas bloqué par une règle de groupe de sécurité réseau (NSG) ou par un serveur DNS personnalisé sur le réseau virtuel.

![Intégrité du serveur principal][10]

### <a name="view-backend-health-with-powershell"></a>Afficher l’intégrité du serveur principal avec PowerShell

L’intégrité du serveur principal est également disponible pour être récupéré via PowerShell. Le code PowerShell suivant montre comment extraire l’intégrité du serveur principal avec l’applet de commande `Get-AzureRmApplicationGatewayBackendHealth`.

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

Les résultats sont renvoyés. Voici un exemple de la réponse dans l’extrait de code suivant.

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Journalisation de diagnostic

Vous pouvez utiliser différents types de journaux dans Azure pour gérer les passerelles Application Gateway et résoudre les problèmes associés. Certains de ces journaux sont accessibles par le biais du portail et tous les journaux peuvent être extraits à partir d’un stockage blob Azure et affichés dans différents outils, notamment [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel et PowerBI. Pour en savoir plus sur les différents types de journaux, consultez la liste suivante :

* **Journal d’activité :** vous pouvez utiliser le [Journal d’activité Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anciennement journaux des opérations et journaux d’audit) pour afficher toutes les opérations soumises à votre abonnement Azure, ainsi que leur état. Les entrées du journal d’activité sont recueillies par défaut et peuvent être affichées dans le Portail Azure.
* **Journaux d’accès :** vous pouvez utiliser ce journal pour afficher le modèle d’accès Application Gateway et analyser des informations importantes, notamment l’adresse IP de l’appelant, l’URL demandée, la latence de réponse, le code de retour, les octets d’entrée et de sortie. Le journal d’accès est collecté toutes les 300 secondes. Ce journal contient un enregistrement par instance de passerelle Application Gateway. L’instance de la passerelle Application Gateway peut être identifié par la propriété « instanceId ».
* **Journaux de performances :** vous pouvez utiliser ce journal pour afficher les performances des instances de la passerelle Application Gateway. Ce journal capture des informations sur les performances par instance, notamment le nombre total de demandes traitées, le débit en octets, le nombre total de demandes présentées, le nombre de demandes ayant échoué, le nombre d’instances du serveur principal correctes et incorrectes. Le journal des performances est collecté toutes les 60 secondes.
* **Journaux du pare-feu :** vous pouvez utiliser ce journal pour afficher les requêtes consignées via le mode de détection ou de prévention d’une passerelle d’application configuré avec un pare-feu d’applications web.

> [!WARNING]
> Les journaux ne sont disponibles que pour les ressources déployées avec le modèle de déploiement de Resource Manager. Vous ne pouvez pas les utiliser pour les ressources utilisant le modèle de déploiement classique. Pour mieux comprendre ces deux modèles, reportez-vous à l’article [Présentation du déploiement de Resource Manager et du déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md) .

Pour vos journaux, il existe trois options de stockage différentes.

* Compte de stockage : les comptes de stockage conviennent parfaitement aux journaux lorsqu’ils sont stockés pour une durée plus longue et consultés lorsque nécessaire.
* Concentrateurs d’événements : les concentrateurs d’événements constituent une excellente solution pour l’intégration avec d’autres outils SEIM afin de recevoir des alertes sur vos ressources
* Log analytics : Log Analytics convient parfaitement pour la surveillance en temps réel générale de votre application ou la recherche de tendances.

### <a name="enable-logging-with-powershell"></a>Activation de la journalisation avec PowerShell

La journalisation d’activité est automatiquement activée pour chaque ressource Resource Manager. Vous devez activer la journalisation de l’accès et des performances pour commencer à collecter les données disponibles dans ces journaux. Pour activer la journalisation, consultez les étapes suivantes :

1. Notez l’ID de ressource de votre compte de stockage, où les données de journalisation sont stockées. Cette valeur peut avoir le format suivant : /abonnements/\<subscriptionId\>/resourceGroups/\<nom du groupe de ressources\>/providers/Microsoft.Storage/storageAccounts/\<nom du compte de stockage\>. Vous pouvez utiliser n’importe quel compte de stockage dans votre abonnement. Vous pouvez utiliser la version préliminaire du portail pour rechercher ces informations.

    ![Portail en version préliminaire - Diagnostics Application Gateway](./media/application-gateway-diagnostics/diagnostics1.png)

2. Notez l’ID de ressource de votre passerelle Application Gateway pour laquelle la journalisation doit être activée. Cette valeur peut avoir le format suivant : /abonnements/\<subscriptionId\>/resourceGroups/\<nom du groupe de ressources\>/providers/Microsoft.Network/applicationGateways/\<nom de la passerelle Application Gateway\>. Vous pouvez utiliser la version préliminaire du portail pour rechercher ces informations.

    ![Portail en version préliminaire - Diagnostics Application Gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Activez la journalisation des diagnostics à l’aide de l’applet de commande PowerShell suivante :

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Les journaux d’activité ne nécessitent pas de compte de stockage distinct. L’utilisation du stockage pour la journalisation de l’accès et des performances occasionne des frais de service.

### <a name="enable-logging-with-azure-portal"></a>Activation de la journalisation avec le portail Azure

#### <a name="step-1"></a>Étape 1 :

Accédez à votre ressource dans le portail Azure. Cliquez sur **Journaux de diagnostic**. S’il s’agit de la première configuration du diagnostic, le panneau ressemble à l’image suivante :

Pour la passerelle Application Gateway, les 3 journaux sont disponibles.

* Journal d’accès
* Journal des performances
* Journal du pare-feu

Cliquez sur **Activer les diagnostics** pour démarrer la collecte de données.

![panneau des paramètres de diagnostics][1]

#### <a name="step-2"></a>Étape 2 :

Le panneau **Paramètres de diagnostic** contient les paramètres définissant les journaux de diagnostic. Dans cet exemple, Log Analytics sert à stocker les journaux. Cliquez sur **Configurer** sous **Log Analytics** pour configurer votre espace de travail. Vous pouvez également utiliser des concentrateurs d’événements et un compte de stockage pour enregistrer les journaux de diagnostic.

![panneau des diagnostics][2]

#### <a name="step-3"></a>Étape 3

Sélectionnez ou créez un espace de travail OMS existant. Pour cet exemple, un espace existant est utilisé.

![espaces de travail OMS][3]

#### <a name="step-4"></a>Étape 4

Lorsque vous avez terminé, confirmez les paramètres, puis cliquez sur **Enregistrer** pour enregistrer les paramètres.

![confirmer la sélection][4]

### <a name="activity-log"></a>Journal d’activité

Ce journal (anciennement appelé « journal des opérations ») est généré par Azure par défaut.  Les journaux sont conservés pendant 90 jours dans la banque de journalisation des événements d’Azure. Pour en savoir plus sur ces journaux, lisez l’article [Affichage des événements et du journal d’activité](../monitoring-and-diagnostics/insights-debugging-with-events.md).

### <a name="access-log"></a>Journal d’accès

Ce journal n’est généré que si vous l’avez activé au niveau de chaque passerelle Application Gateway, comme détaillé dans les étapes précédentes. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Chaque accès à la passerelle Application Gateway est journalisé au format JSON, comme indiqué dans l’exemple suivant :

```json
{
    "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
    "operationName": "ApplicationGatewayAccess",
    "time": "2016-04-11T04:24:37Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId":"ApplicationGatewayRole_IN_0",
        "clientIP":"37.186.113.170",
        "clientPort":"12345",
        "httpMethod":"HEAD",
        "requestUri":"/xyz/portal",
        "requestQuery":"",
        "userAgent":"-",
        "httpStatus":"200",
        "httpVersion":"HTTP/1.0",
        "receivedBytes":"27",
        "sentBytes":"202",
        "timeTaken":"359",
        "sslEnabled":"off"
    }
}
```

### <a name="performance-log"></a>Journal des performances

Ce journal n’est généré que si vous l’avez activé au niveau de chaque passerelle Application Gateway, comme détaillé dans les étapes précédentes. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Les données suivantes sont enregistrées :

```json
{
    "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> La latence est calculée à partir de l’heure de la réception du premier octet de la requête HTTP jusqu’à l’heure de l'envoi du dernier octet de la réponse HTTP. Il s’agit de la somme du temps de traitement de l’Application Gateway et du coût du réseau pour le backend, ainsi que le temps pris par le backend pour traiter la demande.

### <a name="firewall-log"></a>Journal du pare-feu

Ce journal n’est généré que si vous l’avez activé au niveau de chaque passerelle Application Gateway, comme détaillé dans les étapes précédentes. Ce fichier journal nécessite également que ce pare-feu d’applications web soit configuré sur une passerelle d’application. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Les données suivantes sont enregistrées :

```json
{
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
    "operationName": "ApplicationGatewayFirewall",
    "time": "2016-09-20T00:40:04.9138513Z",
    "category": "ApplicationGatewayFirewallLog",
    "properties":     {
        "instanceId":"ApplicationGatewayRole_IN_0",
        "clientIp":"108.41.16.164",
        "clientPort":1815,
        "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
        "ruleId":"OWASP_973336",
        "message":"XSS Filter - Category 1: Script Tag Vector",
        "action":"Logged",
        "site":"Global",
        "message":"XSS Filter - Category 1: Script Tag Vector",
        "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
}
```

### <a name="view-and-analyze-the-activity-log"></a>Afficher et analyser le journal d’activité

Vous pouvez afficher et analyser les données du journal d’activité en utilisant l’une des méthodes suivantes :

* **Outils Azure :** récupérez les informations à partir du journal d’activité au moyen d’Azure PowerShell, de l’interface de ligne de commande Azure, de l’API REST Azure ou du Portail Azure en version préliminaire.  Des instructions pas à pas pour chaque méthode sont détaillées dans l’article [Opérations d’activité avec Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI :** si vous n’avez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing) , vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’activité Azure pour Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), vous pouvez analyser vos données avec des tableaux de bord préconfigurés à utiliser en l’état ou à personnaliser.

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>Affichage et analyse du journal d’accès, des performances et du pare-feu

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) peut collecter le compteur et les fichiers de journal des événements à partir de votre compte de stockage d’objets Blob et inclut des visualisations et de puissantes fonctionnalités de recherche pour analyser vos journaux.

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées de journal JSON pour les journaux d’accès et des performances. Une fois que vous avez téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

> [!TIP]
> Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans Github.
> 
> 

## <a name="metrics"></a>Mesures

Les mesures représentent une fonctionnalité de certaines ressources Azure, vous permettant d’afficher les compteurs de performances dans le portail. Pour la passerelle Application Gateway, une mesure est disponible au moment de la rédaction de cet article. Cette mesure, le débit, peut être affichée dans le portail. Accédez à une passerelle d’application et cliquez sur **Mesures**. Sélectionnez Débit dans la section **Mesures disponibles** pour afficher les valeurs. L’image suivante montre un exemple avec les filtres qui peuvent servir à afficher les données dans différentes périodes.

Pour afficher la liste des mesures actuellement prises en charge, visitez [Mesures prises en charge avec Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![affichage des métriques][5]

### <a name="alert-rules"></a>Règles d'alerte

Des règles d’alerte peuvent être démarrées en fonction des mesures d’une ressource. Cela signifie que pour la passerelle d’application, une alerte peut appeler un webhook ou envoyer un e-mail à un administrateur si le débit de la passerelle d’application est au-dessus ou en dessous d’un seuil pour une période spécifiée.

L’exemple suivant vous guidera dans la création d’une règle d’alerte qui envoie un e-mail à un administrateur lorsqu’un seuil de débit a été dépassé.

#### <a name="step-1"></a>Étape 1 :

Cliquez sur **Ajouter une alerte de mesure** pour commencer. Ce panneau est également accessible à partir du panneau Mesures.

![panneau Règles d’alerte][6]

#### <a name="step-2"></a>Étape 2

Dans le panneau **Ajouter une règle**, remplissez les sections Nom, Condition et Notifier, puis cliquez sur **OK** lorsque vous avez terminé.

Le sélecteur **Condition** comporte 4 valeurs : **Supérieur à**, **Supérieur ou égal à**, **Inférieur à** ou **Inférieur ou égal à**.

Le sélecteur **Période** permet de définir une période allant de 5 minutes à 6 heures.

En sélectionnant **Envoyer des e-mails aux propriétaires, contributeurs et lecteurs** , l’e-mail peut être dynamiquement basé sur les utilisateurs qui ont accès à cette ressource. Dans le cas contraire, une liste d’utilisateurs séparée par des virgules peut être spécifiée dans la zone de texte **Adresse(s) de messagerie d’administrateur(s) supplémentaire(s)** .

![panneau Ajouter une règle][7]

Si le seuil est dépassé, un e-mail similaire à celui de l’image suivante est envoyé :

![e-mail de seuil dépassé][8]

Une liste d’alertes apparaît lorsqu’une alerte de mesure a été créée, et fournit un aperçu de toutes les règles d’alerte.

![affichage de la règle d’alerte][9]

Pour en savoir plus sur les notifications d’alerte, consultez [Réception de notifications d'alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Pour en savoir plus sur les webhooks et sur la façon de les utiliser avec des alertes, consultez [Configurer un webhook sur une alerte de métrique Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>Étapes suivantes

* Visualisation du compteur et des journaux des événements avec [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)
* [Visualiser votre journal d’activité Azure avec Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
* Billet de blog sur [l’affichage et l’analyse du journal d’activité Azure dans Power BI](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
