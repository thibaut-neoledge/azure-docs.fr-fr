---
title: "Présentation du volet d’accès | Microsoft Docs"
description: "Découvrez comment utiliser les différentes versions du volet d’accès (navigateur web, application Android, iPhone et iPad) pour accéder aux applications SaaS qui vous ont été affectées."
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
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 2dd9c96e1c15e424588869b61eb26501b1b66be3


---
# <a name="introduction-to-the-access-panel"></a>Présentation du volet d’accès
Le volet d’accès est un portail web qui permet aux utilisateurs disposant d’un compte professionnel dans Azure Active Directory de voir et de lancer les applications cloud auxquelles ils ont été autorisés à accéder par l’administrateur Azure AD. Si vous êtes un utilisateur final disposant des éditions Azure Active Directory, vous pouvez également utiliser les fonctionnalités de gestion de groupes en libre-service via le volet d’accès. <br>
 Le volet d’accès est distinct du portail de gestion Azure et n’exige pas des utilisateurs qu’ils aient un abonnement Azure. 

![Volet d'accès][1] 

Le volet d’accès permet aux utilisateurs de modifier certains paramètres de leur profil, y compris :

* modifier le mot de passe associé à leur compte professionnel ;
* modifier les paramètres de réinitialisation de mot de passe ;
* modifier les paramètres de préférence et de contact liés à l’authentification multifacteur (pour les comptes qui ont été contraints de l’utiliser par un administrateur) ;
* afficher les détails de compte tels que votre ID d’utilisateur, l’adresse de messagerie de secours et les numéros de téléphone mobile et de bureau ;
* afficher et lancer des applications cloud auxquelles l’accès vous a été accordé par l’administrateur Azure AD. Pour plus d’informations sur le volet d’accès du point de vue de l’utilisateur final, consultez [Utilisation du volet d’accès](https://msdn.microsoft.com/library/azure/dn756411.aspx);
* gérer les groupes en libre-service. Plus précisément, vous pouvez créer et gérer des groupes de sécurité et demander l’appartenance à des groupes de sécurité dans Azure AD. Pour plus d’informations, consultez [Gestion de groupe libre-service pour les utilisateurs dans Azure AD](active-directory-accessmanagement-self-service-group-management.md) et [Gérer vos groupes](active-directory-manage-groups.md). 

## <a name="accessing-the-access-panel"></a>Accès au volet d’accès
Les utilisateurs accèdent au volet d’accès en visitant l’URL suivante dans un navigateur web :  <br> 
**http://myapps.microsoft.com**

Si vous avez configuré la personnalisation de votre page de connexion, vous pouvez charger cette personnalisation par défaut en ajoutant le domaine de votre organisation à la fin de l’URL :  <br> 
**http://myapps.microsoft.com/contosobuild.com**

Dans ce cas, tout nom de domaine actif ou vérifié ayant été configuré sous l’onglet Domaines de votre annuaire dans le portail de gestion Azure peut être utilisé, comme illustré dans la capture d’écran ci-dessous.

![Jouets Wingtip][2]  

Cette URL doit être distribuée à tous les utilisateurs qui se connecteront aux applications intégrées à Azure AD.

## <a name="authentication"></a>Authentification
Pour accéder au volet d’accès, un utilisateur doit être authentifié à l’aide d’un compte professionnel dans Azure AD. <br>
 Un utilisateur peut être authentifié dans Azure AD directement. <br>
 En guise d’alternative, si une organisation a configuré la fédération à l’aide des services ADFS ou d’autres technologies, les utilisateurs peuvent être authentifiés par Windows Server Active Directory.

Si un utilisateur dispose d’un abonnement Azure ou Office 365 et qu’il utilise le portail de gestion Azure ou une application Office 365, la liste des applications lui est présentée sans qu’il ait à se connecter à nouveau. Les utilisateurs qui ne sont pas authentifiés sont invités à se connecter à l’aide du nom d’utilisateur et du mot de passe correspondant à leur compte dans Azure AD. Si l’organisation a configuré la fédération, la saisie du nom d’utilisateur suffit.

Une fois authentifiés, les utilisateurs peuvent interagir avec les applications qui ont été intégrées à l’annuaire par l’administrateur. Pour découvrir comment intégrer des applications à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Configuration requise du navigateur web
Au minimum, le volet d’accès nécessite un navigateur avec prise en charge de JavaScript et CSS activée. Pour que l’utilisateur soit connecté aux applications à l’aide de l’authentification unique avec mot de passe, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement quand un utilisateur sélectionne une application configurée pour l’authentification unique avec mot de passe.

À l’heure actuelle, l’extension du volet d’accès est disponible pour les navigateurs Internet Explorer 8 et versions ultérieures, Chrome et Firefox.

## <a name="mobile-app-support"></a>Prise en charge des applications mobiles
Pour pouvoir se connecter à des applications activées pour l’authentification unique avec mot de passe sur des appareils iOS et Android, les utilisateurs doivent installer l’application mobile My Apps publiée par l’équipe Azure Active Directory.

### <a name="my-apps-for-android"></a>My Apps pour Android
My Apps pour Android est prise en charge sur tout appareil Android exécutant Android 4.1 et versions ultérieures. Elle est disponible dans le [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![My apps][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>My Apps pour iPhone et iPad
My Apps pour iOS est prise en charge sur tout iPhone ou iPad exécutant iOS version 7 et ultérieures. Elle est disponible dans l’Apple App Store.

![Profil d’applications][4]    

> [!NOTE]
> Vous pouvez vous connecter aux applications qui prennent en charge la fédération avec Azure AD (y compris Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 et plus de 70 autres) sur presque n’importe quel navigateur web sur n’importe quel appareil sans nécessiter de plug-in ou d’application mobile. Le reste de l’expérience de volet d’accès accessible à la page [https://myapps.microsoft.com](https://myapps.microsoft.com/) ne nécessite pas non plus l’utilisation de l’application mobile My Apps sur un appareil mobile.
> 
> 

## <a name="tips-for-testing-the-end-user-experience"></a>Conseils pour tester l’expérience utilisateur
Si vous êtes administrateur Azure et que vous êtes connecté au portail de gestion Azure à l’aide d’un compte dans l’annuaire, vous serez automatiquement connecté au volet d’accès sous votre compte d’administrateur actuel. Dans ce cas, vous pourrez voir toutes les applications qui ont été affectées à ce compte.

**Pour tester sous un compte d’utilisateur différent**

1. Cliquez sur le menu utilisateur dans le coin supérieur droit du portail Azure ou du volet d’accès, puis sélectionnez «**Déconnexion**». Vous êtes alors déconnecté d’Azure AD.
2. Accédez au panneau d’accès sur **http://myapps.microsoft.com**.
3. Dans la page de connexion, entrez le nom d’utilisateur et le mot de passe du compte dans l’annuaire que vous souhaitez tester.

## <a name="launching-applications"></a>Lancement d’applications
Plusieurs types d’applications peuvent apparaître dans le volet d’accès.

### <a name="office-365-applications"></a>Applications Office 365
Si une organisation utilise des applications Office 365 et que l’utilisateur dispose d’une licence pour celles-ci, les applications Office 365 apparaissent dans le volet d’accès de l’utilisateur.

Quand un utilisateur clique sur la vignette d’une application Office 365, il est redirigé vers cette application et connecté automatiquement.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Applications Microsoft et tierces configurées avec l’authentification unique basée sur la fédération
Il s’agit d’applications que l’administrateur a ajoutées à la section Active Directory du portail de gestion Azure avec le mode d’authentification unique défini sur «*Authentification unique avec Microsoft Azure AD*». Un utilisateur voit ces applications uniquement si l’accès lui a été accordé explicitement par l’administrateur.

Quand un utilisateur clique sur la vignette de l’une de ces applications, il est redirigé vers cette application et connecté automatiquement.

### <a name="password-based-sso-without-identity-provisioning"></a>Authentification unique avec mot de passe sans approvisionnement d’identité
Il s’agit d’applications que l’administrateur a ajoutées à la section Active Directory du portail de gestion Azure avec le mode d’authentification unique défini sur «*Authentification unique avec mot de passe*». <br>  Tous les utilisateurs de l’annuaire voient toutes les applications qui ont été configurées dans ce mode.

La première fois qu’un utilisateur clique sur une vignette de l’une de ces applications, il est invité à installer le plug-in d’authentification par mot de passe pour Internet Explorer ou Chrome, ce qui peut nécessiter le redémarrage de son navigateur web. Quand il est renvoyé au volet d’accès et qu’il clique de nouveau sur la vignette de l’application, il est invité à fournir un nom d’utilisateur et un mot de passe pour l’application. Une fois le nom d’utilisateur et le mot de passe entrés, ces informations d’identification sont stockées de manière sécurisée dans Azure AD et liées à son compte dans Azure AD, et le volet d’accès connecte automatiquement l’utilisateur à l’application à l’aide de ces informations d’identification.

La prochaine fois qu’un utilisateur clique sur la vignette de l’application, il est automatiquement connecté à l’application sans avoir à réentrer les informations d’identification et sans avoir à réinstaller le plug-in d’authentification unique avec mot de passe.

Si les informations d’identification d’un utilisateur ont changé dans l’application tierce cible, l’utilisateur doit aussi mettre à jour ses informations d’identification stockées dans Azure AD. Pour cela, l’utilisateur doit choisir l’icône dans l’angle inférieur droit de la vignette de l’application et sélectionner « Mettre à jour les informations d’identification » pour réentrer le nom d’utilisateur et le mot de passe correspondant à cette application.

### <a name="password-based-sso-with-identity-provisioning"></a>Authentification unique avec mot de passe avec approvisionnement d’identité
Il s’agit d’applications que l’administrateur a ajoutées à la section Active Directory du portail de gestion Azure avec le mode d’authentification unique défini sur «*Authentification unique avec mot de passe*» ainsi que l’approvisionnement d’identité.

La première fois qu’un utilisateur clique sur une vignette de l’une de ces applications, il est invité à installer le plug-in d’authentification par mot de passe pour Internet Explorer ou Chrome, ce qui peut nécessiter le redémarrage de son navigateur web. Quand il est renvoyé au volet d’accès et qu’il clique de nouveau sur la vignette de l’application, il est connecté automatiquement à l’application.

Certaines applications peuvent exiger de l’utilisateur qu’il modifie son mot de passe lors de la première connexion. Si les informations d’identification d’un utilisateur ont changé dans l’application tierce cible, l’utilisateur doit aussi mettre à jour ses informations d’identification stockées dans Azure AD. Pour cela, l’utilisateur doit choisir l’icône dans l’angle inférieur droit de la vignette de l’application et sélectionner « Mettre à jour les informations d’identification » pour réentrer le nom d’utilisateur et le mot de passe correspondant à cette application.

### <a name="application-with-existing-sso-solutions"></a>Application avec solutions d’authentification unique existantes
Lors de la configuration de l’authentification unique pour une application, le portail de gestion Azure propose une troisième option, l’authentification unique existante. Cette option permet simplement à l’administrateur de créer un lien vers une application et de le placer dans le panneau d’accès pour les utilisateurs sélectionnés. Par exemple, s’il existe une application qui est configurée pour authentifier les utilisateurs avec Active Directory Federation Services 2.0, l’administrateur peut utiliser l’option d’authentification unique existante pour créer un lien vers cette application dans le panneau d’accès. Lorsque les utilisateurs accèdent au lien, ils sont authentifiés via Active Directory Federation Services 2.0 ou toute autre solution d’authentification unique fournie par l’application.

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



<!--HONumber=Dec16_HO4-->


