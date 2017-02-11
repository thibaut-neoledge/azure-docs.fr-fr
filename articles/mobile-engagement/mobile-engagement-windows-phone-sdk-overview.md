---
title: "Vue d’ensemble du Kit de développement logiciel Windows Phone Silverlight"
description: "Vue d&quot;ensemble du Kit de développement Silverlight de Windows Phone pour Azure Engagement Mobile"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 105335b3c4c6e805f9398fbc002d5c4051acd147


---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Vue d'ensemble du Kit de développement Silverlight de Windows Phone pour Azure Engagement Mobile
Cette introduction contient les informations nécessaires pour intégrer Azure Mobile Engagement à une application Windows Phone Silverlight. Si vous souhaitez tout d'abord l'essayer, pensez à suivre notre didacticiel de [15 minutes](mobile-engagement-windows-phone-get-started.md).

Cliquez pour voir le [contenu du Kit de développement logiciel (SDK)](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Procédures d'intégration
1. Commencez ici : [Intégration de Mobile Engagement à votre application Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
2. Pour les notifications : [Intégration de Reach (Notifications) à votre application Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Implémentation du plan de la balise : [Utilisation de l'API de marquage avancée Mobile Engagement dans votre application Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Notes de publication
### <a name="330-04192016"></a>3.3.0 (04/19/2016)
Partie du package NuGet *MicrosoftAzure.MobileEngagement***v3.4.0**

* Ajout de l'API « TestLogLevel » aux journaux de console enable/disable/filter émis par le Kit de développement logiciel (SDK).

Pour les versions antérieures, consultez les [notes de publication complètes](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>Procédures de mise à niveau
Si vous avez déjà intégré une ancienne version de notre SDK à votre application, tenez compte des points suivants avant de procéder à la mise à niveau du SDK.

Vous devrez peut-être suivre quelques procédures si vous avez manqué plusieurs versions du kit SDK. Consultez l’ensemble des [procédures de mise à niveau](mobile-engagement-windows-phone-upgrade-procedure.md). Par exemple, si vous migrez de la version 0.10.1 vers 0.11.0, vous devez tout d'abord suivre la procédure « Migration de 0.9.0 vers 0.10.1 », puis la procédure « Migration de 0.10.1 vers 0.11.0 ».

### <a name="from-200-to-330"></a>Migration de 2.0.0 vers 3.3.0
#### <a name="test-logs"></a>Journaux des tests
Les journaux de console produits par le Kit de développement logiciel (SDK) peuvent maintenant être activés/désactivés/filtrés. Pour personnaliser ce résultat, mettez à jour la propriété `EngagementAgent.Instance.TestLogEnabled` avec une des valeurs disponibles à partir de l'énumération `EngagementTestLogLevel`, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Mise à niveau à partir de versions antérieures
Consultez [Procédures de mise à niveau](mobile-engagement-windows-phone-upgrade-procedure.md)




<!--HONumber=Nov16_HO3-->


