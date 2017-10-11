---
title: "Prise en main d’Azure AD v2 avec les applications Android - Configuration | Documents Microsoft"
description: "Cet article explique comment une application Android peut obtenir un jeton d’accès et appeler l’API Microsoft Graph ou des API qui nécessitent des jetons d’accès à partir d’un point de terminaison Azure Active Directory v2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: c09937582118ebcc5b8cbc1f43a0a2019f2f7a89
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Ajouter les informations d’inscription de l’application à votre application

Dans cette étape, vous devez ajouter l’ID client à votre projet.

1.  Ouvrez `MainActivity` (sous `app` > `java` > *`{host}.{namespace}`*).
2.  Remplacez la ligne commençant par `final static String CLIENT_ID` par :
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Ouvrez `app` > `manifests` > `AndroidManifest.xml`.
4. Ajoutez l’activité suivante au nœud `manifest\application`. Cette opération inscrit une activité `BrowserTabActivity` pour autoriser le système d’exploitation à reprendre l’exécution de votre application une fois l’authentification effectuée :

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

### <a name="what-is-next"></a>Étapes suivantes

[Test et validation](active-directory-mobileanddesktopapp-android-test.md)
