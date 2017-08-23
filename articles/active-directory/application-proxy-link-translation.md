---
title: "Traduire les liens et Proxy d’application d’URL Azure AD | Documents Microsoft"
description: "Couvre les bases sur les connecteurs de proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: d716476deb81f6627cf03401f78ab399ae940e68
ms.contentlocale: fr-fr
ms.lasthandoff: 07/04/2017

---

# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Rediriger les liens codés en dur pour les applications publiées avec le Proxy d’application Azure AD

Le Proxy d’application Azure AD rend vos applications locales disponibles pour les utilisateurs distants ou sur leurs propres appareils. Toutefois, certaines applications, ont été développées pour un public local, ce qui signifie généralement que les liens locaux sont incorporés dans le code HTML et ne fonctionnent pas correctement lorsque l’application est utilisée à distance. Cela est plus fréquent lorsque plusieurs applications locales s’indiquent l’une l’autre et que vos utilisateurs prévoient d’utiliser des liens pour vous déplacer entre les applications au lieu de taper l’URL externe pour chaque application.

La fonctionnalité de traduction de lien du Proxy d’application a été développée pour résoudre ce problème. Lorsque vous avez des applications qui indiquent directement les points de terminaison internes ou des ports, vous pouvez mapper ces URL internes aux URL de Proxy d’application externes publiées. Activez la traduction de lien et les recherches de Proxy d’application via le code HTML, CSS, puis sélectionnez les balises JavaScript pour les liens internes publiés et traduisez-les afin que vos utilisateurs obtiennent une expérience sans interruption.

>[!NOTE]
>La fonctionnalité de traduction de lien est destinée aux clients qui, pour une raison quelconque, ne peuvent pas utiliser de domaines personnalisés dans les mêmes URL internes et externes pour leurs applications. Avant d’activer cette fonctionnalité, consultez si [des domaines personnalisés dans le Proxy d’application Azure AD](active-directory-application-proxy-custom-domains.md) peuvent vous être utiles.

## <a name="how-link-translation-works"></a>Comment fonctionnement la traduction de lien

Après l’authentification, lorsque le serveur proxy transmet les données d’application à l’utilisateur, le Proxy d’application analyse l’application pour obtenir des liens codés en dur et les remplace par leurs URL externes publiées respectives.

### <a name="which-links-are-affected"></a>Quels sont les liens qui sont affectés ?

La fonctionnalité de traduction de lien recherche uniquement les liens qui se trouvent dans les balises de code dans le corps d’une application. Le Proxy d’application dispose d’une fonctionnalité distincte pour traduire les URL ou les cookies dans les en-têtes. 

Il existe deux types de liens internes communs dans des applications locales :

- **Les liens internes relatifs** qui indiquent une ressource partagée dans une structure de fichiers locaux, tels que `/claims/claims.html`. Ces liens fonctionnent automatiquement dans les applications qui sont publiées via le Proxy d’application et continuent de fonctionner avec ou sans traduction de lien. 
- **Les liens internes codés en dur** vers d’autres applications locales comme `http://expenses` ou des fichiers publiés comme `http://expenses/logo.jpg`. La fonctionnalité de traduction de lien fonctionne sur les liens internes codés en dur et les modifie pour pointer vers les URL externes par lesquelles les utilisateurs distants doivent passer.

### <a name="how-do-apps-link-to-each-other"></a>Comment les applications sont-elles liées l’une à l’autre ?

La traduction de lien est activée pour chaque application, afin que vous contrôliez l’expérience utilisateur pour chaque application. Activez la traduction de lien pour une application lorsque vous souhaitez que les liens *depuis* cette application soient traduits et pas les liens *vers* cette application. 

Par exemple, supposons que vous disposez de trois applications publiées via le Proxy d’application qui sont toutes liées l’une à l’autre : Profits, Dépenses et Transports. Il existe une quatrième application, Commentaires, qui n’est pas publiée via le Proxy d’application.

Lorsque vous activez la traduction de lien pour l’application Profits, les liens vers Dépenses et Transports sont redirigés vers les URL externes pour ces applications, mais le lien vers Commentaires n’est pas redirigé, car il n’existe aucune URL externe. Les liens à partir de Dépenses et Transports de retour vers Profits ne fonctionnent pas, car la traduction de lien n’a pas été activée pour ces deux applications.

![Liens à partir de Profit vers d’autres applications lorsque la traduction de lien est activée](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Quels sont les liens qui ne sont pas traduits ?

Pour améliorer les performances et la sécurité, certains liens ne sont pas traduits :

- Les liens non inclus dans des balises de code. 
- Liens internes ouverts à partir d’autres programmes. Les liens envoyés par e-mail ou message instantané ou inclus dans d’autres documents, ne sont pas traduits. Les utilisateurs doivent savoir comment accéder à l’URL externe.

Si vous avez besoin prendre en charge un de ces deux scénarios, vous pouvez utiliser les mêmes URL internes et externes, ce qui élimine le besoin de traduction de lien.  

## <a name="enable-link-translation"></a>Activer la traduction de lien

Bien démarrer avec la traduction de lien est aussi simple que cliquer sur un bouton :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Applications d’entreprise** > **toutes les applications** > sélectionnez l’application que vous souhaitez gérer > **Proxy d’application**.
3. Définissez **Traduire les URL dans le corps de l’application** sur **Oui**.

   ![Sélectionnez Oui pour traduire les URL dans le corps de l’application](./media/application-proxy-link-translation/select_yes.png).
4. Sélectionnez **Enregistrer** pour enregistrer vos modifications.

Maintenant, lorsque les utilisateurs accèdent à cette application, le proxy recherche automatiquement les URL internes qui ont été publiées via le Proxy d’application sur votre client.

## <a name="send-feedback"></a>Envoyer des commentaires

Nous souhaitons votre aide pour que cette fonctionnalité fonctionne pour toutes vos applications. Nous recherchons plus de 30 balises dans HTML et CSS et envisageons les cas JavaScript à prendre en charge. Si vous disposez d’un exemple de liens générés qui ne sont pas en cours de traduction, envoyez un extrait de code aux [Commentaires du Proxy d’application](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Étapes suivantes
- [Utilisez des domaines personnalisés avec le Proxy d’application Azure AD pour que l’URL interne et externe soit identiques](active-directory-application-proxy-custom-domains.md)

