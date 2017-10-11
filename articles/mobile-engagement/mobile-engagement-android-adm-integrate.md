---
title: "Intégration du SDK Android d'Azure Mobile Engagement"
description: "Dernières mises à jour et procédures du SDK Android pour Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 43987962ea2b7b825b88643d18b4db65f1f1670e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-integrate-adm-with-engagement"></a>Comment intégrer ADM à Engagement
> [!IMPORTANT]
> Vous devez suivre la procédure d'intégration décrite dans le document « Comment intégrer Engagement sur Android » avant de suivre ce guide.
> 
> Ce document est utile uniquement si vous avez intégré le module Reach et que vous avez l’intention d’effectuer des transmissions de type Push vers des appareils Amazon. Pour intégrer les couvertures campagnes à votre application, lisez d'abord « Comment intégrer le module de couverture Engagement sur Android ».
> 
> 

## <a name="introduction"></a>Introduction
L'intégration d'ADM permet à votre application d’être envoyée lorsque vous ciblez des appareils Android Amazon.

Les charges utiles ADM envoyées vers le Kit de développement logiciel (SDK) contiennent toujours la clé `azme` dans l'objet de données. Donc si vous utilisez ADM à d’autres fins dans votre application, vous pouvez filtrer les notifications push en fonction de cette clé.

> [!IMPORTANT]
> Seuls les appareils Amazon Kindle exécutant Android 4.0.3 ou version ultérieure sont pris en charge par Amazon Device Messaging. Vous pouvez toutefois intégrer ce code en toute sécurité sur d'autres appareils.
> 
> 

## <a name="sign-up-to-adm"></a>S'inscrire à ADM
Si vous ne l'avez pas déjà fait, vous devez activer ADM sur votre compte Amazon.

La procédure détaillée est disponible ici : [<https://developer.amazon.com/sdk/adm/credentials.html>].

À la fin de la procédure, vous obtenez :

* Des informations d'identification OAuth (un ID client et une clé secrète client) pour qu'Engagement puisse effectuer un Push sur vos appareils.
* Une clé d'API qui doit être intégrée à votre application.

## <a name="sdk-integration"></a>Intégration du SDK
### <a name="managing-device-registrations"></a>Gestion des inscriptions des appareils
Chaque appareil doit envoyer une commande d'inscription aux serveurs ADM, sinon il ne pourra pas recevoir de campagnes.

Si vous utilisez déjà la [bibliothèque cliente ADM] et que vous avez déjà [intégré ADM], vous pouvez accéder directement au fichier android-sdk-adm-receive.

Si vous n'avez pas encore intégré ADM, Engagement vous permet de l'activer dans votre application encore plus facilement :

Modifiez le fichier `AndroidManifest.xml` :

* Ajoutez l'espace de noms Amazon. Le fichier doit commencer ainsi :
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* Dans la balise `<application/>`, ajoutez la section suivante :
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* Après l'ajout de la balise Amazon, vous pouvez rencontrer une erreur de build si la cible de la build du projet est antérieure à Android 2.1. Vous devez utiliser une cible de build **Android 2.1 +** (vous pourrez toujours avoir un `minSdkVersion` défini sur 4).
* Intégrez la clé d'API ADM en tant que ressource en suivant [cette procédure].

Ensuite, suivez les instructions des sections ci-dessous.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Communiquer l'ID d'inscription au service Push d'Engagement et recevoir des notifications
Pour communiquer l'ID d'inscription de l'appareil au service Push d'Engagement et recevoir ses notifications, ajoutez le code suivant au fichier `AndroidManifest.xml`, dans la balise `<application/>` (même si vous utilisez ADM sans Engagement) :

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Assurez-vous de disposer des autorisations suivantes dans votre `AndroidManifest.xml` (avant la balise `</application>`).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Accorder à Engagement des informations d'identification OAuth
Envoyez vos informations d’identification OAuth (ID client et clé secrète client) sur le portail Engagement.

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[bibliothèque cliente ADM]:https://developer.amazon.com/sdk/adm/setup.html
[intégré ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[cette procédure]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
