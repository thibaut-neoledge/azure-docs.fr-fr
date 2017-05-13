---
title: "Authentification basée sur un en-tête avec PingAccess pour le proxy d’application Azure AD | Microsoft Docs"
description: "Publiez des applications avec PingAccess et Application Proxy pour prendre en charge l’authentification basée sur un en-tête."
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
ms.date: 04/21/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 8db76d1f83cdf1cf53ddd1e9c69c56400d04af2d
ms.contentlocale: fr-fr
ms.lasthandoff: 04/29/2017

---

# <a name="publish-applications-that-support-header-based-authentication-with-azure-ad-application-proxy-and-pingaccess"></a>Publier des applications qui prennent en charge l’authentification basée sur un en-tête avec le proxy d’application et PingAccess pour Azure AD

Azure Active Directory Application Proxy et PingAccess se sont associés pour permettre aux clients Azure Active Directory d’accéder à davantage d’applications. PingAccess développe les [offres Application Proxy existantes](active-directory-application-proxy-get-started.md) en intégrant l’accès distant aux applications qui utilisent des en-têtes d’authentification. 

## <a name="what-is-pingaccess-for-azure-ad"></a>Présentation de PingAccess pour Azure AD

Pour permettre aux utilisateurs d’accéder aux applications qui utilisent des en-têtes d’authentification, vous publiez l’application pour un accès distant dans Application Proxy et PingAccess. Application Proxy traite ces applications comme n’importe quelle autre, en utilisant Azure AD pour authentifier l’accès et faire transiter le trafic via le service de connecteur. PingAccess se trouve devant les applications et convertit le jeton d’accès Azure AD en en-tête afin que l’application reçoive l’authentification dans un format qu’elle puisse lire. 

Vos utilisateurs ne remarqueront rien lorsqu’ils se connecteront pour utiliser vos applications d’entreprise. Ils pourront toujours travailler depuis n’importe où et sur n’importe quel appareil. Lorsque vos utilisateurs sont au bureau, ni Application Proxy ni PingAccess n’interceptent le trafic. Ainsi, ils bénéficient de l’expérience habituelle.

Par ailleurs, comme les connecteurs Application Proxy redirigent le trafic distant vers l’ensemble des applications, quel que soit leur type d’authentification, ils continuent à équilibrer la charge automatiquement. 

## <a name="how-do-i-get-access"></a>Comment puis-je avoir accès ?

Comme ce scénario est possible grâce à un partenariat entre Azure Active Directory et PingAccess, vous avez besoin de licences pour les deux services. Les abonnements Azure Active Directory Premium sont fournis avec une licence de PingAccess de démarrage, ce qui vous permet de configurer jusqu’à 20 applications avec ce flux. 

Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

## <a name="publish-your-first-application"></a>Publier votre première application

Cet article s’adresse aux personnes qui publient une application pour la première fois dans ce cas de figure. Il explique comment mettre en œuvre Application Proxy et PingAccess, en plus de la procédure de publication. Si vous avez déjà configuré les deux services mais que vous souhaitez un rappel sur la procédure de publication, vous pouvez ignorer les deux sections concernant l’inscription.

>[!NOTE]
>Comme ce cas de figure repose sur un partenariat entre Azure AD et PingAccess, certaines de ces instructions figurent sur le site Ping Identity. 

### <a name="install-an-application-proxy-connector"></a>Installer un connecteur Application Proxy

Si vous avez activé Application Proxy et installé un connecteur, vous pouvez ignorer ces étapes.

Le connecteur Application Proxy est un service Windows Server qui dirige le trafic de vos employés distants vers vos applications publiées. Pour plus d’informations sur l’installation, consultez [Activer Application Proxy dans le portail Azure](active-directory-application-proxy-enable.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur. 
2. Sélectionnez **Azure Active Directory** > **Application Proxy**.
3. Sélectionnez **Download Connector** (Télécharger le connecteur) pour lancer le téléchargement du connecteur Application Proxy. Suivez les instructions d’installation. 
4. Le téléchargement du connecteur doit activer automatiquement Application Proxy pour votre annuaire, mais pas si vous pouvez sélectionner **Enable Application Proxy** (Activer Application Proxy). 

![Activer Application Proxy et télécharger le connecteur](./media/application-proxy-ping-access/install-connector.png)

### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Ajouter votre application dans Azure AD avec Application Proxy

Cette section comprend deux parties. Tout d’abord, vous devez publier l’application dans Azure AD. Ensuite, vous devez collecter certaines informations sur cette application que vous pouvez utiliser pendant la procédure PingAccess. 

#### <a name="publish-the-app"></a>Publier l’application

1. Si cela n’a pas été fait dans la partie précédente, connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur global. 
2. Sélectionnez **Azure Active Directory (Azure Active Directory)** > **Enterprise applications (Applications d’entreprise)**. 
3. Sélectionnez **Ajouter** en haut du panneau. 
4. Sélectionnez **On-premises application (Application locale)**.
5. Saisissez les informations concernant votre nouvelle application dans les champs requis. Suivez les conseils ci-dessous pour les paramètres :
  - **URL interne** : logiquement, vous devez indiquer l’URL de la page de connexion de l’application lorsque vous êtes sur le réseau d’entreprise. Pour les besoins de ce partenariat, le connecteur doit traiter le proxy PingAccess en tant que première page de l’application. Utilisez le format suivant : `https://<host name of your PA server>:<port>/<App path name>`. Le port par défaut est 3000, mais vous pouvez le configurer dans PingAccess.
  - **Méthode de pré-authentification** : Azure Active Directory
  - **Traduire des URL dans les en-têtes** : Non
6. Sélectionnez **Ajouter** en bas du panneau. Votre application est ajoutée et le menu de démarrage rapide s’ouvre. 
7. Dans le menu de démarrage rapide, sélectionnez **Assign a user for testing (Attribuer un utilisateur à des fins de test)**, et ajoutez au moins un utilisateur à l’application. Vérifiez que ce compte de test a accès à l’application locale. 
8. Sélectionnez **Affecter** pour enregistrer l’affectation de l’utilisateur de test. 
9. Dans le panneau de gestion de l’application, sélectionnez **Authentification unique**. 
10. Choisissez **Authentification basée sur un en-tête** dans le menu déroulant. Sélectionnez **Enregistrer**.

  ![Sélectionner l’authentification basée sur un en-tête](./media/application-proxy-ping-access/sso-header.PNG)

11. Fermez le panneau des applications d’entreprise ou faites défiler vers la gauche pour revenir au menu Azure Active Directory. 
12. Sélectionnez **Inscriptions d’applications**.
13. Sélectionnez l’application que vous venez d’ajouter, puis sélectionnez **Reply URLs (URL de réponse)**. 
14. Vérifiez si l’URL externe que vous avez affectée à votre application à l’étape 5 figure dans la liste des URL de réponse. Si tel n’est pas le cas, ajoutez-la maintenant. 
15. Dans le panneau Paramètres d’application, sélectionnez **Required permissions (Autorisations requises)**. 
16. Sélectionnez **Ajouter**. Pour l’API, choisissez **Windows Azure Active Directory**, puis **Sélectionner**. Pour les autorisations, choisissez **Lire et écrire toutes les applications** et **Se connecter et lire le profil utilisateur**, puis **Sélectionner** et **Terminé**.  

  ![Sélectionner les autorisations](./media/application-proxy-ping-access/select-permissions.png) 

#### <a name="collect-information-for-the-pingaccess-steps"></a>Collecter les informations pour la procédure PingAccess

1. Dans le panneau des paramètres de l’application, sélectionnez **Propriétés**. Enregistrez la valeur figurant dans **ID de l’Application**. Cette information est utilisée comme ID de client lors de la configuration de PingAccess.
2. Dans le panneau des paramètres de l’application, sélectionnez **Clés**. 
3. Créez une clé en entrant une description et en choisissant une date d’expiration dans le menu déroulant. 
4. Sélectionnez **Enregistrer**. Un GUID s’affiche dans le champ **Valeur**. 

  Enregistrez cette valeur maintenant, car vous ne la reverrez plus, une fois cette fenêtre fermée. 

5. Fermez le panneau des inscriptions d’application ou faites défiler vers la gauche pour revenir au menu Azure Active Directory.
6. Sélectionner **Propriétés**.
7. Enregistrez le GUID **ID de répertoire**. 

### <a name="download-pingaccess-and-configure-your-app"></a>Télécharger PingAccess et configurer votre application

La procédure concernant PingAccess dans ce scénario continue dans la documentation de Ping Identity sur la [configuration de PingAccess pour Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Ces étapes expliquent comment obtenir un compte PingAccess (si vous n’en avez pas), installer le serveur PingAccess et créer une connexion Azure AD OIDC Provider avec l’ID de répertoire que vous avez copié à partir du portail Azure. Ensuite, utilisez les valeurs ID d’application et Clé pour créer une session Web sur PingAccess. Enfin, vous pouvez configurer le mappage d’identité et créer un hôte virtuel, le site et l’application.

### <a name="test-your-app"></a>Test de l'application

Une fois toutes ces étapes effectuées, votre application doit être opérationnelle. Pour la tester, ouvrez un navigateur et accédez à l’URL externe que vous avez créée lors de la publication de l’application dans Azure. Connectez-vous au compte de test que vous avez attribué à l’application. 

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes du proxy d’application](active-directory-application-proxy-troubleshoot.md)

