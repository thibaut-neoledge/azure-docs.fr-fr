<properties
   pageTitle="Création d'une application et d'un principal du service AD dans le portail | Microsoft Azure"
   description="Décrit comment créer une application et un principal du service Active Directory qui peuvent être utilisés avec le contrôle d'accès basé sur les rôles dans Azure Resource Manager pour gérer l'accès aux ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/20/2016"
   ms.author="tomfitz"/>

# Création de l'application Active Directory et du principal du service à l'aide du portail

## Vue d'ensemble
Lorsqu’un processus automatisé ou une application doit accéder à des ressources ou les modifier, vous pouvez utiliser le portail Azure Classic pour créer une application Active Directory. Lorsque vous créez une application Active Directory par le biais du portail Azure Classic, celui-ci crée l’application et un principal du service. Vous pouvez exécuter l’application sous sa propre identité ou sous l’identité de l’utilisateur connecté à votre application. Ces deux méthodes d’authentification des applications désignent ce que l’on appelle des méthodes interactives (connexion de l’utilisateur) et non interactives (l’application fournit ses propres informations d’identification). En mode non interactif, vous devez attribuer au principal du service un rôle associé à l’autorisation appropriée.

Cette rubrique montre comment créer une application et un principal du service à l’aide du portail Azure Classic. Actuellement, vous devez utiliser le portail Azure Classic pour créer une application Active Directory. Cette possibilité sera ajoutée au portail Azure dans une version ultérieure. Vous pouvez utiliser le portail pour assigner l’application à un rôle. Vous pouvez également effectuer ces étapes via Azure PowerShell ou l’interface de ligne de commande Azure. Pour plus d’informations sur l’utilisation de PowerShell ou de l’interface de ligne de commande avec le principal du service, consultez [Authentification d’un principal du service à l’aide d’Azure Resource Manager](resource-group-authenticate-service-principal.md).

## Concepts
1. Azure Active Directory (AAD) - une build de service de gestion des identités et des accès pour le cloud. Pour plus d’informations, consultez la rubrique [Qu’est-ce qu’Azure Active Directory ?](active-directory/active-directory-whatis.md)
2. Principal du service : une instance d’application dans un annuaire.
3. Application Active Directory : un enregistrement d’annuaire dans AAD qui identifie une application à AAD. 

Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](active-directory/active-directory-application-objects.md). Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Créer une application

Pour les applications interactives et non interactives, vous devez créer et configurer votre application Active Directory.

1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/).

2. Sélectionnez **Active Directory** dans le volet gauche.

     ![sélectionner Active Directory][1]

3. Sélectionnez l’annuaire que vous souhaitez utiliser pour la création de la nouvelle application.

     ![choisir l’annuaire][2]

3. Pour afficher les applications dans votre annuaire, cliquez sur **Applications**.

     ![afficher les applications][11]

4. Si vous n’avez pas préalablement créé d’application dans cet annuaire, vous devez voir quelque chose de similaire à l’image suivante. Cliquez sur **AJOUTER UNE APPLICATION**

     ![ajouter l’application][6]

     Ou, cliquez sur **Ajouter** dans le volet inférieur.

     ![ajouter][12]

5. Sélectionnez le type d’application que vous souhaitez créer. Pour ce tutoriel, nous n’utiliserons pas d’application de la galerie.

     ![nouvelle application][10]

6. Renseignez le nom de l’application et sélectionnez le type d’application que vous souhaitez utiliser. Sélectionnez le type d’application que vous créez. Pour ce didacticiel, nous allons créer une **APPLICATION WEB ET/OU UNE API WEB** et cliquer sur le bouton Suivant.

     ![nommer l’application][9]

7. Renseignez les propriétés de votre application. Pour **URL de connexion**, indiquer l’URI vers un site web qui décrit votre application. L’existence du site web n’est pas validée. Pour **URI ID d’application**, indiquez l’URI qui identifie votre application. Le caractère unique ou l’existence du point de terminaison n’est pas validé. Si vous avez sélectionné **Application cliente native** comme type d’application, vous devez renseigner la valeur de l’**URI de redirection**. Cliquez sur **Terminé** pour créer votre application AAD.

     ![propriétés de l’application][4]

Vous avez créé votre application.

## Obtenir l’ID client et l’ID de locataire

Lorsque vous accédez à votre application par le biais d’un programme, vous avez besoin de l’ID de votre application. Sélectionnez l’onglet **Configurer** et copiez l’**ID CLIENT**.
  
   ![ID de client][5]

Dans certains cas, vous devez transmettre l'ID de client avec votre demande d'authentification. Vous pouvez récupérer l’ID de locataire en sélectionnant **Afficher les points de terminaison** au bas de l’écran et en extrayant l’ID comme indiqué ci-dessous.

   ![ID client](./media/resource-group-create-service-principal-portal/save-tenant.png)

## Créer une clé d’authentification

Si votre application est appelée à s’exécuter avec ses propres informations d’identification, vous devez créer une clé pour l’application.

1. Cliquez sur l’onglet **Configurer** pour configurer le mot de passe de votre application.

     ![configurer l’application][3]

2. Faites défiler jusqu’à la section **Clés** et sélectionnez la durée de validité de votre mot de passe.

     ![clés][7]

3. Sélectionnez **Enregistrer** pour créer votre clé.

     ![enregistrer][13]

     La clé enregistrée s’affiche. Vous pouvez la copier. Copiez la clé dès à présent, car par la suite, vous ne pourrez plus la récupérer.

     ![clé enregistrée][8]

Votre application est maintenant prête et le principal du service est créé sur votre client. Lorsque vous vous connectez en tant que principal du service, veillez à utiliser :

* **ID CLIENT** comme nom d’utilisateur.
* **CLÉ** comme mot de passe.

## Définir des autorisations déléguées

Si votre application accède aux ressources pour le compte d’un utilisateur connecté, vous devez accorder à votre application l’autorisation déléguée d’accéder aux autres applications. Pour cela, vous devez vous rendre dans la section **Autorisations pour d’autres applications** de l’onglet **Configurer**. Par défaut, une autorisation déléguée est déjà activée pour Azure Active Directory. Ne modifiez pas cette autorisation déléguée.

1. Sélectionnez **Ajouter une application**.

2. Dans la liste, sélectionnez l’**API de gestion des services Windows Azure**.

      ![sélectionner une application](./media/resource-group-create-service-principal-portal/select-app.png)

3. Ajoutez l’autorisation déléguée **Accéder à la gestion des services Azure (en version préliminaire)** à l’API de gestion des services.

       ![modifier une autorisation](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Enregistrez la modification.

## Configurer une application mutualisée

Si les utilisateurs d’autres annuaires Azure Active Directory peuvent donner leur consentement à l’application et se connecter, vous devez activer une architecture mutualisée. Sous l’onglet **Configurer**, définissez **Application mutualisée** sur **Oui**.

![architecture mutualisée](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Affecter l’application à un rôle

Si votre application n’est pas exécutée sous l’identité d’un utilisateur connecté, vous devez affecter l’application à un rôle pour lui accorder l’autorisation d’effectuer des actions. Pour affecter l’application à un rôle, passez du portail Azure Classic au [portail Azure](https://portal.azure.com). Vous devez décider quel rôle ajouter à l’application et avec quelle étendue. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md). Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Dans le portail, accédez au niveau d’étendue que vous souhaitez affecter à l’application. Dans le cadre de cette rubrique, accédez à un groupe de ressources, puis dans le panneau du groupe de ressources, sélectionnez l’icône **Accès**.

     ![sélectionner les utilisateurs](./media/resource-group-create-service-principal-portal/select-users.png)

2. Sélectionnez **Ajouter**.

     ![sélectionner ajouter](./media/resource-group-create-service-principal-portal/select-add.png)

3. Sélectionnez le rôle **Lecteur** (ou un rôle auquel vous souhaitez affecter l’application).

     ![sélectionner un rôle](./media/resource-group-create-service-principal-portal/select-role.png)

4. Lorsque vous voyez pour la première fois la liste des utilisateurs que vous pouvez ajouter au rôle, aucune application n’est indiquée. Vous voyez uniquement des utilisateurs et un groupe.

     ![afficher les utilisateurs](./media/resource-group-create-service-principal-portal/show-users.png)

5. Pour trouver votre application, vous devez la rechercher. Commencez à taper le nom de votre application. La liste des options disponibles change. Sélectionnez votre application dans la liste.

     ![affecter au rôle](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Sélectionnez **OK** pour finaliser l’affectation du rôle. Vous devez maintenant voir votre application dans la liste des utilisateurs affectés à un rôle pour le groupe de ressources.

     ![afficher](./media/resource-group-create-service-principal-portal/show-app.png)

Pour plus d’informations sur l’attribution des utilisateurs, des applications et des rôles au moyen du portail, consultez la rubrique [Gérer l’accès à l’aide du portail de gestion Azure](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal).

## Obtention d'un jeton d'accès dans le code

Si vous utilisez .NET, vous pouvez récupérer le jeton d'accès de votre application avec le code suivant.

Tout d'abord, vous devez installer la bibliothèque d'authentification Active Directory dans votre projet Visual Studio. Le plus simple consiste à utiliser le package NuGet. Ouvrez la Console du Gestionnaire de package et entrez les commandes suivantes.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

Pour vous connecter avec l’ID et la clé secrète de votre application, utilisez la méthode suivante pour récupérer le jeton.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Pour vous connecter pour le compte de l’utilisateur, utilisez la méthode suivante pour récupérer le jeton.

    public static string GetAcessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", {application id}, new Uri({redirect uri});

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Vous pouvez utiliser le code suivant pour transmettre le jeton dans l’en-tête de demande :

    string token = GetAcessToken();
    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);


## Étapes suivantes

- Pour en savoir plus sur la spécification de stratégies de sécurité, consultez la rubrique [Contrôle d’accès en fonction du rôle](./active-directory/role-based-access-control-configure.md).  
- Pour une démonstration vidéo de ces étapes, consultez la rubrique [Activation de la gestion par programme d’une ressource Azure avec Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Pour en savoir plus sur l’utilisation d’Azure PowerShell ou de l’interface de ligne de commande Azure pour utiliser les applications et les principaux du service Active Directory, notamment l’utilisation d’un certificat pour l’authentification, consultez la rubrique [Authentification d’un principal du service à l’aide d’Azure Resource Manager](./resource-group-authenticate-service-principal.md).
- Pour obtenir des instructions sur l'implémentation de la sécurité avec Azure Resource Manager, consultez la rubrique [Questions de sécurité relatives à Azure Resource Manager](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=AcomDC_0128_2016-->