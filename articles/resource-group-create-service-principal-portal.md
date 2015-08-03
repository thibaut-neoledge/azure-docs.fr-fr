<properties
   pageTitle="Création d’un nouveau principal du service Azure à l’aide du portail Azure"
   description="Décrit comment créer un nouveau principal du service Azure qui peut être utilisé avec le contrôle d’accès basé sur les rôles dans Azure Resource Manager pour gérer l’accès aux ressources."
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
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Création d’un nouveau principal du service Azure à l’aide du portail Azure

## Vue d'ensemble
Un principal du service est un processus, une application ou un service automatisé qui doit accéder à d’autres ressources. À l’aide d’Azure Resource Manager, vous pouvez accorder l’accès à un principal du service et l’authentifier afin qu’il puisse exécuter les actions de gestion autorisées sur les ressources qui existent dans l’abonnement ou en tant que client.

Cette rubrique montre comment créer un nouveau principal du service à l’aide du portail Azure. Actuellement, vous devez utiliser le portail Microsoft Azure pour créer un nouveau principal du service. Cette possibilité sera ajoutée à la version préliminaire du portail Azure dans une version ultérieure.

## Concepts
1. Azure Active Directory (AAD) - une build de service de gestion des identités et des accès pour le cloud. Pour plus d’informations, consultez la rubrique [Qu’est-ce qu’Azure Active Directory ?](./active-directory-whatis/)
2. Principal du service : une instance d’application dans un annuaire.
3. Application Active Directory : un enregistrement d’annuaire dans AAD qui identifie une application à AAD. Pour plus d’informations, consultez [Principes fondamentaux de l’authentification dans Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).


## Création de l’application Active Directory
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

6. Renseignez le nom de l’application et sélectionnez le type d’application que vous souhaitez utiliser. Étant donné que nous avons l’intention d’utiliser le principal du service de cette application pour s’authentifier auprès d’Azure Resource Manager, nous choisirons de créer une **APPLICATION WEB ET/OU une API WEB** et cliquerons sur le bouton Suivant.

   ![nommer l’application][9]

7. Renseignez les propriétés de votre application. Pour **URL de connexion**, indiquer l’URI vers un site web qui décrit votre application. L’existence du site web n’est pas validée. Pour **URI ID d’application**, indiquez l’URI qui identifie votre application. Le caractère unique ou l’existence du point de terminaison n’est pas validé. Cliquez sur **Terminé** pour créer votre application AAD.

   ![propriétés de l’application][4]

## Création de votre mot de passe de principal du service
L’application doit à présent être sélectionnée dans le portail.

1. Cliquez sur l’onglet **Configurer** pour configurer le mot de passe de votre application.

   ![configurer l’application][3]

2. Faites défiler jusqu’à la section **Clés** et sélectionnez la durée de validité de votre mot de passe.

   ![clés][7]

3. Sélectionnez **Enregistrer** pour créer votre clé.

   ![enregistrer][13]

   La clé enregistrée s’affiche. Vous pouvez la copier.

   ![clé enregistrée][8]

4. Vous pouvez maintenant utiliser votre clé pour vous authentifier en tant que principal du service. Vous aurez besoin votre **ID CLIENT** en plus de votre **CLÉ** pour vous connecter. Accédez à **ID CLIENT** et copiez-le.
  
   ![ID de client][5]


Votre application est maintenant prête et le principal du service est créé sur votre client. Lorsque vous vous connectez en tant que principal du service, veillez à utiliser :

* **ID CLIENT** comme nom d’utilisateur.
* **CLÉ** comme mot de passe.

## Étapes suivantes
Mise en route

- [Présentation d’Azure Resource Manager](./resource-group-overview.md)  
- [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md)
- [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec le Gestionnaire des ressources Azure](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [Utilisation du portail Azure pour gérer vos ressources Azure](azure-portal/resource-group-portal.md)  
  
Création et déploiement d’applications
  
- [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md)  
- [Déploiement d’une application avec un modèle Azure Resource Manager](azure-portal/resource-group-template-deploy.md)  
- [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](virtual-machines/resource-group-deploy-debug.md)  
- [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md)  
- [Opérations de modèle avancées](./resource-group-advanced-template.md)  
- [Déploiement de ressources Azure à l’aide de bibliothèques .NET et d’un modèle](virtual-machines/arm-template-deployment.md)
  
Organisation des ressources
  
- [Organisation des ressources Azure à l’aide de balises](./resource-group-using-tags.md)  
  
Gestion et audit de l’accès
  
- [Gestion et audit d’accès aux ressources](azure-portal/resource-group-rbac.md)  
- [Authentification d’un principal du service à l’aide d’Azure Resource Manager](./resource-group-authenticate-service-principal.md)  

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

<!---HONumber=July15_HO4-->