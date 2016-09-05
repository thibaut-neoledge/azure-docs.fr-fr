<properties
   pageTitle="Créer un principal du service dans le portail | Microsoft Azure"
   description="Décrit comment créer une application et un principal du service Active Directory qui peuvent être utilisés avec le contrôle d'accès basé sur les rôles dans Azure Resource Manager pour gérer l'accès aux ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="tomfitz"/>

# Utiliser le portail pour créer une application et un principal du service Active Directory pouvant accéder aux ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interface de ligne de commande Azure](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)


Si une application doit accéder à des ressources ou les modifier, vous devez configurer une application Active Directory (AD) et lui accorder les autorisations nécessaires. Cette rubrique explique comment effectuer ces étapes via le portail. Actuellement, vous devez utiliser le portail Azure Classic pour créer une application Active Directory, puis revenir au portail Azure pour attribuer un rôle à l’application.

> [AZURE.NOTE] Vous pouvez configurer plus facilement votre application AD et le principal du service via [PowerShell](resource-group-authenticate-service-principal.md) ou [Azure CLI](resource-group-authenticate-service-principal-cli.md), en particulier si vous souhaitez utiliser un certificat pour l’authentification. Cette rubrique n’explique pas comment utiliser un certificat.

Pour obtenir une explication des concepts Active Directory, consultez [Objets principal du service et application](./active-directory/active-directory-application-objects.md). Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Pour obtenir des instructions détaillées sur l’intégration d’une application à Azure pour la gestion des ressources, consultez [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).

## Créer une application Active Directory

1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/).

2. Assurez-vous de connaître le répertoire Active Directory par défaut de votre abonnement. Vous pouvez uniquement attribuer l’accès aux applications qui se trouvent dans le même répertoire que votre abonnement. Sélectionnez **Paramètres** et recherchez le nom du répertoire associé à votre abonnement. Pour plus d’informations, consultez la page [Association des abonnements Azure avec Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).
   
     ![rechercher le répertoire par défaut](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. Sélectionnez **Active Directory** dans le volet gauche.

     ![sélectionner Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Sélectionnez l’annuaire Active Directory que vous voulez utiliser pour la création de l’application. Si vous avez plusieurs annuaires Active Directory, créez l’application dans l’annuaire par défaut de votre abonnement.

     ![choisir l’annuaire](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. Pour afficher les applications dans votre annuaire, sélectionnez **Applications**.

     ![afficher les applications](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Si vous n’avez pas préalablement créé d’application dans cet annuaire, vous devez voir quelque chose de similaire à l’image suivante. Sélectionnez **AJOUTER UNE APPLICATION**.

     ![ajouter l’application](./media/resource-group-create-service-principal-portal/create-application.png)

     Ou, cliquez sur **Ajouter** dans le volet inférieur.

     ![ajouter](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Sélectionnez le type d’application que vous souhaitez créer. Pour ce didacticiel, sélectionnez **Ajouter une application développée par mon organisation**.

     ![nouvelle application](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Renseignez le nom de l’application et sélectionnez le type d’application que vous voulez créer. Pour ce didacticiel, créez une **APPLICATION WEB ET/OU API WEB**, puis cliquez sur le bouton Suivant. Si vous sélectionnez **APPLICATION CLIENTE NATIVE**, les étapes suivantes de cet article ne correspondront pas à votre cas.

     ![nommer l’application](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Renseignez les propriétés de votre application. Pour **URL DE CONNEXION**, indiquez l’URI d’un site web qui décrit votre application. L’existence du site web n’est pas validée. Pour **URI ID d’application**, indiquez l’URI qui identifie votre application.

     ![propriétés de l’application](./media/resource-group-create-service-principal-portal/app-properties.png)

Vous avez créé votre application.

## Obtenir un ID client et une clé d’authentification

Lors d’une connexion par programmation, vous aurez besoin de l’ID de votre application. Si l’application s’exécute sous ses propres informations d’identification, vous avez également besoin d’une clé d’authentification.

1. Sélectionnez l’onglet **Configurer** pour configurer le mot de passe de votre application.

     ![configurer l’application](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copiez l’**ID CLIENT**.
  
     ![ID de client](./media/resource-group-create-service-principal-portal/client-id.png)

3. Si l’application s’exécute sous ses propres informations d’identification, faites défiler jusqu’à la section **Clés** et sélectionnez la durée de validité de votre mot de passe.

     ![clés](./media/resource-group-create-service-principal-portal/create-key.png)

4. Sélectionnez **Enregistrer** pour créer votre clé.

     ![enregistrer](./media/resource-group-create-service-principal-portal/save-icon.png)

     La clé enregistrée s’affiche. Vous pouvez la copier. Vous ne pourrez pas récupérer la clé ultérieurement. Nous vous conseillons donc de la copier maintenant.

     ![clé enregistrée](./media/resource-group-create-service-principal-portal/save-key.png)

## Obtenir l’ID de locataire

Lors d’une connexion par programmation, vous devez transmettre l’ID de locataire avec votre demande d’authentification. Pour les applications web et les applications API, vous pouvez récupérer l’ID de locataire en sélectionnant **Afficher les points de terminaison** au bas de l’écran et en récupérant l’ID comme indiqué dans l’image ci-dessous.

   ![ID client](./media/resource-group-create-service-principal-portal/save-tenant.png)

Vous pouvez également récupérer l’ID de locataire à l’aide de PowerShell :

    Get-AzureRmSubscription

Ou d’Azure CLI :

    azure account show --json

## Définir des autorisations déléguées

Si votre application accède aux ressources pour le compte d’un utilisateur connecté, vous devez accorder à votre application l’autorisation déléguée d’accéder aux autres applications. Pour cela, vous devez vous rendre dans la section **Autorisations pour d’autres applications** de l’onglet **Configurer**. Par défaut, une autorisation déléguée est déjà activée pour Azure Active Directory. Ne modifiez pas cette autorisation déléguée.

1. Sélectionnez **Ajouter une application**.

2. Dans la liste, sélectionnez l’**API de gestion des services Azure**. Cliquez sur l’icône Terminer.

      ![sélectionner une application](./media/resource-group-create-service-principal-portal/select-app.png)

3. Dans la liste déroulante Autorisations déléguées, sélectionnez **Access Azure Service Management as organization** (Accéder à la gestion des services Azure en tant qu’organisation).

      ![modifier une autorisation](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Enregistrez la modification.

## Affecter l’application à un rôle

Si votre application s’exécute sous ses propres informations d’identification, vous devez l’assigner à un rôle. Vous devez décider du rôle qui doit représenter les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md).

Pour attribuer un rôle à une application, vous devez disposer des autorisations appropriées. Plus précisément, vous devez disposer d’un accès `Microsoft.Authorization/*/Write`, qui est accordé via le rôle [Propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator). Le rôle de contributeur n’a pas l’accès approprié.

Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Pour affecter l’application à un rôle, passez du portail Azure Classic au [portail Azure](https://portal.azure.com).

1. Vérifiez vos autorisations pour vous assurer que vous pouvez affecter le principal du service à un rôle. Sélectionnez **Mes autorisations** pour votre compte.

    ![sélectionner mes autorisations](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. Affichez les autorisations affectées de votre compte. Comme mentionné précédemment, vous devez appartenir aux rôles Propriétaire ou Administrateur de l’accès utilisateur, ou avoir un rôle personnalisé vous accordant l’accès en écriture à Microsoft.Authorization. L’image suivante montre un compte qui est affecté au rôle Contributeur de l’abonnement, ce qui n’est pas une autorisation adéquate pour affecter une application à un rôle.

    ![afficher mes autorisations](./media/resource-group-create-service-principal-portal/show-permissions.png)

     Si vous ne disposez pas des autorisations appropriées pour accorder l’accès à une application, vous devez demander que votre administrateur d’abonnement vous ajoute au rôle Administrateur de l’accès utilisateur, ou bien demander qu’un administrateur accorde l’accès à l’application.

1. Accédez au niveau d’étendue que vous souhaitez affecter à l’application. Dans le cadre de cette rubrique, accédez à un groupe de ressources, puis dans le panneau du groupe de ressources, sélectionnez **Contrôle d’accès**.

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


Pour plus d’informations sur l’affectation des utilisateurs et des applications aux rôles au moyen du portail, consultez la rubrique [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## Exemples d'applications

Les exemples d’applications suivants montrent comment ouvrir une session en tant que principal du service.

**.NET**

- [Deploy an SSH Enabled VM with a Template with .NET (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Manage Azure resources and resource groups with .NET (Gérer des ressources et des groupes de ressources Azure avec .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Getting Started with Resources - Deploy Using Azure Resource Manager Template - in Java (Découverte des ressources - Déployer à l’aide du modèle Azure Resource Manager dans Java)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Getting Started with Resources - Manage Resource Group - in Java (Découverte des ressources - Gérer un groupe de ressources dans Java)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Deploy an SSH Enabled VM with a Template in Python (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Managing Azure Resource and Resource Groups with Python (Gérer des ressources et des groupes de ressources Azure avec Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

- [Deploy an SSH Enabled VM with a Template in Node.js (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Manage Azure resources and resource groups with Node.js (Gérer des ressources et des groupes de ressources Azure avec Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Deploy an SSH Enabled VM with a Template in Ruby (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Managing Azure Resource and Resource Groups with Ruby (Gérer des ressources et des groupes de ressources Azure avec Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## Étapes suivantes

- Pour en savoir plus sur la spécification de stratégies de sécurité, consultez la rubrique [Contrôle d’accès en fonction du rôle](./active-directory/role-based-access-control-configure.md).
- Pour une démonstration vidéo de ces étapes, consultez la rubrique [Activation de la gestion par programme d’une ressource Azure avec Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

<!---HONumber=AcomDC_0824_2016-->