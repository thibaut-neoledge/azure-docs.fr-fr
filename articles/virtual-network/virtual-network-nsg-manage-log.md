---
title: "Surveiller les opérations, les événements et les compteurs pour les groupes de sécurité réseau | Microsoft Docs"
description: "Découvrez comment activer la journalisation des compteurs, des événements et des opérations pour les groupes de sécurité réseau"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/14/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 30542a5166dffda4a99fe2fccd9e1c5d6127cabd


---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Analyse de journaux pour les groupes de sécurité réseau (NSG)
Vous pouvez utiliser différents types de journaux dans Azure pour gérer les groupes de sécurités réseau et résoudre les problèmes associés. Certains de ces journaux sont accessibles par le biais du portail et tous les journaux peuvent être extraits à partir d’un stockage blob Azure et affichés dans différents outils, notamment [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel et PowerBI. Pour en savoir plus sur les différents types de journaux, consultez la liste ci-dessous.

* **Journaux d’audit :** vous pouvez utiliser les [journaux d’audit Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anciennement journaux des opérations) pour afficher toutes les opérations soumises à votre ou vos abonnements Azure, ainsi que leur état. Les journaux d’audit sont activés par défaut et peuvent être affichés dans le portail Azure en version préliminaire.
* **Journaux des événements :** vous pouvez utiliser ces journaux pour voir quelles règles NSG sont appliquées aux machines virtuelles et les rôles d’instance en fonction de l’adresse MAC. L’état de ces règles est collecté toutes les 60 secondes.
* **Journaux des compteurs :** vous pouvez utiliser ces journaux pour afficher le nombre de fois où chaque règle NSG a été appliquée pour refuser ou autoriser le trafic.

> [!WARNING]
> Les journaux ne sont disponibles que pour les ressources déployées avec le modèle de déploiement de Resource Manager. Vous ne pouvez pas les utiliser pour les ressources utilisant le modèle de déploiement classique. Pour mieux comprendre ces deux modèles, reportez-vous à l’article [Présentation du déploiement de Resource Manager et du déploiement classique](../resource-manager-deployment-model.md) .
> 
> 

## <a name="enable-logging"></a>Activation de la journalisation
La journalisation d’audit est automatiquement activée systématiquement pour chaque ressource Resource Manager. Vous devez activer la journalisation des événements et des compteurs pour commencer à collecter les données disponibles dans ces journaux. Pour activer la journalisation, procédez comme suit.

1. Connectez-vous au [portail Azure](https://portal.azure.com). Si vous ne disposez pas d’un groupe de sécurité réseau existant, [créez un NSG](virtual-networks-create-nsg-arm-ps.md) avant de continuer.
2. Dans le portail en préversion, cliquez sur **Parcourir** >> **Groupes de sécurité réseau**.
   
   ![Portail en version préliminaire - Groupes de sécurité réseau](./media/virtual-network-nsg-manage-log/portal-enable1.png)
3. Sélectionnez un groupe de sécurité réseau existant.
   
    ![Portail en version préliminaire - Paramètres des groupes de sécurité réseau](./media/virtual-network-nsg-manage-log/portal-enable2.png)
4. Dans le panneau **Paramètres**, cliquez sur **Diagnostics**, puis dans le volet **Diagnostics**, en regard de **État**, cliquez sur **Activé**.
5. Dans le panneau **Paramètres**, cliquez sur **Compte de stockage**, puis sélectionnez un compte de stockage existant ou créez-en un.  

> [AZURE.INFORMATION] Les journaux d’audit ne nécessitent pas de compte de stockage distinct. L’utilisation du stockage pour la journalisation des événements et des règles occasionnera des frais de service.
> 
> 

1. Dans la liste déroulante sous **Compte de stockage**, indiquez si vous souhaitez journaliser des événements et/ou des compteurs, puis cliquez sur **Enregistrer**.
   
    ![Portail en version préliminaire - Journaux de diagnostics](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>Journal d’audit
Ce journal (anciennement appelé « journal des opérations ») est généré par Azure par défaut.  Les journaux sont conservés pendant 90 jours dans la banque de journalisation des événements d’Azure. Pour en savoir plus sur ces journaux, lisez l’article [Affichage des événements et des journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="counter-log"></a>Journal des compteurs
Ce journal n’est généré que si vous l’avez activé au niveau de chaque groupe de sécurité réseau, comme détaillé ci-dessous. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Chaque règle appliquée aux ressources est enregistrée au format JSON, comme indiqué ci-dessous.

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>Journal des événements
Ce journal n’est généré que si vous l’avez activé au niveau de chaque groupe de sécurité réseau, comme détaillé ci-dessous. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Les données suivantes sont enregistrées :

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>Afficher et analyser le journal d’audit
Vous pouvez afficher et analyser les données du journal d’audit en utilisant l’une des méthodes suivantes :

* **Outils Azure :** récupérez les informations à partir des journaux via Azure PowerShell, l’interface de ligne de commande Azure, l’API REST Azure ou le portail Azure en version préliminaire.  Des instructions détaillées pour chaque méthode sont détaillées dans l’article [Opérations d’audit avec Resource Manager](../resource-group-audit.md) .
* **Power BI :** si vous n’avez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing) , vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’audit Azure pour Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) , vous pouvez analyser vos données avec des tableaux de bord préconfigurés à utiliser en l’état ou à personnaliser.

## <a name="view-and-analyze-the-counter-and-event-log"></a>Afficher et analyser les journaux des compteurs et des événements
Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) peut collecter le compteur et les fichiers de journal des événements à partir de votre compte de stockage d’objets Blob et inclut des visualisations et de puissantes fonctionnalités de recherche pour analyser vos journaux.

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées de journal JSON pour les journaux des événements et des compteurs. Une fois que vous avez téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

> [!TIP]
> Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans Github.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Visualisation du compteur et des journaux des événements avec [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)
* [Visualiser vos journaux d’audit Azure avec Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
* [Afficher et analyser les journaux d’audit Azure dans Power BI et bien plus encore](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) .




<!--HONumber=Nov16_HO3-->


