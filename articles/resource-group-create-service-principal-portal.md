<properties
   pageTitle="Créer un principal du service dans le portail | Microsoft Azure"
   description="Décrit comment créer une application et un principal du service Active Directory qui peuvent être utilisés avec le contrôle d'accès basé sur les rôles dans Azure Resource Manager pour gérer l'accès aux ressources."
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
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Utiliser le portail pour créer une application et un principal du service Active Directory pouvant accéder aux ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interface de ligne de commande Azure](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)


Si une application doit accéder à des ressources ou les modifier, vous devez configurer une application Active Directory (AD) et lui accorder les autorisations nécessaires. Cette rubrique explique comment effectuer ces étapes via le portail. Actuellement, vous devez utiliser le portail Azure Classic pour créer une application Active Directory, puis revenir au portail Azure pour attribuer un rôle à l’application.

> [AZURE.NOTE] Vous pouvez configurer plus facilement votre application AD et le principal du service via [PowerShell](resource-group-authenticate-service-principal.md) ou [Azure CLI](resource-group-authenticate-service-principal-cli.md), en particulier si vous souhaitez utiliser un certificat pour l’authentification. Cette rubrique n’explique pas comment utiliser un certificat.

Pour obtenir une explication des concepts Active Directory, consultez [Objets principal du service et application](./active-directory/active-directory-application-objects.md). Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Pour obtenir des instructions détaillées sur l’intégration d’une application à Azure pour la gestion des ressources, consultez [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).

## Créer une application Active Directory

1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/).

2. Sélectionnez **Active Directory** dans le volet gauche.

     ![sélectionner Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Sélectionnez l’annuaire Active Directory que vous voulez utiliser pour la création de l’application. En règle générale, si vous avez plusieurs annuaires Active Directory, il est préférable de créer l’application dans le celui où se trouve votre abonnement. Vous pouvez uniquement attribuer l’accès aux ressources de votre abonnement aux applications qui se trouvent dans le même répertoire que votre abonnement.

     ![choisir l’annuaire](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
    Pour trouver le répertoire de votre abonnement, sélectionnez **Paramètres**, puis recherchez le nom du répertoire.
   
     ![rechercher le répertoire par défaut](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. Pour afficher les applications dans votre annuaire, cliquez sur **Applications**.

     ![afficher les applications](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Si vous n’avez pas préalablement créé d’application dans cet annuaire, vous devez voir quelque chose de similaire à l’image suivante. Cliquez sur **AJOUTER UNE APPLICATION**

     ![ajouter l’application](./media/resource-group-create-service-principal-portal/create-application.png)

     Ou, cliquez sur **Ajouter** dans le volet inférieur.

     ![ajouter](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Sélectionnez le type d’application que vous souhaitez créer. Pour ce didacticiel, sélectionnez **Ajouter une application développée par mon organisation**.

     ![nouvelle application](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Renseignez le nom de l’application et sélectionnez le type d’application que vous voulez créer. Pour ce didacticiel,créez une **application web et/ou API web**, puis cliquez sur le bouton Suivant. Si vous sélectionnez **Application cliente native**, les étapes suivantes de cet article ne correspondront pas à votre cas.

     ![nommer l’application](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Renseignez les propriétés de votre application. Pour **URL de connexion**, indiquez l’URI d’un site web qui décrit votre application. L’existence du site web n’est pas validée. Pour **URI ID d’application**, indiquez l’URI qui identifie votre application.

     ![propriétés de l’application](./media/resource-group-create-service-principal-portal/app-properties.png)

Vous avez créé votre application.

## Obtenir un ID client et une clé d’authentification

Lors d’une connexion par programmation, vous aurez besoin de l’ID de votre application. Si l’application s’exécute sous ses propres informations d’identification, vous aurez également besoin d’une clé d’authentification.

1. Cliquez sur l’onglet **Configurer** pour configurer le mot de passe de votre application.

     ![configurer l’application](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copiez **l’ID client**.
  
     ![ID de client](./media/resource-group-create-service-principal-portal/client-id.png)

3. Si l’application s’exécute sous ses propres informations d’identification, faites défiler jusqu’à la section **Clés** et sélectionnez la durée de validité de votre mot de passe.

     ![clés](./media/resource-group-create-service-principal-portal/create-key.png)

4. Sélectionnez **Enregistrer** pour créer votre clé.

     ![enregistrer](./media/resource-group-create-service-principal-portal/save-icon.png)

     La clé enregistrée s’affiche. Vous pouvez la copier. Copiez la clé dès à présent, car par la suite, vous ne pourrez plus la récupérer.

     ![clé enregistrée](./media/resource-group-create-service-principal-portal/save-key.png)

## Obtenir l’ID de locataire

Lors d’une connexion par programmation, vous devez transmettre l’ID de locataire avec votre demande d’authentification. Pour les applications web et les applications API, vous pouvez récupérer l’ID de locataire en sélectionnant **Afficher les points de terminaison** au bas de l’écran et en récupérant l’ID comme indiqué ci-dessous.

   ![ID client](./media/resource-group-create-service-principal-portal/save-tenant.png)

Vous pouvez également récupérer l’ID de locataire à l’aide de PowerShell :

    Get-AzureRmSubscription

Ou d’Azure CLI :

    azure account show --json

## Définir des autorisations déléguées

Si votre application accède aux ressources pour le compte d’un utilisateur connecté, vous devez accorder à votre application l’autorisation déléguée d’accéder aux autres applications. Pour cela, vous devez vous rendre dans la section **Autorisations pour d’autres applications** de l’onglet **Configurer**. Par défaut, une autorisation déléguée est déjà activée pour Azure Active Directory. Ne modifiez pas cette autorisation déléguée.

1. Sélectionnez **Ajouter une application**.

2. Dans la liste, sélectionnez l’**API de gestion des services Azure**. Cliquez sur l’icône Terminer.

      ![sélectionner une application](./media/resource-group-create-service-principal-portal/select-app.png)

3. Dans la liste déroulante Autorisations déléguées, sélectionnez **Access Azure Service Management as organization** (Accéder à la gestion des services Azure en tant qu’organisation).

      ![modifier une autorisation](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Enregistrez la modification.

## Configurer une application mutualisée

Si les utilisateurs d’autres annuaires Azure Active Directory peuvent donner leur consentement à l’application et se connecter, vous devez activer une architecture mutualisée. Dans l’onglet **Configurer**, définissez **Application mutualisée** sur **Oui**.

![architecture mutualisée](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Affecter l’application à un rôle

Si votre application s’exécute sous ses propres informations d’identification, vous devez l’assigner à un rôle. Vous devez décider du rôle qui doit représenter les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md).

Pour affecter un rôle, vous devez disposer d’un accès `Microsoft.Authorization/*/Write`, qui est accordé via le rôle [Propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Pour affecter l’application à un rôle, passez du portail Azure Classic au [portail Azure](https://portal.azure.com).

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

Pour plus d’informations sur l’attribution des utilisateurs, des applications et des rôles au moyen du portail, consultez la rubrique [Gérer l’accès à l’aide du portail de gestion Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## Exemples d'applications

Les exemples d’applications suivants montrent comment ouvrir une session en tant que principal du service.

**.NET**

- [Deploy an SSH Enabled VM with a Template with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans .NET)
- [Manage Azure resources and resource groups with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec .NET)

**Java**

- [Getting Started with Resources - Deploy Using Azure Resource Manager Template - in Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/) (Découverte des ressources - Déployer à l’aide du modèle Azure Resource Manager dans Java)
- [Getting Started with Resources - Manage Resource Group - in Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//) (Découverte des ressources - Gérer un groupe de ressources dans Java)

**Python**

- [Deploy an SSH Enabled VM with a Template in Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Python)
- [Managing Azure Resource and Resource Groups with Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec Python)

**Node.JS**

- [Deploy an SSH Enabled VM with a Template in Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Node.js)
- [Manage Azure resources and resource groups with Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec Node.js)

**Ruby**

- [Deploy an SSH Enabled VM with a Template in Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Ruby)
- [Managing Azure Resource and Resource Groups with Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec Ruby)


## Étapes suivantes

- Pour en savoir plus sur la spécification de stratégies de sécurité, consultez la rubrique [Contrôle d’accès en fonction du rôle](./active-directory/role-based-access-control-configure.md).
- Pour une démonstration vidéo de ces étapes, consultez la rubrique [Activation de la gestion par programme d’une ressource Azure avec Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

<!---HONumber=AcomDC_0720_2016-->