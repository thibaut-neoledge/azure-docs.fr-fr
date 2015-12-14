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
   ms.date="12/01/2015"
   ms.author="tomfitz"/>

# Création de l'application Active Directory et du principal du service à l'aide du portail

## Vue d'ensemble
Lorsque vous avez un processus automatisé ou une application qui doit accéder ou modifier une ressource dans votre abonnement, vous pouvez utiliser le portail pour créer une application Active Directory et l'assigner à un rôle avec les autorisations correctes. Lorsque vous créez une application Active Directory par le biais du portail, celui-ci crée l'application et un principal du service. Vous utilisez le principal du service lors de la définition des autorisations.

Cette rubrique montre comment créer une application et un principal du service à l'aide du portail Azure. Actuellement, vous devez utiliser le portail Microsoft Azure pour créer une application Active Directory. Cette possibilité sera ajoutée à la version préliminaire du portail Azure dans une version ultérieure. Vous pouvez utiliser le portail en version préliminaire pour assigner l'application à un rôle. Vous pouvez également effectuer ces étapes via Azure PowerShell ou l’interface de ligne de commande Azure. Pour en savoir plus, consultez la rubrique [Authentification d'un principal du service à l'aide d'Azure Resource Manager](resource-group-authenticate-service-principal.md).

## Concepts
1. Azure Active Directory (AAD) - une build de service de gestion des identités et des accès pour le cloud. Pour plus d’informations, consultez la rubrique [Qu’est-ce qu’Azure Active Directory ?](active-directory/active-directory-whatis.md)
2. Principal du service : une instance d’application dans un annuaire.
3. Application Active Directory : un enregistrement d’annuaire dans AAD qui identifie une application à AAD. 

Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](active-directory/active-directory-application-objects.md). Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Création des objets principal du service et application

1. Connectez-vous à votre compte Azure par le biais du [portail](https://manage.windowsazure.com/).

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

6. Renseignez le nom de l’application et sélectionnez le type d’application que vous souhaitez utiliser. Étant donné que nous avons l’intention d’utiliser le principal du service de cette application pour s’authentifier auprès d’Azure Resource Manager, nous choisirons de créer une **APPLICATION WEB ET/OU une API WEB** et cliquerons sur le bouton Suivant.

     ![nommer l’application][9]

7. Renseignez les propriétés de votre application. Pour **URL de connexion**, indiquer l’URI vers un site web qui décrit votre application. L’existence du site web n’est pas validée. Pour **URI ID d’application**, indiquez l’URI qui identifie votre application. Le caractère unique ou l’existence du point de terminaison n’est pas validé. Cliquez sur **Terminé** pour créer votre application AAD.

     ![propriétés de l’application][4]

## Création d'une clé d'authentification pour votre application
L’application doit à présent être sélectionnée dans le portail.

1. Cliquez sur l’onglet **Configurer** pour configurer le mot de passe de votre application.

     ![configurer l’application][3]

2. Faites défiler jusqu’à la section **Clés** et sélectionnez la durée de validité de votre mot de passe.

     ![clés][7]

3. Sélectionnez **Enregistrer** pour créer votre clé.

     ![enregistrer][13]

     La clé enregistrée s’affiche. Vous pouvez la copier. Copiez la clé dès à présent, car par la suite, vous ne pourrez plus la récupérer.

     ![clé enregistrée][8]

4. Vous pouvez maintenant utiliser votre clé pour vous authentifier en tant que principal du service. Vous aurez besoin votre **ID CLIENT** en plus de votre **CLÉ** pour vous connecter. Accédez à **ID CLIENT** et copiez-le.
  
     ![ID de client][5]

5. Dans certains cas, vous devez transmettre l'ID de client avec votre demande d'authentification. Vous pouvez récupérer l'ID de client en sélectionnant **Afficher les points de terminaison** et en extrayant l'ID comme indiqué ci-dessous.

     ![ID client](./media/resource-group-create-service-principal-portal/save-tenant.png)

Votre application est maintenant prête et le principal du service est créé sur votre client. Lorsque vous vous connectez en tant que principal du service, veillez à utiliser :

* **ID CLIENT** comme nom d’utilisateur.
* **CLÉ** comme mot de passe.

## Affectation de l'application à un rôle

Vous devez affecter l'application à un rôle pour lui accorder des autorisations lui permettant de réaliser des opérations. Vous pouvez utiliser le [portail en version préliminaire](https://portal.azure.com) pour affecter l'application Active Directory à un rôle avec des autorisations appropriées.

Pour vous familiariser avec le contrôle d'accès dans la version préliminaire du portail, sélectionnez l’icône **Accès**.

![sélectionner les utilisateurs](./media/resource-group-create-service-principal-portal/select-users.png)

Sélectionnez le rôle auquel vous souhaitez affecter l'application et recherchez l'application.

![sélectionner les utilisateurs](./media/resource-group-create-service-principal-portal/assign-to-role.png)

Pour plus d’informations sur l’attribution des utilisateurs, des applications et des rôles via le portail, consultez la rubrique [Gérer l’accès à l’aide du portail de gestion Azure](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal).

## Obtention d'un jeton d'accès dans le code

Si vous utilisez .NET, vous pouvez récupérer le jeton d'accès de votre application avec le code suivant.

Tout d'abord, vous devez installer la bibliothèque d'authentification Active Directory dans votre projet Visual Studio. Le plus simple consiste à utiliser le package NuGet. Ouvrez la Console du Gestionnaire de package et entrez les commandes suivantes.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

Dans votre application, ajoutez une méthode comme suit pour récupérer le jeton.

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

## Étapes suivantes

- Pour en savoir plus sur la spécification de stratégies de sécurité, consultez la rubrique [Gestion et audit d'accès aux ressources](resource-group-rbac.md).  
- Pour une démonstration vidéo de ces étapes, consultez la rubrique [Activation de la gestion par programme d'une ressource Azure avec Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Pour en savoir plus sur l'utilisation d'Azure PowerShell ou l’interface de ligne de commande Azure pour utiliser les applications et les principaux du service Active Directory, notamment l'utilisation d'un certificat pour l'authentification, consultez la rubrique [Authentification d'un principal du service à l'aide d'Azure Resource Manager](./resource-group-authenticate-service-principal.md).
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

<!---HONumber=AcomDC_1203_2015-->