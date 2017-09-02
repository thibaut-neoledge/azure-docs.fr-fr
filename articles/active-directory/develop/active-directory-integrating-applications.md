---
title: "Intégration d’applications à Azure Active Directory | Microsoft Docs"
description: "Ajout, mise à jour ou suppression d’une application dans Azure Active Directory (Azure AD)."
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: lenalepa
ms.custom: aaddev
ms.reviewer: luleon
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3be341bcb897a1481f145825429a1a94dfaae3b0
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="integrating-applications-with-azure-active-directory"></a>Intégration d’applications dans Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Les développeurs et les fournisseurs de logiciels en tant que service (SaaS) peuvent développer des services cloud commerciaux ou des applications métier qui peuvent être intégrés à Azure Active Directory (Azure AD) pour fournir un processus de connexion et d’autorisation sécurisé à leurs services. Pour intégrer une application ou un service à Azure AD, le développeur doit d’abord enregistrer les détails relatifs à son application auprès d’Azure AD à l’aide du portail Azure Classic.

Cet article vous montre comment ajouter, mettre à jour ou supprimer une application dans Azure AD. Vous découvrirez les différents types d'applications qui peuvent être intégrées à Azure AD, comment configurer vos applications pour accéder aux autres ressources telles que les API web, et bien plus encore.

Pour plus d’informations sur les deux objets Azure AD qui représentent une application enregistrée ainsi que sur leur relation, consultez [Objets principal du service et application](active-directory-application-objects.md). Pour connaître les directives de personnalisation à utiliser lors du développement d’applications avec Azure Active Directory, consultez [Instructions de personnalisation pour les applications intégrées](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Ajout d'une application
Toute application qui souhaite utiliser les fonctionnalités d’Azure AD doit d’abord être enregistrée dans un locataire Azure AD. Le processus d'enregistrement implique de fournir à Azure AD des informations sur votre application, notamment l'URL où elle est située, l'URL à laquelle envoyer une réponse après avoir authentifié un utilisateur, l'URI qui identifie l'application, et ainsi de suite.

Si vous créez une application web qui a simplement besoin de prendre en charge l’authentification des utilisateurs dans Azure AD, il vous suffit de suivre les instructions ci-dessous. Si votre application requiert des informations d’identification ou des autorisations pour accéder à une API web, ou de permettre aux utilisateurs d’autres locataires Azure AD d’y accéder, consultez la section [Mise à jour d’une application](#updating-an-application) pour continuer à configurer votre application.

### <a name="to-register-a-new-application-in-the-azure-portal"></a>Enregistrement d’une nouvelle application dans le portail Azure
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le volet de navigation de gauche, choisissez **Plus de services**, cliquez sur **Inscriptions d’application**, puis cliquez sur **Ajouter**.
4. Suivez les invites et créez une application. Si vous souhaitez des exemples spécifiques pour les applications web ou natives, consultez les [Démarrages rapides](active-directory-developers-guide.md).
  * Pour les applications web, indiquez l’**URL de connexion**, c’est-à-dire l’URL de base de votre application, à laquelle les utilisateurs peuvent se connecter, par exemple `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Pour les applications natives, indiquez un **URI de redirection**, qui sera utilisé par Azure AD pour retourner les réponses de jeton. Entrez une valeur spécifique à votre application, par exemple, `http://MyFirstAADApp`
5. Une fois l’inscription terminée, Azure AD affecte un identificateur client unique à votre application, l’ID d’application. Votre application a été ajoutée, et vous allez être redirigé vers la page de démarrage rapide pour votre application. Selon que votre application est une application native ou web, vous verrez des options différentes d'ajout de fonctionnalités supplémentaires à votre application. Une fois votre application ajoutée, vous pouvez commencer la mise à jour de votre application pour permettre aux utilisateurs de se connecter, accéder à des API web dans d'autres applications ou configurer l'application multilocataire (ce qui permet à d'autres organisations d’accéder à votre application).

> [!NOTE]
> Par défaut, l'inscription de l'application nouvellement créée est configurée pour autoriser les utilisateurs de votre répertoire à se connecter à votre application.
> 
> 

## <a name="updating-an-application"></a>Mise à jour d’une application
Une fois votre application inscrite avec Azure AD, il est possible qu’elle doive être mise à jour pour fournir un accès aux API web, être mise à la disposition d'autres organisations, et plus encore. Cette section décrit les différents scénarios dans lesquels vous devrez peut-être configurer davantage votre application. Tout d’abord, nous allons examiner une vue d’ensemble de l’infrastructure de consentement, un élément important à comprendre si vous développez des ressources/applications API qui seront utilisées par les applications clientes créées par les développeurs de votre organisation ou d’une autre entreprise.

Pour plus d'informations sur le fonctionnement de l'authentification dans Azure AD, consultez la section [Scénarios d'authentification pour Azure AD](active-directory-authentication-scenarios.md).

### <a name="overview-of-the-consent-framework"></a>Vue d’ensemble de l’infrastructure de consentement
L’infrastructure de consentement d’Azure AD facilite le développement d’applications clientes web et natives multilocataires qui doivent accéder aux API web sécurisées par un locataire Azure AD autre que celui dans lequel l’application cliente est enregistrée. Ces API web comprennent l’API Microsoft Graph (pour l’accès à Azure Active Directory, à Intune et aux services d’Office 365) et d’autres API de services Microsoft, en plus de vos propres API web. L’infrastructure est basée sur le consentement d’un utilisateur ou d’un administrateur à l’inscription d’une application dans son répertoire, ce qui peut impliquer l’accès aux données du répertoire.

Par exemple, si une application cliente web doit lire les informations de calendrier de l’utilisateur à partir d’Office 365, cet utilisateur devra donner son consentement à l’application cliente. Une fois le consentement donné, l’application cliente sera en mesure d’appeler l’API Microsoft Graph au nom de l’utilisateur et d’utiliser les informations de calendrier en fonction des besoins. [L’API Microsoft Graph](https://graph.microsoft.io) permet d’accéder aux données d’Office 365 (comme les calendriers et les messages Exchange, les sites et les listes SharePoint, les documents OneDrive, les blocs-notes OneNote, les tâches Organiseur, les classeurs Excel, etc.), ainsi qu’aux utilisateurs et groupes d’Azure AD, et à d’autres objets de données provenant d’autres services cloud Microsoft. 

L'infrastructure de consentement est conçue sur OAuth 2.0 et ses différents flux, notamment l’octroi d’un code d’autorisation et d’informations d'identification du client, à l'aide de clients publics ou confidentiels. En utilisant OAuth 2.0, Azure AD permet de créer de nombreux types d'applications clientes, sur téléphone, tablette, serveur ou web, et d'accéder aux ressources requises.

Pour plus d’informations sur l’infrastructure de consentement, consultez l’article concernant [l’utilisation d’OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) et l’article [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md). Pour plus d’informations sur l’obtention d’un accès autorisé à Office 365 par le biais de Microsoft Graph, consultez l’article [App authentication with Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview) (Authentification d’applications avec Microsoft Graph).

#### <a name="example-of-the-consent-experience"></a>Exemple d’expérience de consentement
Les étapes suivantes vous montrent comment l’expérience de consentement fonctionne à la fois pour le développeur d'applications et pour l'utilisateur.

1. À partir de la page de configuration de votre application cliente web dans le portail Azure, définissez les autorisations demandées par votre application à l’aide des menus disponibles dans la section Autorisations requises.
   
    ![Autorisations pour d'autres applications](./media/active-directory-integrating-applications/requiredpermissions.png)
2. Considérons que les autorisations de votre application ont été mises à jour, que l'application s'exécute et qu’un utilisateur s'apprête à l’utiliser pour la première fois. Si l'application n'a pas encore acquis de jeton d’accès ou d’actualisation, l'application a besoin d’accéder au point de terminaison de l'autorisation d'Azure AD pour obtenir un code d'autorisation qui peut servir à acquérir un nouveau jeton d’accès et d’actualisation.
3. Si l'utilisateur n'est pas déjà authentifié, il sera invité à se connecter à Azure AD.
   
    ![Connexion d’un utilisateur ou d’un administrateur à Azure AD](./media/active-directory-integrating-applications/usersignin.png)
4. Une fois l'utilisateur connecté, Azure AD déterminera s’il est nécessaire d'afficher une page de consentement pour l’utilisateur. La détermination est différente selon que l’utilisateur (ou l’administrateur de son organisation), a déjà accordé ou non son consentement à l'application. Si l'autorisation n'a pas encore été accordée, Azure AD invitera l'utilisateur à fournir son consentement et affichera les autorisations dont il a besoin pour fonctionner. Le jeu d’autorisations qui s’affiche dans la boîte de dialogue de consentement est identique à la sélection effectuée dans la section Autorisations déléguées du portail Azure.
   
    ![Expérience de consentement de l'utilisateur](./media/active-directory-integrating-applications/consent.png)
5. Une fois que l'utilisateur a donné son consentement, un code d'autorisation est retourné à votre application, qui peut être utilisé pour acquérir un jeton d'accès et un jeton d'actualisation. Pour plus d’informations sur ce flux, consultez la section [Application web vers API web](active-directory-authentication-scenarios.md#web-application-to-web-api) dans [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

6. En tant qu’administrateur, vous pouvez également donner votre consentement pour les autorisations déléguées d’une application pour le compte de tous les utilisateurs de votre client. Cela évitera que la boîte de dialogue de consentement s’affiche pour chaque utilisateur du client. Vous pouvez le faire à partir de la page de votre application dans le [portail Azure](https://portal.azure.com). Dans le panneau **Paramètres** de votre application, cliquez sur **Autorisations requises**, puis sur le bouton **Accorder des autorisations**. 

    ![Accorder des autorisations pour un consentement administrateur explicite](./media/active-directory-integrating-applications/grantpermissions.png)
    
> [!NOTE]
> Pour les applications à page unique (SPA) qui utilisent ADAL.js, il est actuellement nécessaire d’accorder un consentement explicite à l’aide du bouton **Accorder des autorisations**. En effet, le jeton d’accès est requis sans invite de consentement, ce qui donnera lieu à un échec si le consentement n’est pas déjà accordé.   

### <a name="configuring-a-client-application-to-access-web-apis"></a>Configuration d’une application cliente pour accéder aux API web
Une application cliente web/confidentielle doit établir des informations d’identification sécurisées afin de pouvoir participer à un flux d’octroi d’autorisations qui requiert une authentification. La méthode d’authentification par défaut prise en charge par le portail Azure est l’ID Client + la clé symétrique. Cette section décrit les étapes de configuration requises pour fournir les informations d’identification de votre client à la clé secrète.

En outre, avant qu’un client puisse accéder à une API web exposée par une application de ressources (par exemple, l’API Microsoft Graph), l’infrastructure de consentement permet au client d’obtenir l’autorisation nécessaire en fonction des autorisations demandées. Par défaut, toutes les applications peuvent choisir des autorisations d'Azure Active Directory (API Graph) et de l’API Azure Service Management, avec l'autorisation « Activer l'authentification et lire le profil de l’utilisateur » d’Azure AD déjà sélectionnée par défaut. Si votre application cliente est en cours d’enregistrement dans un locataire Azure AD Office 365, vous aurez également la possibilité de sélectionner les API web et les autorisations pour Exchange Online et SharePoint. Vous pouvez sélectionner deux [types d’autorisations](active-directory-dev-glossary.md#permissions) dans les listes déroulantes en regard de l’API web souhaitée :

* Autorisations de l’application : votre application cliente doit accéder à l’API web directement en tant que telle (aucun contexte utilisateur). Ce type d'autorisation requiert le consentement de l'administrateur et n'est également pas disponible pour les applications clientes natives.
* Autorisations déléguées : votre application cliente doit accéder à l’API web en tant qu’utilisateur connecté, mais avec un accès limité par l’autorisation sélectionnée. Ce type d'autorisation peut être accordé par un utilisateur, à moins que l'autorisation ne soit configurée comme nécessitant le consentement de l'administrateur. 

> [!NOTE]
> L’ajout d’une autorisation déléguée à une application n’accorde pas automatiquement un consentement aux utilisateurs du client, comme c’était le cas dans le portail Azure Classic. Les utilisateurs doivent donner manuellement leur consentement pour les autorisations déléguées ajoutées, au moment de l’exécution, à moins que l’administrateur clique sur le bouton **Accorder des autorisations** dans la section **Autorisations requises** de la page de l’application dans le portail Azure. 

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>Pour ajouter des informations d’identification ou des autorisations pour accéder aux API web
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le menu supérieur, choisissez **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis sur l’application que vous souhaitez configurer. Vous accédez alors à la page Démarrage rapide de l’application, avec le panneau Paramètres de l’application ouvert.
4. Pour ajouter une clé secrète aux informations d’identification de votre application web, cliquez sur la section Clés dans le panneau Paramètres.  
   
   * Ajoutez une description pour votre clé et sélectionnez une durée de 1 ou 2 ans. 
   * La colonne située le plus à droite contient la valeur de clé, après avoir enregistré les modifications de configuration. N’oubliez pas de revenir à cette section et de la copier après l’avoir enregistrée afin de pouvoir l’utiliser dans votre application cliente lors de l’authentification au moment de l’exécution.
5. Pour ajouter des autorisations d’accès aux API de ressource à partir de votre client, cliquez sur la section Autorisations requises dans le panneau Paramètres. 
   
   * Tout d’abord, cliquez sur le bouton Ajouter.
   * Cliquez sur Sélectionner une API pour sélectionner le type de ressources que vous souhaitez choisir.
   * Parcourez la liste des API disponibles ou utilisez la zone de recherche pour sélectionner une des applications de ressources qui exposent une API web disponibles dans votre répertoire. Cliquez sur la ressource qui vous intéresse, puis sur **Sélectionner**.
   * Une fois la sélection effectuée, vous pouvez accéder au menu **Sélectionner des autorisations**, qui permet de choisir les autorisations d’application et les autorisations déléguées pour votre application.
   
6. Lorsque vous avez terminé, cliquez sur le bouton **Terminé**.

> [!NOTE]
> Lorsque vous cliquez sur le bouton **Terminé**, les autorisations sont automatiquement définies pour l’application de votre répertoire en fonction des autorisations pour les autres applications que vous avez configurées.  Vous pouvez afficher ces autorisations d’application dans le panneau **Paramètres** de l’application.
> 
> 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configuration d’une application de ressource pour exposer les API web
Vous pouvez développer une API web et la mettre à disposition des applications clientes en exposant l’[étendue](active-directory-dev-glossary.md#scopes) et les [rôles](active-directory-dev-glossary.md#roles) d’accès. Une API web correctement configurée peut être mise à disposition tout comme les autres API web Microsoft, notamment l'API Graph et les API Office 365. Les étendues d’accès sont exposées par le biais du [manifeste de votre application](active-directory-dev-glossary.md#application-manifest), c’est-à-dire un fichier JSON représentant la configuration d’identité de votre application.  

La section suivante indique comment exposer les étendues d’accès en modifiant le manifeste de l’application de la ressource.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Ajout d’étendues d’accès à votre application de ressources
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le menu supérieur, choisissez **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis sur l’application que vous souhaitez configurer. Vous accédez alors à la page Démarrage rapide de l’application, avec le panneau Paramètres de l’application ouvert.
4. Cliquez sur **Manifeste** dans la page de l’application pour ouvrir l’éditeur de manifeste en ligne. 
5. Remplacez le nœud « oauth2Permissions » par l’extrait de code JSON suivant. Cet extrait de code est un exemple montrant comment exposer une étendue appelée « emprunt d’identité », qui permet à un propriétaire de ressource d’attribuer à une application cliente un type d’accès délégué à une ressource. Veillez à modifier le texte et les valeurs selon votre propre application :
   
        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in     user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],
   
    La valeur d’ID doit être un nouveau GUID, créé à l’aide d’un [outil de génération de GUID](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) ou d’un programme. Elle représente un identificateur unique pour l'autorisation exposée par l'API web. Une fois votre client correctement configuré pour demander l'accès à votre API web et pour appeler l'API web, il présentera un jeton OAuth 2.0 JWT qui a la revendication d’étendue définie à la valeur ci-dessus, dans ce cas user_impersonation.
   
   > [!NOTE]
   > Vous pouvez exposer des étendues supplémentaires ultérieurement si nécessaire. Considérez que votre API web peut exposer plusieurs étendues associées à un éventail de fonctions différentes. Vous pouvez maintenant contrôler l'accès à l'API web à l'aide de la revendication d'étendue dans le jeton OAuth 2.0 JWT reçu.
   > 
   > 
6. Cliquez sur **Enregistrer** pour enregistrer le manifeste. Votre API web est maintenant configurée pour être utilisée par les autres applications de votre répertoire.

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>Pour vérifier que l’API web est exposée à d’autres applications dans votre répertoire
1. Dans le menu supérieur, cliquez sur **Inscriptions des applications**, sélectionnez l’application cliente dont vous souhaitez configurer l’accès à l’API web, puis accédez au panneau Paramètres.
2. Dans la section **Autorisations requises**, sélectionnez l’API web pour laquelle vous venez d’exposer une autorisation. Dans la liste déroulante Autorisations déléguées, sélectionnez la nouvelle autorisation.

![Les autorisations de la liste des tâches sont affichées.](./media/active-directory-integrating-applications/todolistpermissions.png)

#### <a name="more-on-the-application-manifest"></a>Informations complémentaires concernant le manifeste d’application
Le manifeste d’application sert de mécanisme de mise à jour de l’entité Application, qui définit tous les attributs de configuration d’identité d’une application Azure AD, y compris les étendues d’accès d’API dont il a été question ci-dessus. Pour plus d’informations sur l’entité Application, consultez la [documentation relative à l’entité Application de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Vous y trouverez des informations de référence complètes sur les membres de l’entité Application permettant de spécifier des autorisations pour votre API :  

* le membre appRoles, qui est une collection d’entités [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) pouvant être utilisée pour définir les **autorisations d’application** pour une API web ;  
* le membre oauth2Permissions, qui est une collection d’entités [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) pouvant être utilisée pour définir les **autorisations déléguées** pour une API web.

Pour des informations plus générales sur les concepts de manifeste d’application, consultez la page [Connaître le manifeste d’application Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Accès à Azure AD Graph et à Office 365 par le biais des API Microsoft Graph  
Comme mentionné précédemment, vous pouvez non seulement exposer des API et y accéder dans vos propres applications de ressources, mais également mettre à jour votre application cliente pour accéder aux API exposées par les ressources Microsoft.  L’API Microsoft Graph, appelée « Microsoft Graph » dans la liste des autorisations pour d’autres applications, est disponible pour toutes les applications inscrites auprès d’Azure AD. Si vous inscrivez votre application cliente dans un client Azure AD qui a été approvisionné par Office 365, vous pouvez également accéder à toutes les autorisations exposées par l’API Microsoft Graph sur diverses ressources Office 365.

Pour découvrir une description complète des étendues d’accès exposées par l’API Microsoft Graph, consultez l’article [Permission scopes | Microsoft Graph API concepts](https://graph.microsoft.io/docs/authorization/permission_scopes) (Étendues des autorisations | Concepts d’API Microsoft Graph).

> [!NOTE]
> En raison d'une limitation actuelle, les applications clientes natives ne peuvent appeler l'API Graph Azure AD que si elles utilisent l'autorisation « Accéder au répertoire de votre organisation ».  Cette restriction ne s'applique pas aux applications web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Configuration d'applications multilocataires
Lorsque vous ajoutez une application à Azure AD, vous pouvez choisir de rendre votre application accessible uniquement aux utilisateurs de votre organisation. Vous pouvez également rendre votre application accessible aux utilisateurs d'organisations externes. Ces deux types d’applications sont appelés applications à locataire unique et applications multilocataires. Vous pouvez modifier la configuration d'une application à locataire unique pour en faire une application multilocataire, ce que cette section décrit ci-dessous.

Il est important de noter les différences entre une application à locataire unique et une application multilocataire :  

* Une application à locataire unique est prévue pour une utilisation dans une seule organisation. Il s’agit généralement d’une application métier écrite par un développeur de l’entreprise. Une application à locataire unique doit être accessible uniquement aux utilisateurs d’un annuaire et, en conséquence, ne doit être approvisionnée que dans un seul annuaire.
* Une application multilocataire est prévue pour une utilisation dans plusieurs organisations. Il s’agit d’une application SaaS (software-as-a-service) web généralement écrite par un éditeur de logiciels indépendant. Les applications multilocataires doivent être approvisionnées dans chaque annuaire dans lequel elles sont utilisées, ce qui suppose le consentement d’un utilisateur ou d’un administrateur pour les inscrire via l’infrastructure de consentement Azure AD. Notez que toutes les applications clientes natives sont multilocataires par défaut lorsqu’elles sont installées sur l’appareil du propriétaire de la ressource. Pour plus d’informations sur l’infrastructure de consentement, consultez la section Vue d’ensemble de l’infrastructure de consentement ci-dessus.

#### <a name="enabling-external-users-to-grant-your-application-access-to-their-resources"></a>Permettre aux utilisateurs externes d’accorder à votre application l’accès à leurs ressources
Si vous écrivez une application que vous souhaitez proposer à vos clients ou à des partenaires externes à votre organisation, vous devez mettre à jour la définition de l’application dans le portail Azure.

> [!NOTE]
> Lorsque vous rendez une application multilocataire, vous devez vous assurer que l’URI ID de votre application appartient à un domaine vérifié. En outre, l'URL de renvoi doit commencer par https://. Pour plus d’informations, voir [Objets principal du service et application](active-directory-application-objects.md).
> 
> 

Pour autoriser les utilisateurs externes à accéder à votre application : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le menu supérieur, choisissez **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis sur l’application que vous souhaitez configurer. Vous accédez alors à la page Démarrage rapide de l’application, avec le panneau Paramètres de l’application ouvert.
4. Dans le panneau Paramètres, cliquez sur **Propriétés** et basculez le commutateur **Mutualisé** sur **Oui**.

Une fois que vous avez apporté les modifications ci-dessus, les utilisateurs et les administrateurs d’autres organisations pourront accorder à votre application l'accès à leur répertoire et à d'autres données.

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>Déclenchement de l’infrastructure de consentement Azure AD lors de l’exécution
Pour utiliser l’infrastructure de consentement, les applications clientes multilocataires doivent en demander l’autorisation avec OAuth 2.0. Des [exemples de code](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) sont disponibles pour vous montrer comment une application web, une application native ou une application serveur/démon demande des codes d’autorisation et des jetons d’accès pour appeler des API web.

Votre application web peut peut-être offrir également une expérience d’inscription aux utilisateurs. Si vous offrez une expérience d’inscription, l’utilisateur devra cliquer sur un bouton d’inscription qui redirigera le navigateur vers le point de terminaison d’autorisation AD Azure OAuth2.0 ou un point de terminaison userinfo OpenID Connect. Ces points de terminaison permettent à l'application d’obtenir des informations sur le nouvel utilisateur en inspectant l'id_token. Après la phase d’inscription, l’utilisateur reçoit une invite de consentement similaire à celle illustrée ci-dessus dans la section Vue d’ensemble de l’infrastructure de consentement.

Sinon, votre application web peut également offrir une expérience permettant aux administrateurs d’« inscrire ma société ». Cette expérience redirige également l'utilisateur vers le point de terminaison d’autorisation AD OAuth Azure 2.0. Dans ce cas, vous pouvez transmettre un paramètre prompt=admin_consent pour autoriser le point de terminaison à forcer l’expérience de consentement de l’administrateur, où l’administrateur accorde son consentement pour le compte de son organisation. Seul l’utilisateur qui s’authentifie avec un compte appartenant au rôle Administrateur général peut fournir son consentement ; les autres utilisateurs recevront un message d’erreur. En cas de consentement réussi, la réponse contiendra admin_consent=true. Lors de l’échange d’un jeton d'accès, vous recevrez également un id_token qui fournira des informations sur l'organisation et sur l'administrateur qui s’est inscrit à votre application.

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Activation de l’accord implicite OAuth 2.0 pour les applications à page unique
Les applications à page unique (SPA) se composent généralement d’une partie frontale reposant largement sur JavaScript qui s’exécute dans le navigateur. Celle-ci appelle l’API web principale de l’application pour effectuer sa logique métier. Pour les applications à page unique hébergées dans Azure AD, l’accord implicite OAuth 2.0 permet d’authentifier l’utilisateur auprès d’Azure AD et d’obtenir un jeton qui peut servir à sécuriser les appels du client JavaScript de l’application à son API web principale. Une fois que l’utilisateur a donné son consentement, ce même protocole d’authentification peut servir à obtenir des jetons pour sécuriser les appels entre le client et d’autres ressources API web configurées pour l’application. Pour plus d’informations sur l’octroi d’autorisation implicite et pour vous aider à décider si cette méthode est adaptée à votre scénario d’application, consultez [Comprendre le flux d’octroi implicite OAuth2 dans Azure Active Directory (AD)](active-directory-dev-understanding-oauth2-implicit-grant.md).

Par défaut, l’accord implicite OAuth 2.0 est désactivé pour les applications. Vous pouvez activer un accord implicite OAuth 2.0 pour votre application en définissant la valeur `oauth2AllowImplicitFlow` dans son [manifeste d’application](active-directory-application-manifest.md) (fichier JSON représentant la configuration d’identité de votre application).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Pour activer l’accord implicite OAuth 2.0
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le menu supérieur, choisissez **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis sur l’application que vous souhaitez configurer. Vous accédez alors à la page Démarrage rapide de l’application, avec le panneau Paramètres de l’application ouvert.
4. Dans la page de l’application, cliquez sur **Manifeste** pour ouvrir l’éditeur de manifeste en ligne.
   Recherchez la valeur « oauth2AllowImplicitFlow » et définissez-la sur « true ». La valeur par défaut est « false ».
   
    `"oauth2AllowImplicitFlow": true,`
5. Enregistrez le manifeste mis à jour. Une fois l’enregistrement effectué, votre API web est configurée pour utiliser l’accord implicite OAuth 2.0 pour authentifier les utilisateurs.


## <a name="removing-an-application"></a>Suppression d'une application
Cette section décrit comment supprimer une application de votre locataire Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Suppression d’une application créée par votre organisation
Il s’agit des applications qui s’affichent sous le filtre « Applications que ma société possède » sur la page principale « Applications » de votre locataire Azure AD. En termes techniques, il s’agit des applications que vous avez inscrites soit manuellement au moyen du portail Azure Classic, soit par un programme par le biais de PowerShell ou de l’API Graph. Plus précisément, ces applications sont représentées dans votre locataire par des objets Application et Principal du service. Pour plus d’informations, consultez [Objets principal du service et application](active-directory-application-objects.md) .

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Pour supprimer une application à locataire unique de votre répertoire
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le menu supérieur, choisissez **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis sur l’application que vous souhaitez configurer. Vous accédez alors à la page Démarrage rapide de l’application, avec le panneau Paramètres de l’application ouvert.
4. Dans la page de l’application, cliquez sur **Supprimer**.
5. Cliquez sur **Oui** dans le message de confirmation.

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>Pour supprimer une application multilocataire de votre répertoire
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le menu supérieur, choisissez **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis sur l’application que vous souhaitez configurer. Vous accédez alors à la page Démarrage rapide de l’application, avec le panneau Paramètres de l’application ouvert.
4. Dans le panneau Paramètres, cliquez sur **Propriétés** et basculez le commutateur **Mutualisé** sur **Non**. Cela convertit votre application en une application à locataire unique, mais l’application reste toujours dans les organisations qui ont déjà donné leur consentement.
5. Dans la page de l’application, cliquez sur le bouton **Supprimer**.
6. Cliquez sur **Oui** dans le message de confirmation.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Suppression d’une application multilocataire autorisée par une autre organisation
Il s’agit d’un sous-ensemble des applications qui s’affichent sous le filtre « Applications que ma société utilise » sur la page principale « Applications » de votre locataire Azure AD, et en particulier celles qui ne figurent pas dans la liste « Applications que ma société possède ». En termes techniques, il s’agit d’applications multilocataires enregistrées pendant le processus de consentement. Ces applications sont représentées dans votre locataire par un objet Principal du service. Pour plus d’informations, consultez [Objets principal du service et application](active-directory-application-objects.md) .

Afin de pouvoir supprimer l’accès d’une application multilocataires à votre répertoire (après avoir donné son consentement), l’administrateur de l’entreprise doit posséder un abonnement Azure pour supprimer l’accès via le portail Azure. L’administrateur de la société peut également utiliser la [Gestion d’Azure AD à l’aide de Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151) pour supprimer l’accès.

## <a name="next-steps"></a>Étapes suivantes
* Consultez les [instructions de personnalisation pour applications intégrées](active-directory-branding-guidelines.md) afin d’obtenir des conseils sur l’aide visuelle pour votre application.
* Consultez la rubrique [Objets principal du service et application](active-directory-application-objects.md) pour plus d’informations sur la relation existant entre les objets principal du service et application de l’application.
* Pour en savoir plus sur le rôle joué par le manifeste d’application, consultez [Connaître le manifeste d’application Azure Active Directory](active-directory-application-manifest.md).
* Consultez le [glossaire du développeur Azure AD](active-directory-dev-glossary.md) pour connaître les définitions de certains des principaux concepts de développement Azure Active Directory (AD).
* Consultez le [guide du développeur Active Directory](active-directory-developers-guide.md) pour avoir une vue d’ensemble du contenu associé au développement.


