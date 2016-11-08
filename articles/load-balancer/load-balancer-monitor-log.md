---
title: Surveiller les opérations, les événements et les compteurs pour l’équilibreur de charge | Microsoft Docs
description: Découvrez comment activer la journalisation des événements d'alerte et de l'état des sondes d'intégrité pour l'équilibreur de charge Azure
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn
tags: azure-resource-manager

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2016
ms.author: sewhee

---
# Analyse des journaux de l’équilibreur de charge Azure (version préliminaire)
Vous pouvez utiliser différents types de journaux dans Azure pour gérer les équilibreurs de charge et résoudre les problèmes associés. Certains de ces journaux sont accessibles via le portail et tous les journaux peuvent être extraits à partir d’un stockage blob Azure et affichés dans différents outils, comme Excel et PowerBI. Pour en savoir plus sur les différents types de journaux, consultez la liste ci-dessous.

* **Journaux d’audit :** vous pouvez utiliser les [journaux d’audit Azure](../azure-portal/insights-debugging-with-events.md) (anciennement journaux des opérations) pour afficher toutes les opérations soumises à votre ou vos abonnements Azure, ainsi que leur état. Les journaux d’audit sont activés par défaut et peuvent être affichés dans le portail Azure.
* **Journaux des événements d'alerte :** vous pouvez utiliser ce journal pour afficher les alertes relatives à l’équilibreur de charge. L'état de l'équilibreur de charge est collecté toutes les cinq minutes. Ce journal est écrit uniquement si un événement d'alerte d’équilibreur de charge est généré.
* **Journaux des sondes d'intégrité :** vous pouvez utiliser ce journal pour vérifier l’état des sondes d'intégrité, le nombre d'instances en ligne dans l’équilibreur de charge principal, et le pourcentage de machines virtuelles qui reçoivent le trafic de l’équilibreur de charge. Ce journal est écrit à chaque changement de l’événement d’état de la sonde.

> [!WARNING]
> Les journaux ne sont disponibles que pour les ressources déployées avec le modèle de déploiement de Resource Manager. Vous ne pouvez pas les utiliser pour les ressources utilisant le modèle de déploiement classique. Pour mieux comprendre ces deux modèles, reportez-vous à l’article [Présentation du déploiement de Resource Manager et du déploiement classique](../resource-manager-deployment-model.md). <BR> L'analyse des journaux s’applique uniquement aux équilibreurs de charge accessibles sur Internet. Cette limitation est temporaire et peut changer à tout moment. N'oubliez pas de revenir sur cette page pour consulter les modifications.
> 
> 

## Activation de la journalisation
La journalisation d’audit est automatiquement activée systématiquement pour chaque ressource Resource Manager. Vous devez activer la journalisation des événements et des sondes d’intégrité pour commencer à collecter les données disponibles dans ces journaux. Pour activer la journalisation, procédez comme suit.

Connectez-vous au [portail Azure](http://portal.azure.com). Si vous ne disposez pas déjà d'un équilibreur de charge, [créez un équilibreur de charge](load-balancer-get-started-internet-arm-ps.md) avant de continuer.

Dans le portail, cliquez sur **Parcourir** >> **Équilibreurs de charge**.

![portail - équilibreur-charge](./media/load-balancer-monitor-log/load-balancer-browse.png)

Sélectionnez un équilibreur de charge existant >> **Tous les paramètres**.

![portail - paramètres-équilibreur-charge](./media/load-balancer-monitor-log/load-balancer-settings.png) <BR>

Dans le panneau **Paramètres**, cliquez sur **Diagnostics**, puis dans le volet **Diagnostics**, en regard de **État**, cliquez sur **Activé** dans le panneau **Paramètres**, cliquez sur **Compte de stockage**, puis sélectionnez un compte de stockage existant ou créez-en un.

Dans la liste déroulante sous **Compte de stockage**, indiquez si vous souhaitez journaliser des événements et/ou l’état des sondes d’intégrité, puis cliquez sur **Enregistrer**.

![Portail en version préliminaire - Journaux de diagnostics](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [AZURE.INFORMATION] Les journaux d’audit ne nécessitent pas de compte de stockage distinct. L’utilisation du stockage pour la journalisation des événements et des sondes d’intégrité occasionnera des frais de service.
> 
> 

## Journal d’audit
Ce journal (anciennement appelé « journal des opérations ») est généré par Azure par défaut. Les journaux sont conservés pendant 90 jours dans la banque de journalisation des événements d’Azure. Pour en savoir plus sur ces journaux, lisez l’article [Affichage des événements et des journaux d’audit](../azure-portal/insights-debugging-with-events.md).

## Journal des événements d'alerte
Ce journal n’est généré que si vous l’avez activé au niveau de chaque équilibreur de charge, comme détaillé ci-dessous. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Les informations sont enregistrées au format JSON, comme indiqué ci-dessous.

    {
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }


La sortie JSON affiche la propriété *eventname* qui décrit la raison pour laquelle l'équilibreur de charge a créé une alerte. Dans ce cas, l'alerte a été générée en raison de l'épuisement du port TCP à cause des limites de l’IP NAT source (SNAT).

## Journal des sondes d’intégrité
Ce journal n’est généré que si vous l’avez activé au niveau de chaque équilibreur de charge, comme détaillé ci-dessous. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Un conteneur nommé « insights-logs-loadbalancerprobehealthstatus » est créé et les données suivantes sont enregistrées :

        {
        "records":

        {
               "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 1,
                "healthPercentage": 50.000000
            }
        },
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 0,
                "healthPercentage": 100.000000
            }
        }

    ]
    }

La sortie JSON apparaît dans le champ des propriétés des informations de base pour l'état des sondes d’intégrité. La propriété *dipDownCount* indique le nombre total d'instances sur le serveur principal qui ne reçoivent pas le trafic réseau en raison d’une absence de réponse de la part de la sonde.

## Afficher et analyser le journal d’audit
Vous pouvez afficher et analyser les données du journal d’audit en utilisant l’une des méthodes suivantes :

* **Outils Azure :** récupérez les informations à partir des journaux via Azure PowerShell, l’interface de ligne de commande Azure, l’API REST Azure ou le portail Azure en version préliminaire. Des instructions détaillées pour chaque méthode sont détaillées dans l’article [Opérations d’audit avec Resource Manager](../resource-group-audit.md).
* **Power BI :** si vous n’avez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing), vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’audit Azure pour Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), vous pouvez analyser vos données avec des tableaux de bord préconfigurés à utiliser en l’état ou à personnaliser.

## Afficher et analyser les journaux des sondes d’intégrité et des événements
Vous devez vous connecter à votre compte de stockage et récupérer les entrées de journal JSON pour les journaux des événements et des sondes d’intégrité. Une fois que vous avez téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

> [!TIP]
> Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans Github.
> 
> 

## Ressources supplémentaires
* Billet de blog [Visualiser vos journaux d’audit Azure avec Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
* Billet de blog [Afficher et analyser les journaux d’audit Azure dans Power BI et bien plus encore](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_0824_2016-->