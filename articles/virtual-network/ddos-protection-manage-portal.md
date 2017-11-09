---
title: "Gérer le service Protection DDos Standard Azure à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment utiliser les données de télémétrie du service Protection DDos Standard Azure dans Azure Monitor pour atténuer une attaque."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 5c599b4cc867dbc9a081af3a081195b998f63954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gérer le service Protection DDos Standard Azure à l’aide du portail Azure

>[!IMPORTANT]
>Le service Protection DDos Standard Azure (Protection DDos) est disponible en préversion. Un nombre limité de ressources Azure prennent en charge le service Protection DDos, et ce dans un nombre limité de régions. Vous devez [vous inscrire au service](http://aka.ms/ddosprotection) pendant la préversion limitée afin que le service Protection DDos soit activé pour votre abonnement. L’équipe Azure DDoS vous contacte au moment de l’inscription pour vous guider tout au long du processus d’activation. Le service Protection DDos est disponible dans les régions États-Unis de l’Est, États-Unis de l’Ouest et États-Unis Centre-Ouest. Pendant la préversion, l’utilisation du service ne vous est pas facturée.

Cet article explique comment utiliser le portail Azure pour activer le service Protection DDos, pour le désactiver et pour utiliser les données de télémétrie afin d’atténuer une attaque. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="enable-ddos-protection"></a>Activer le service Protection DDos

Activez le service Protection DDos sur un réseau virtuel nouveau ou existant.

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Créer un réseau virtuel et activer le service Protection DDos

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Entrez les informations du réseau virtuel. Sous *Protection DDoS*, cliquez sur **Activé**, puis cliquez sur **Créer**.

    ![Création d’un réseau virtuel](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

Un avertissement indique que l’activation du service Protection DDos entraîne des frais. Aucun frais lié au service Protection DDos n’est facturé pendant la préversion. Les frais sont facturés au moment de la disponibilité générale ; les clients sont avertis 30 jours à l’avance de l’application de frais et de la disponibilité générale.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Activer le service Protection DDos sur un réseau virtuel existant 

1. Cliquez sur **Réseaux virtuels** dans le menu du portail Azure, puis sélectionnez votre réseau virtuel.
2. Cliquez sur **Protection DDoS**, cliquez sur **Activé** dans l’écran *Protection DDoS*, puis cliquez sur **Enregistrer**. 

Un avertissement indique que l’activation du service Protection DDos entraîne des frais. Aucun frais lié au service Protection DDos n’est facturé pendant la préversion. Les frais sont facturés au moment de la disponibilité générale ; les clients sont avertis 30 jours à l’avance de l’application de frais et de la disponibilité générale.

## <a name="disable-ddos-protection"></a>Désactiver le service Protection DDos

1. Cliquez sur **Réseaux virtuels** dans le menu du portail Azure, puis sélectionnez votre réseau virtuel.
2. Cliquez sur **Protection DDoS**, cliquez sur **Désactivé** dans l’écran *Protection DDoS*, puis cliquez sur **Enregistrer**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurer des alertes pour les métriques du service Protection DDos

En tirant parti de la configuration des alertes Azure Monitor, vous pouvez sélectionner les métriques du service Protection DDos disponibles de votre choix pour être averti quand il existe une atténuation active pendant une attaque. Quand les conditions sont remplies, vous recevez un e-mail d’alerte à l’adresse spécifiée.

1. Cliquez sur **Surveiller**, puis cliquez sur **Métriques**.
2. Dans l’écran *Métriques*, sélectionnez le groupe de ressources, le type de ressource **Adresse IP publique** et votre adresse IP publique Azure.
3. Pour configurer une alerte par e-mail pour une métrique, cliquez sur **Aucune alerte n’est configurée pour cette ressource. Cliquez pour ajouter une alerte**. Vous pouvez créer une alerte par e-mail sur n’importe quelle métrique, mais la métrique la plus évidente est **Sous attaque DDoS ou non**. Il s’agit d’une valeur booléenne 1 ou 0. **1** signifie que vous faites l’objet d’une attaque. **0** signifie que vous ne faites pas l’objet d’une attaque.
4. Pour recevoir un e-mail quand vous faites l’objet d’une attaque, définissez la métrique **Sous attaque DDoS ou non** et **Condition de supériorité à zéro (0) au cours des 5 dernières minutes**. Vous pouvez configurer des alertes similaires pour les autres métriques.

    ![Configurer les métriques](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Dans les quelques minutes qui suivent la détection d’une attaque, vous êtes informé grâce aux métriques Azure Monitor.

    ![Alerte en cas d’attaque](./media/ddos-protection-manage-portal/ddos-alert.png) 

Vous pouvez également découvrir plus en détail la [configuration de webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) et les [applications logiques](../logic-apps/logic-apps-what-are-logic-apps.md) pour créer des alertes.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurer la journalisation des métriques du service Protection DDos

1. Cliquez sur **Surveiller**, puis sur **Paramètres de diagnostic**.
2. Dans l’écran *Métriques*, sélectionnez le groupe de ressources, le type de ressource **Adresse IP publique** et votre adresse IP publique Azure.
3. Cliquez sur **Activer les diagnostics pour collecter les données suivantes**.

Vous disposez de trois options pour la journalisation :

- **Archiver dans un compte de stockage** : écrit les journaux dans un compte de stockage.
- **Diffuser vers Event Hub** : permet à un récepteur de journal de sélectionner les journaux à l’aide d’un hub d’événements. Cette option permet l’intégration à Splunk ou à d’autres systèmes SIEM.
- **Envoyer à Log Analytics** : écrit les journaux dans le service Azure OMS Log Analytics.

## <a name="use-ddos-protection-telemetry"></a>Utiliser les données de télémétrie du service Protection DDos

Les données de télémétrie pour une attaque sont fournies par le biais d’Azure Monitor en temps réel. Les données de télémétrie ne sont disponibles que pendant l’atténuation dont fait l’objet une adresse IP publique. Vous ne les voyez pas avant ou après l’atténuation d’une attaque.

1. Cliquez sur **Surveiller**, puis cliquez sur **Métriques**. 
2. Dans l’écran *Métriques*, sélectionnez le groupe de ressources, le type de ressource **Adresse IP publique** et votre adresse IP publique Azure. Une série de métriques disponibles s’affiche sur le côté gauche de l’écran. Quand elles sont sélectionnées, ces métriques sont représentées dans le graphique des métriques Azure Monitor sur l’écran de vue d’ensemble. 

Les noms des métriques présentent différents types de paquets et les octets/paquets, et les noms de balise sur chaque métrique sont essentiellement construits comme suit :

- **Nom de balise des paquets transférés (par exemple, Paquets entrants ignorés DDoS) :** nombre de paquets supprimés/nettoyés par le système de protection DDoS.
- **Nom de balise des paquets transférés (par exemple, Paquets entrants transférés DDoS) :** nombre de paquets transférés par le système DDoS vers l’adresse IP virtuelle de destination (trafic qui n’a pas été filtré).
- **Nom de balise du nombre de paquets (par exemple, Paquets entrants DDoS) :** nombre total de paquets ayant atteint le système de nettoyage, représentant la somme des paquets supprimés et transférés.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Analyser les journaux du stockage Azure avec Log Analytics](../log-analytics/log-analytics-azure-storage.md)
- [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)