---
title: "Intégration du SDK Android d&quot;Azure Mobile Engagement"
description: "Dernières mises à jour et procédures du SDK Android pour Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: af776804affea5abe599594d7b486bbead982f3e


---
# <a name="release-notes"></a>Notes de publication
## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Plus aucun verrou Wi-Fi.
* Résolution d'un blocage lors de l’appel de getDeviceId avant init (bogue introduit dans 4.2.0).

## <a name="422-05172016"></a>4.2.2 (05/17/2016)
* Améliorations de la stabilité.

## <a name="421-05102016"></a>4.2.1 (05/10/2016)
* Sécurité : Désactivation de l’accès aux fichiers locaux dans les vues web.
* Sécurité : Suppression de la classe `EngagementPreferenceActivity` qui étend la classe `PreferenceActivity` obsolète et non sécurisée.
* Sécurité : Les activités Reach sont maintenant documentées pour utiliser `exported="false"`. Vous pouvez aussi utiliser cet indicateur dans les versions précédentes du SDK.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* Le Kit de développement logiciel (SDK) est désormais sous licence MIT.
* Autorise la spécification d’un identificateur d’appareil personnalisé au moment de l’initialisation du Kit de développement logiciel (SDK).

## <a name="415-02012016"></a>4.1.5 (01/02/2016)
* Améliorations de la stabilité.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)
* Améliorations de la stabilité.

## <a name="413-1292015"></a>4.1.3 (12/9/2015)
* Améliorations de la stabilité.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)
* Améliorations de la stabilité.

## <a name="411-11042015"></a>4.1.1 (11/04/2015)
* Améliorations de la stabilité.

## <a name="410-08252015"></a>4.1.0 (25/08/2015)
* Gestion du nouveau modèle d’autorisation pour Android M.
* Vous pouvez désormais configurer les fonctionnalités de localisation lors de l’exécution au lieu d’utiliser `AndroidManifest.xml`.
* Résolution d’un bogue d’autorisation : si vous utilisez `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` n’est plus nécessaire.
* Améliorations de la stabilité.

## <a name="400-07062015"></a>4.0.0 (06/07/2015)
* Modifications de protocole interne pour rendre les analyses et la diffusion plus fiables.
* Native Push (GCM/ADM) est désormais également utilisé pour les notifications dans l’application. Vous devez donc configurer les informations d’identification Native Push pour tout type de campagne push.
* Correction de la notification de la vue d’ensemble : elle ne s’affichait que 10 s après avoir été transmise.
* Résolution d’un bogue dans l’affichage web : un clic sur un lien exécutait également l’URL de l’action par défaut.
* Résolution d’un blocage rare lié à la gestion du stockage local.
* Correction de la gestion des chaînes de configuration dynamique.
* Mise à jour du CLUF.

## <a name="300-02172015"></a>3.0.0 (17/02/2015)
* Version initiale d'Azure Engagement Mobile
* La configuration d'appId est remplacée par la configuration d'une chaîne de connexion.
* Suppression de l'API pour envoyer et recevoir des messages XMPP arbitraires d'entités XMPP arbitraires.
* Suppression de l'API pour envoyer et recevoir des messages entre appareils.
* Améliorations de sécurité.
* Suppression du suivi de Google Play et SmartAd.




<!--HONumber=Nov16_HO3-->


