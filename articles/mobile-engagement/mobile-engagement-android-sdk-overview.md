---
title: Intégration du SDK Android pour Azure Mobile Engagement
description: Décrit comment intégrer le SDK Azure Mobile Engagement dans les applications Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal

---
# Intégration du SDK Android pour Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Windows universel](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Ce document décrit toutes les options d’intégration et de configuration disponibles pour le SDK Azure Mobile Engagement pour Android.

## Composants requis
[!INCLUDE [Conditions préalables](../../includes/mobile-engagement-android-prereqs.md)]

## Fonctionnalités avancées
### Fonctionnalités de génération de rapports
Vous pouvez ajouter les fonctionnalités suivantes :

1. [Options de génération de rapports avancés](mobile-engagement-android-advanced-reporting.md)
2. [Options de génération de rapports d’emplacement](mobile-engagement-android-location-reporting.md)
3. [Options de configuration avancées](mobile-engagement-android-advanced-configuration.md)

### Notifications :
[comment intégrer le module Couverture (notifications) à votre application Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM) : [comment intégrer GCM à Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM) : [comment intégrer ADM à Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### Implémentation du plan de balise :
[comment utiliser l'API de balisage avancée de Mobile Engagement dans votre application Android](mobile-engagement-android-use-engagement-api.md)

## Notes de publication
### 4\.2.3 (08/10/2016)
* Plus aucun verrou Wi-Fi.
* Résolution d'un blocage lors de l’appel de getDeviceId avant init (bogue introduit dans 4.2.0).

Pour toutes les versions, consultez les [notes de publication complètes](mobile-engagement-android-release-notes.md).

## Procédures de mise à niveau
Si vous avez déjà intégré une version plus ancienne de notre kit de développement logiciel (SDK) dans votre application, consultez [Procédures de mise à niveau](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_0817_2016-->