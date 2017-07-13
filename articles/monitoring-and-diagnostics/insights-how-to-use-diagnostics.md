---
title: Activation de la surveillance et des diagnostics dans Microsoft Azure | Microsoft Docs
description: "Découvrez comment configurer des diagnostics pour vos ressources dans Azure."
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: af1947a9-c211-4aa1-8924-880a86240be4
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: b82bb1ab419831e803689edb2a2a7fe256dde5a2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/07/2017


---
<a id="enable-monitoring-and-diagnostics" class="xliff"></a>

# Activation de la surveillance et des diagnostics
Dans le [portail Azure](https://portal.azure.com), vous pouvez configurer les données de surveillance et de diagnostic, riches et fréquentes, concernant vos ressources. Vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931932.aspx) ou le [Kit de développement (SDK) .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) pour configurer les diagnostics par programmation.

Les données de diagnostic, de surveillance et de mesure d’Azure sont enregistrées dans le compte de stockage de votre choix. Ceci vous permet d'utiliser les outils avec lesquels vous souhaitez lire les données, de l'explorateur de stockage à Power BI,  en passant par les outils tiers.

<a id="when-you-create-a-resource" class="xliff"></a>

## Lorsque vous créez une ressource
La plupart des services vous permettent d'activer les diagnostics lorsque vous les créez dans le [portail Azure](https://portal.azure.com).

1. Accédez à **Nouveau** et choisissez la ressource qui vous intéresse.
2. Sélectionnez **Configuration facultative**.
    ![Panneau des diagnostics](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)
3. Sélectionnez **Diagnostics**, puis cliquez sur **Activé**. Vous devrez ensuite choisir le compte de stockage dans lequel vous souhaitez que les diagnostics soient enregistrés. Des frais de données normaux vous sont facturés pour le stockage et les transactions lorsque vous envoyez des diagnostics à un compte de stockage.
4. Cliquez sur **OK** et créez la ressource.

<a id="change-settings-for-an-existing-resource" class="xliff"></a>

## Modifiez les paramètres d'une ressource existante
Si vous avez déjà créé une ressource et que vous souhaitez modifier les paramètres de diagnostic (pour modifier le niveau de collecte de données, par exemple), vous pouvez le faire directement dans le portail Azure.

1. Accédez à la ressource et cliquez sur la commande **Paramètres** .
2. Sélectionnez **Diagnostics**.
3. Le panneau **Diagnostics** dispose, pour cette ressource, de tous les diagnostics possibles et d’une surveillance de collecte de données. Pour certaines ressources, vous pouvez également choisir une stratégie de **Rétention** des données, vous permettant de les nettoyer de votre compte de stockage.
    ![Diagnostics de stockage](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. Une fois que vous avez choisi vos paramètres, cliquez sur la commande **Enregistrer** . La surveillance des données peut prendre un certain temps avant d’apparaître, si vous l'activez pour la première fois.

<a id="categories-of-data-collection-for-virtual-machines" class="xliff"></a>

### Catégories de collecte de données pour les machines virtuelles
Pour les machines virtuelles, toutes les mesures et tous les journaux sont enregistrés à des intervalles d'une minute, de façon à vous fournir les informations les plus récentes possible concernant votre machine.

* **Mesures de base** : mesures sur l'état d'intégrité de votre machine virtuelle (par ex., processeur et mémoire)
* **Mesures réseau et mesures Web** : mesures concernant vos connexions réseau et services Web
* **Mesures .NET** : mesures concernant les applications .NET et ASP.NET exécutées sur votre machine virtuelle
* **Mesures SQL** : si vous exécutez Microsoft SQL Service, ses mesures de performances
* **Journaux d'événements Windows de l'application** : événements Windows envoyés au canal Application
* **Journaux d'événements Windows du système** : événements Windows envoyés au canal Système. Cela inclut également tous les événements de [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
* **Journaux d'événements Windows de la sécurité** : événements Windows envoyés au canal Sécurité
* **Journaux d'infrastructure de diagnostics** : journalisation concernant l'infrastructure de collecte de diagnostics
* **Journaux IIS** : journaux concernant votre serveur IIS

Notez qu’à ce stade, certaines distributions de Linux ne sont pas prises en charge, et que l'agent invité doit être installé sur la machine virtuelle.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
* [Réceptions de notifications d'alerte](insights-receive-alert-notifications.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* [Surveillance des mesures de service](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
* [Mise à l’échelle automatique du nombre d’instances](insights-how-to-scale.md) pour vous assurer que votre service est mis à l’échelle en fonction de la demande.
* [Surveillance des performances d'une application](../application-insights/app-insights-azure-web-apps.md) si vous voulez comprendre exactement comment votre code s'exécute dans le cloud.
* [Affichage des événements et du journal d’activité](insights-debugging-with-events.md) pour découvrir tout ce qui s'est produit dans votre service.
* [Suivi de l'intégrité du service](insights-service-health.md) pour déterminer à quel moment Azure a rencontré des interruptions de service ou une dégradation des performances.


