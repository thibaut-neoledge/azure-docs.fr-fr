<properties title="Prise en main de l'authentification Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Mise en route][Mise en route]
> -   [Que s'est-il passé ?][Que s'est-il passé ?]

## Prise en main d'Azure Active Directory (Projets API web)

##### Demander une authentification pour l'accès aux contrôleurs

Tous les contrôleurs de votre projet ont été dotés de l'attribut **Authorize**. Cet attribut permet de demander à l'utilisateur de s'authentifier avant d'accéder aux API définies par ces contrôleurs. Pour autoriser un accès anonyme au contrôleur, cet attribut doit être supprimé du contrôleur. Pour définir plus précisément les autorisations, appliquez cet attribut à chaque méthode nécessitant une autorisation, au lieu de l'appliquer à la classe de contrôleur.

[En savoir plus sur Azure Active Directory][En savoir plus sur Azure Active Directory]

  [Mise en route]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [Que s'est-il passé ?]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [En savoir plus sur Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
