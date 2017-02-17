---
title: "Création d’un principal du service dans le portail | Microsoft Docs"
description: "Décrit comment créer une application et un principal du service Active Directory qui peuvent être utilisés avec le contrôle d&quot;accès basé sur les rôles dans Azure Resource Manager pour gérer l&quot;accès aux ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4312002b311ec17f175f6eb6bc45fbe1ce7c7a01
ms.openlocfilehash: 3232aa0356353e3856286c38d931543a254fd9fd


---
# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Utiliser le portail pour créer une application et un principal du service Active Directory pouvant accéder aux ressources
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Interface de ligne de commande Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portail](resource-group-create-service-principal-portal.md)
>
>

Si une application doit accéder à des ressources ou les modifier, vous devez configurer une application Active Directory (AD) et lui accorder les autorisations nécessaires. Cette rubrique explique comment effectuer ces étapes via le portail. Elle se concentre sur une application à locataire unique conçue pour s’exécuter au sein d’une seule organisation. Les applications à locataire unique sont généralement utilisées pour les applications métier exécutées au sein de votre organisation.
 
## <a name="required-permissions"></a>Autorisations requises
Pour cette rubrique, vous devez disposer des autorisations suffisantes pour enregistrer une application auprès d’Active Directory et affecter l’application à un rôle dans votre abonnement Azure. Vérifions que vous disposez des droits suffisants pour effectuer ces étapes.

### <a name="check-active-directory-permissions"></a>Vérifier les autorisations Active Directory
1. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Azure Active Directory**.

     ![sélectionner azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)
3. Dans votre Active Directory, sélectionnez **Paramètres utilisateur**.

     ![sélectionner les paramètres utilisateur](./media/resource-group-create-service-principal-portal/select-user-settings.png)
4. Vérifiez le paramètre **Inscriptions d’applications**. S’il est défini sur **Oui**, les utilisateurs non administrateurs peuvent inscrire des applications AD. Ce paramètre signifie que n’importe quel utilisateur dans Active Directory peut inscrire une application. Vous pouvez passer à [Vérifier les autorisations d’abonnement Azure](#check-azure-subscription-permissions).

     ![afficher les inscriptions d’applications](./media/resource-group-create-service-principal-portal/view-app-registrations.png)
5. Si le paramètre d’inscriptions d’applications est défini sur **Non**, seuls les utilisateurs administrateurs peuvent inscrire des applications. Vous devez vérifier si votre compte est un administrateur d’Active Directory. Sélectionnez **Vue d’ensemble** et **Rechercher un utilisateur** dans Tâches rapides.

     ![rechercher un utilisateur](./media/resource-group-create-service-principal-portal/find-user.png)
6. Recherchez votre compte et sélectionnez-le lorsque vous l’avez trouvé.

     ![rechercher un utilisateur](./media/resource-group-create-service-principal-portal/show-user.png)
7. Pour votre compte, sélectionnez **Rôle Directory**. 

     ![rôle directory](./media/resource-group-create-service-principal-portal/select-directory-role.png)
8. Affichez votre rôle affecté pour Active Directory. Si le rôle Utilisateur est affecté à votre compte mais que le paramètre d’inscription d’application (de la procédure précédente) est limité aux utilisateurs administrateurs, demandez à votre administrateur de vous affecter rôle administrateur ou d’autoriser les utilisateurs à inscrire des applications.

     ![afficher le rôle](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Vérifier les autorisations d’abonnement Azure
Dans votre abonnement Azure, votre compte doit disposer d’un accès `Microsoft.Authorization/*/Write` pour affecter un rôle à une application AD. Cette action est accordée par le biais du rôle [Propriétaire](../active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Si le rôle **Collaborateur** est affecté à votre compte, vous ne disposez pas de l’autorisation appropriée. Vous recevez un message d’erreur lorsque vous tentez d’affecter un rôle au principal du service. 

Pour vérifier vos autorisations d’abonnement :

1. Si vous n’avez pas déjà recherché votre compte Active Directory dans le cadre de la procédure précédente, sélectionnez **Azure Active Directory** dans le volet gauche.

2. Recherchez votre compte Active Directory. Sélectionnez **Vue d’ensemble** et **Rechercher un utilisateur** dans Tâches rapides.

     ![rechercher un utilisateur](./media/resource-group-create-service-principal-portal/find-user.png)
2. Recherchez votre compte et sélectionnez-le lorsque vous l’avez trouvé.

     ![rechercher un utilisateur](./media/resource-group-create-service-principal-portal/show-user.png) 
     
3. Sélectionnez **Ressources Azure**.

     ![sélectionner des ressources](./media/resource-group-create-service-principal-portal/select-azure-resources.png) 
3. Affichez les rôles qui vous sont affectés et déterminez si vous disposez des autorisations appropriées pour affecter un rôle à une application AD. Si ce n’est pas le cas, demandez à votre administrateur d’abonnement de vous ajouter un rôle Administrateur de l’accès utilisateur. Dans l’image suivante, le rôle Propriétaire est affecté à l’utilisateur pour deux abonnements, ce qui signifie que l’utilisateur dispose des autorisations appropriées. 

     ![afficher les autorisations](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-active-directory-application"></a>Créer une application Active Directory
1. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Azure Active Directory**.

     ![sélectionner azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

4. Sélectionnez **Inscriptions d’applications**.   

     ![sélectionner des inscriptions d’applications](./media/resource-group-create-service-principal-portal/select-app-registrations.png)
5. Sélectionnez **Ajouter**.

     ![ajouter une application](./media/resource-group-create-service-principal-portal/select-add-app.png)

6. Fournissez un nom et une URL pour l’application. Sélectionnez **Application Web / API** ou **Native** pour le type d’application que vous souhaitez créer. Après avoir défini les valeurs, sélectionnez **Créer**.

     ![nommer l’application](./media/resource-group-create-service-principal-portal/create-app.png)

Vous avez créé votre application.

## <a name="get-application-id-and-authentication-key"></a>Obtenir un ID d’application et une clé d’authentification
Lors d’une connexion par programmation, vous aurez besoin de l’ID de votre application et d’une clé d’authentification. Pour obtenir ces valeurs, procédez comme suit :

1. Dans **Inscriptions d’applications** dans Active Directory, sélectionnez votre application.

     ![sélectionner une application](./media/resource-group-create-service-principal-portal/select-app.png)
2. Copiez l’**ID d’application** et stockez-le dans votre code d’application. Les applications de la section [Exemples d’applications](#sample-applications) font référence à cette valeur en tant qu’ID de locataire.

     ![ID CLIENT](./media/resource-group-create-service-principal-portal/copy-app-id.png)
3. Pour générer une clé d’authentification, sélectionnez **Clés**.

     ![sélectionner des clés](./media/resource-group-create-service-principal-portal/select-keys.png)
4. Fournissez une description de la clé et la durée de la clé. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

     ![enregistrer une clé](./media/resource-group-create-service-principal-portal/save-key.png)

     Après avoir enregistré la clé, la valeur de la clé s’affiche. Copiez cette valeur car vous ne pourrez pas récupérer la clé ultérieurement. Vous fournissez la valeur de la clé avec l’ID d’application pour vous connecter en tant qu’application. Stockez la valeur de la clé à un emplacement où votre application peut la récupérer.

     ![clé enregistrée](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Obtenir l’ID de locataire
Lors d’une connexion par programmation, vous devez transmettre l’ID de locataire avec votre demande d’authentification. 

1. Pour obtenir l’ID de locataire, sélectionnez **Propriétés** pour votre Active Directory. 

     ![sélectionner les propriétés active directory](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

2. Copiez l’**ID Directory**. Cette valeur est votre ID de locataire.

     ![ID client](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Affecter l’application à un rôle
Pour accéder aux ressources de votre abonnement, vous devez affecter un rôle à l’application. Vous devez décider du rôle qui doit représenter les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md).

Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Accédez au niveau d’étendue que vous souhaitez affecter à l’application. Par exemple, pour affecter un rôle sur l’étendue de l’abonnement, sélectionnez **Abonnements**. Vous pouvez également sélectionner un groupe de ressources ou une ressource.

     ![sélectionner l'abonnement](./media/resource-group-create-service-principal-portal/select-subscription.png)

2. Sélectionnez l’abonnement (groupe de ressources ou ressource) auquel l’application doit être affectée.

     ![sélectionner l’abonnement pour l’affectation](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

3. Sélectionnez **Contrôle d’accès (IAM)**.

     ![sélectionner l’accès](./media/resource-group-create-service-principal-portal/select-access-control.png)

4. Sélectionnez **Ajouter**.

     ![sélectionner ajouter](./media/resource-group-create-service-principal-portal/select-add.png)
6. Sélectionnez le rôle que vous souhaitez affecter à l’application. L’image suivante montre le code **Lecteur**.

     ![sélectionner un rôle](./media/resource-group-create-service-principal-portal/select-role.png)

8. Recherchez votre application et sélectionnez-la.

     ![rechercher une application](./media/resource-group-create-service-principal-portal/search-app.png)
9. Sélectionnez **OK** pour finaliser l’affectation du rôle. Votre application apparaît dans la liste des utilisateurs affectés à un rôle pour cette étendue.

Votre application est maintenant configurée dans Active Directory. Vous disposez d’un ID et d’une clé à utiliser pour la connexion en tant qu’application. Un rôle est affecté à l’application qui lui permet d’effectuer certaines actions. Vous pouvez rechercher des exemples d’applications pour en savoir plus sur l’exécution de tâches dans le code d’application.

## <a name="sample-applications"></a>Exemples d'applications
Les exemples d’applications suivants montrent comment ouvrir une session en tant qu’application AD.

**.NET**

* [Deploy an SSH Enabled VM with a Template with .NET (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Manage Azure resources and resource groups with .NET (Gérer des ressources et des groupes de ressources Azure avec .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Getting Started with Resources - Deploy Using Azure Resource Manager Template - in Java (Découverte des ressources - Déployer à l’aide du modèle Azure Resource Manager dans Java)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Getting Started with Resources - Manage Resource Group - in Java (Découverte des ressources - Gérer un groupe de ressources dans Java)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Deploy an SSH Enabled VM with a Template in Python (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Managing Azure Resource and Resource Groups with Python (Gérer des ressources et des groupes de ressources Azure avec Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Deploy an SSH Enabled VM with a Template in Node.js (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Manage Azure resources and resource groups with Node.js (Gérer des ressources et des groupes de ressources Azure avec Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Deploy an SSH Enabled VM with a Template in Ruby (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Managing Azure Resource and Resource Groups with Ruby (Gérer des ressources et des groupes de ressources Azure avec Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer une application mutualisée, consultez le [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).
* Pour en savoir plus sur la spécification de stratégies de sécurité, consultez la rubrique [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md).  




<!--HONumber=Dec16_HO1-->


