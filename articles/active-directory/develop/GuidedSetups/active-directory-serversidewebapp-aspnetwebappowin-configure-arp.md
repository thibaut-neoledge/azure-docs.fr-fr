---
title: Prise en main du serveur web ASP.NET Azure AD v2 - Configuration | Microsoft Docs
description: "Implémentation de la connexion Microsoft dans une solution ASP.NET avec une application basée sur un navigateur web traditionnel utilisant le standard OpenID Connect"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b10c8bd6878f6891b2420ae7fb4b65b19144762c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configurer votre application Web ASP.NET avec les informations d’inscription de l’application

Dans cette étape, vous allez configurer votre projet pour utiliser SSL, puis vous servir de l’URL SSL pour configurer les informations d’inscription de votre application. Ensuite, ajoutez les informations d’inscription de l’application à votre solution via *web.config*.

1.    Dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre `Properties` (si vous ne voyez pas une fenêtre de propriétés, appuyez sur F4).
2.    Remplacez `SSL Enabled` par `True` :<br/>
![Propriétés du projet](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
3.    Copiez la valeur de `SSL URL` ci-dessus et collez-la dans le champ `Redirect URL` en haut de cette page, puis cliquez sur *Mettre à jour* :
4.    Ajoutez le code suivant dans le fichier `web.config` situé dans le dossier racine, sous la section `configuration\appSettings` :

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```

