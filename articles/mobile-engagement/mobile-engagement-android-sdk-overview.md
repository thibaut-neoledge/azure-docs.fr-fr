<properties
	pageTitle="Intégration du SDK Android pour Azure Mobile Engagement"
	description="Décrit comment intégrer le SDK Azure Mobile Engagement dans les applications Android"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="piyushjo;ricksal" />

# Intégration du SDK Android pour Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows universel](mobile-engagement-windows-store-sdk-overview.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS](mobile-engagement-ios-sdk-overview.md)
- [Android](mobile-engagement-android-sdk-overview.md)

Ce document décrit toutes les options d’intégration et de configuration disponibles pour le SDK Azure Mobile Engagement pour Android.

## Composants requis

[AZURE.INCLUDE [Conditions préalables](../../includes/mobile-engagement-android-prereqs.md)]

## Fonctionnalités avancées

### Fonctionnalités de génération de rapports

Vous pouvez ajouter les fonctionnalités suivantes :

1. [Options de génération de rapports avancés](mobile-engagement-android-advanced-reporting.md)
2. [Options de génération de rapports d’emplacement](mobile-engagement-android-location-reporting.md)
3. [Options de configuration avancées](mobile-engagement-android-advanced-configuration.md)

### Notifications :
[comment intégrer le module Couverture (notifications) à votre application Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM) : [comment intégrer GCM à Mobile Engagement](mobile-engagement-android-gcm-integrate.md)

2. Amazon Device Messaging (ADM) : [comment intégrer ADM à Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### Implémentation du plan de balise :
[comment utiliser l'API de balisage avancée de Mobile Engagement dans votre application Android](mobile-engagement-android-use-engagement-api.md)

## Notes de publication

### 4\.2.2 (05/17/2016)

- Améliorations de la stabilité.

### 4\.2.1 (05/10/2016)

- Sécurité : Désactivation de l’accès aux fichiers locaux dans les vues web.
- Sécurité : Suppression de la classe `EngagementPreferenceActivity` qui étend la classe `PreferenceActivity` obsolète et non sécurisée.
- Sécurité : Les activités Reach sont maintenant documentées pour utiliser `exported="false"`. Vous pouvez aussi utiliser cet indicateur dans les versions précédentes du SDK.

### 4\.2.0 (03/11/2016)

- Le Kit de développement logiciel (SDK) est désormais sous licence MIT.
- Autorise la spécification d’un identificateur d’appareil personnalisé au moment de l’initialisation du Kit de développement logiciel (SDK).

Pour toutes les versions, consultez les [notes de publication complètes](mobile-engagement-android-release-notes.md).

## Procédures de mise à niveau

Si vous avez déjà intégré une version plus ancienne de notre SDK dans votre application, consultez [Procédures de mise à niveau](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_0518_2016-->