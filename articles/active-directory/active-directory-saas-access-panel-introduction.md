---
title: "Présentation du volet d’accès | Microsoft Docs"
description: "Découvrez comment utiliser les différentes versions du volet d’accès (navigateur web, application Android, iPhone et iPad) pour accéder aux applications SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c5a0f3f958d42d514a95477c1e39f02c78307d4d
ms.contentlocale: fr-fr
ms.lasthandoff: 04/06/2017

---
# <a name="what-is-the-access-panel"></a>Présentation du volet d’accès
Le volet d’accès est un portail basé sur le web. Il permet à un utilisateur qui dispose d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD) d’afficher et de démarrer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. Un utilisateur disposant d’éditions Azure AD peut également utiliser des fonctionnalités de gestion des groupes en libre-service via le volet d’accès.

Le volet d’accès est distinct du portail Azure et n’exige pas des utilisateurs qu’ils aient un abonnement Azure.

![Volet d'accès][1]

Le volet d’accès permet aux utilisateurs de modifier certains paramètres de leur profil, y compris :

* Modifier le mot de passe associé à un compte professionnel ou scolaire.
* Modifier les paramètres de réinitialisation de mot de passe.
* Modifier les paramètres de contact et de préférence liés à l’authentification multifacteur (pour les comptes qui ont été contraints de l’utiliser par un administrateur).
* Afficher les détails de compte tels que l’ID d’utilisateur, l’adresse de messagerie de secours et les numéros de téléphone mobile et de bureau.
* Afficher et démarrer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. Pour plus d’informations sur le volet d’accès du point de vue de l’utilisateur, consultez [Utilisation du volet d’accès](https://msdn.microsoft.com/library/azure/dn756411.aspx).
* gérer les groupes en libre-service. Plus précisément, l’administrateur peut créer et gérer des groupes de sécurité et demander l’appartenance à des groupes de sécurité dans Azure AD. Pour plus d’informations, consultez [Gestion de groupe libre-service pour les utilisateurs dans Azure AD](active-directory-accessmanagement-self-service-group-management.md) et [Gérer vos groupes](active-directory-manage-groups.md).

## <a name="accessing-the-access-panel"></a>Accès au volet d’accès
Les utilisateurs accèdent au volet d’accès en visitant l’URL suivante dans un navigateur web :  <br>
**http://myapps.microsoft.com**

Si vous avez configuré la personnalisation de votre page de connexion, vous pouvez charger cette personnalisation par défaut en ajoutant le domaine de votre organisation à la fin de l’URL :  <br>
**http://myapps.microsoft.com/contosobuild.com**

Dans ce cas, vous pouvez utiliser tout nom de domaine actif ou vérifié ayant été configuré sous l’onglet **Domaines** de votre annuaire dans le portail Azure, comme illustré dans la capture d’écran suivante :

![Nom de domaine Jouets Wingtip][2]  

Cette URL doit être distribuée à tous les utilisateurs qui se connecteront aux applications qui sont intégrées à Azure AD.

## <a name="authentication"></a>Authentification
Pour accéder au volet d’accès, un utilisateur doit être authentifié via un compte professionnel ou scolaire dans Azure AD. Un utilisateur peut être authentifié dans Azure AD directement. En guise d’alternative, si une organisation a configuré la fédération à l’aide des services Active Directory Federation Services (AD FS) ou d’autres technologies, les utilisateurs peuvent être authentifiés par Windows Server Active Directory.

Si un utilisateur dispose d’un abonnement Azure ou Office 365 et qu’il utilise le portail Azure ou une application Office 365, il verra la liste des applications sans qu’il ait à se connecter à nouveau. Les utilisateurs qui ne sont pas authentifiés sont invités à se connecter à l’aide du nom d’utilisateur et du mot de passe correspondant à leur compte dans Azure AD. Si l’organisation a configuré la fédération, la saisie du nom d’utilisateur suffit.

Après l’authentification, les utilisateurs peuvent interagir avec les applications que l’administrateur a intégrées à l’annuaire. Pour découvrir comment intégrer des applications à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Configuration requise du navigateur web
Au minimum, le volet d’accès nécessite un navigateur prenant en charge JavaScript et dans lequel CSS est activée. Pour que l’utilisateur soit connecté aux applications via l’authentification unique (SSO) avec mot de passe, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement lorsqu’un utilisateur sélectionne une application configurée pour l’authentification unique (SSO) avec mot de passe.

L’extension du volet d’accès est actuellement disponible pour les navigateurs Internet Explorer 8 et ultérieur, Edge, Chrome et Firefox.

## <a name="mobile-app-support"></a>Prise en charge des applications mobiles
L’équipe Azure Active Directory publie l’application mobile My Apps. Lorsque les utilisateurs installent cette application, ils peuvent se connecter aux applications SSO avec mot de passe sur des appareils iOS et Android.

> [!NOTE]
> Les utilisateurs peuvent se connecter aux applications qui prennent en charge la fédération avec Azure AD (y compris Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 et plus de 70 autres) sur presque n’importe quel navigateur web sur n’importe quel appareil sans nécessiter de plug-in ou d’application mobile. Le reste de l’[expérience de volet d’accès](https://myapps.microsoft.com/) ne nécessite pas non plus l’utilisation de l’application mobile My Apps sur un appareil mobile.
>
>

### <a name="my-apps-for-android"></a>My Apps pour Android
My Apps pour Android est prise en charge sur n’importe quel appareil Android exécutant Android version 4.1 et ultérieures. Elle est disponible dès aujourd’hui dans le [Google Play store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Écran de My Apps pour Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>My Apps pour iPhone et iPad
My Apps pour iOS est prise en charge sur n’importe quel iPhone ou iPad exécutant iOS version 7 et ultérieures. Elle est disponible dès aujourd’hui dans l’Appel Play Store.

![Écran My Apps pour iOS][4]    

## <a name="tips-for-testing-the-user-experience"></a>Conseils pour tester l’expérience utilisateur
Si vous êtes administrateur Azure et que vous êtes connecté au portail Azure à l’aide d’un compte dans l’annuaire, vous êtes automatiquement connecté au volet d’accès sous votre compte d’administrateur actuel. Dans ce cas, vous pourrez voir toutes les applications qui ont été affectées à ce compte.

Pour tester sous un compte d’utilisateur*différent* :

1. Cliquez sur le menu utilisateur dans le coin supérieur droit du portail Azure ou du volet d’accès, puis sélectionnez **Déconnexion**. Cette opération vous déconnecte d’Azure AD.
2. Accédez au [volet d’accès](http://myapps.microsoft.com).
3. Dans la page de connexion, entrez le nom d’utilisateur et le mot de passe du compte dans l’annuaire que vous souhaitez tester.

## <a name="starting-applications"></a>Démarrage des applications
Plusieurs types d’applications peuvent apparaître dans le volet d’accès.

### <a name="office-365-applications"></a>Applications Office 365
Si une organisation utilise des applications Office 365 et que l’utilisateur dispose d’une licence pour celles-ci, les applications Office 365 apparaissent dans le volet d’accès de l’utilisateur.

Lorsqu’un utilisateur clique sur la vignette d’une application Office 365, il est redirigé vers cette application et connecté automatiquement.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Applications Microsoft et tierces configurées avec l’authentification unique (SSO) basée sur la fédération
L’administrateur peut ajouter des applications à la section Active Directory du portail Azure avec le mode d’authentification unique (SSO) défini sur **Authentification unique avec Azure AD**. Un utilisateur ne voit ces applications que si l’administrateur lui a explicitement accordé un accès aux applications.

Lorsqu’un utilisateur clique sur la vignette de l’une de ces applications, il est redirigé vers cette application et connecté automatiquement.

### <a name="password-based-sso-without-identity-provisioning"></a>Authentification unique avec mot de passe sans approvisionnement d’identité
L’administrateur peut ajouter des applications à la section Active Directory du portail Azure avec le mode d’authentification unique (SSO) défini sur **Authentification unique avec mot de passe**. Tous les utilisateurs de l’annuaire voient toutes les applications qui ont été configurées dans ce mode.

La première fois qu’un utilisateur clique sur la vignette de l’une de ces applications, il est invité à installer le plug-in d’authentification unique (SSO) avec mot de passe pour Internet Explorer ou Chrome. L’installation peut nécessiter que l’utilisateur redémarre son navigateur web. Lorsque l’utilisateur est renvoyé au volet d’accès et qu’il clique de nouveau sur la vignette de l’application, il est invité à fournir un nom d’utilisateur et un mot de passe pour l’application. Une fois que l’utilisateur entre le nom d’utilisateur et le mot de passe, ces informations d’identification sont stockées de manière sécurisée dans Azure AD et liées à son compte dans Azure AD.

La prochaine fois que l’utilisateur clique sur la vignette de l’application, il est connecté automatiquement à l’application. L’utilisateur ne devra pas entrer à nouveau les informations d’identification ou réinstaller le plug-in d’authentification unique (SSO) avec mot de passe.

Si les informations d’identification d’un utilisateur ont changé dans l’application tierce cible, l’utilisateur doit également mettre à jour ses informations d’identification stockées dans Azure AD. Pour cela, l’utilisateur choisit l’icône sur la vignette de l’application, puis sélectionne **Mettre à jour les informations d’identification** pour entrer à nouveau le nom d’utilisateur et le mot de passe correspondant à cette application.

### <a name="password-based-sso-with-identity-provisioning"></a>Authentification unique avec mot de passe avec approvisionnement d’identité
L’administrateur peut ajouter des applications à la section Active Directory du portail Azure avec le mode d’authentification unique (SSO) défini sur **Authentification unique avec mot de passe**, ainsi que l’approvisionnement d’identité.

La première fois qu’un utilisateur clique sur la vignette d’une application, il est invité à installer le plug-in d’authentification unique (SSO) avec mot de passe pour Internet Explorer ou Chrome. L’installation peut nécessiter que l’utilisateur redémarre son navigateur web. Lorsque l’utilisateur revient au volet d’accès et qu’il clique de nouveau sur la vignette de l’application, il est connecté automatiquement à l’application.

Certaines applications peuvent exiger de l’utilisateur qu’il modifie son mot de passe lors de la première connexion. Si les informations d’identification d’un utilisateur ont changé dans l’application tierce cible, l’utilisateur doit également mettre à jour ses informations d’identification stockées dans Azure AD. Pour cela, l’utilisateur choisit l’icône sur la vignette de l’application, puis sélectionne **Mettre à jour les informations d’identification** pour entrer à nouveau le nom d’utilisateur et le mot de passe correspondant à cette application.

### <a name="application-with-existing-sso-solutions"></a>Application avec solutions d’authentification unique (SSO) existantes
Pour la configuration de l’authentification unique (SSO) pour une application, le portail Azure propose une troisième option, l’**authentification unique existante**. Cette option permet à l’administrateur de créer un lien vers une application et de le placer dans le volet d’accès pour les utilisateurs sélectionnés.

Par exemple, si une application est configurée pour authentifier les utilisateurs avec AD FS 2.0, l’administrateur peut utiliser l’option d’**authentification unique existante** pour créer un lien vers cette application dans le volet d’accès. Lorsque les utilisateurs accèdent au lien, ils sont authentifiés via AD FS 2.0 ou quelle que soit la solution d’authentification unique (SSO) existante fournie par l’application.

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)
* [Introduction à l’authentification unique et à la gestion de l’accès aux applications avec Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

