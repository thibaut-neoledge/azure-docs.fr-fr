---
title: "Publier des applications avec le proxy d’application Azure AD | Microsoft Docs"
description: "Publiez des applications locales dans le cloud avec le proxy d’application Azure AD dans le portail Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5acfbfbe7327fc28fa909e6ef7e0f9b6ce8b0e54
ms.openlocfilehash: e0be8f4617c2fdbe1cb6fe2f904bf9ea7056c55e

---


# <a name="publish-applications-using-azure-ad-application-proxy---public-preview"></a>Publier des applications avec le proxy d’application Azure AD - Version préliminaire publique

> [!div class="op_single_selector"]
> * [Portail Azure](application-proxy-publish-azure-portal.md)
> * [Portail Azure Classic](active-directory-application-proxy-publish.md)

Le service Proxy d’application Azure Active Directory (AD) vous aide à prendre en charge les personnes qui travaillent à distance en publiant des applications locales afin de les rendre accessibles sur Internet. Dans le portail Azure, vous pouvez publier des applications exécutées sur votre réseau local et fournir un accès à distance sécurisé depuis l’extérieur de votre réseau.

Cet article vous guide au long des étapes de publication d’une application locale avec un proxy d’application. Lorsque vous aurez terminé de lire cet article, vous serez prêt à configurer l’application avec l’authentification unique, des exigences de sécurité ou des informations personnalisées.

Si vous n’êtes pas familiarisé avec le proxy d’application, vous pouvez en savoir plus en consultant l’article [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>Publier une application locale pour un accès à distance


> [!TIP]
> S’il s’agit de votre première utilisation du proxy d’application, choisissez une application qui est déjà configurée pour l’authentification par mot de passe. Le proxy d’application prend en charge d’autres types d’authentification, mais les applications basées sur un mot de passe sont les plus faciles à exploiter rapidement. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur.
2. Sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Ajouter**.

  ![Ajouter une application d’entreprise](./media/application-proxy-publish-azure-portal/add-app.png)

3. Dans la page Catégories, sélectionnez **Application locale**.  

  ![Ajout de votre propre application](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Fournissez les informations suivantes sur votre application :

   - **Nom** : le nom de l’application qui apparaît dans le panneau d’accès. 

   - **URL interne**: adresse utilisée par le connecteur du proxy d’application pour accéder à l’application à partir de votre réseau privé. Vous pouvez spécifier un chemin spécifique sur le serveur principal à publier, alors que le reste du serveur n’est pas publié. De cette façon, vous pouvez publier des sites différents sur le même serveur en tant qu’applications distinctes et donner à chacun d’eux son propre nom et ses propres règles d’accès.

     > [!TIP]
     > Si vous publiez un chemin d’accès, vérifiez qu’il inclut l’ensemble des images, des scripts et des feuilles de style nécessaires pour votre application. Par exemple, si votre application est sur https://yourapp/app et utilise des images situées dans https://yourapp/media, vous devez publier https://yourapp/ comme chemin d’accès.

   - **URL externe** : adresse que vos utilisateurs utilisent pour accéder à l’application en dehors de votre réseau.
   - **Pré-authentification** : façon dont le service Proxy d’application vérifie les utilisateurs avant de leur donner accès à votre application. 

     - Azure Active Directory : le proxy d’application redirige les utilisateurs pour la connexion à Azure AD, qui authentifie leurs autorisations pour le répertoire et l’application. Nous vous recommandons de conserver cette option par défaut.
     - Direct : les utilisateurs n’ont pas besoin de s’authentifier auprès d’Azure Active Directory pour accéder à l’application. Vous pouvez toujours configurer les exigences d’authentification sur le serveur principal.
   - **Voulez-vous traduire l’URL dans les en-têtes ?** : indiquez si vous souhaitez traduire l’URL dans les en-têtes ou conserver l’original. 
   - **Groupe de connecteurs** : les connecteurs gèrent l’accès à distance à votre application et les groupes de connecteurs vous aident à organiser des connecteurs et des applications par région, réseau ou objectif. Si vous n’avez pas encore créé de groupe de connecteurs, votre application est attribuée au groupe **par défaut** et un message d’avertissement vous demande de [créer un groupe de connecteurs](active-directory-application-proxy-connectors-azure-portal.md).

   ![Configuration de votre application](./media/application-proxy-publish-azure-portal/configure-app.png)

8. Sélectionnez **Ajouter**.


## <a name="add-a-test-user"></a>Ajouter un utilisateur de test 

Pour vérifier que votre application a été correctement publiée, ajoutez un compte d’utilisateur auquel vous avez accès. 

1. Dans le panneau Démarrage rapide, sélectionnez **Assign a user for testing (Attribuer un utilisateur à des fins de test)**.

  ![Attribuer un utilisateur à des fins de test](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Dans le panneau Utilisateurs et groupes, sélectionnez **Ajouter**.

  ![Ajouter un utilisateur ou groupe](./media/application-proxy-publish-azure-portal/add-user.png)

3. Dans le panneau Ajouter une attribution, sélectionnez **Utilisateurs et groupes**, puis le compte à ajouter. 
4. Sélectionnez **Attribuer**.

## <a name="test-your-published-app"></a>Tester votre application publiée

Dans votre navigateur, accédez à l’URL externe que vous avez configurée au cours de l’étape de publication. L’écran d’accueil doit s’afficher et vous devez être en mesure de vous connecter avec le compte de test que vous avez configuré.

![Tester votre application publiée](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Étapes suivantes
- [Télécharger des connecteurs](active-directory-application-proxy-enable.md) et [créer des groupes de connecteurs](active-directory-application-proxy-connectors-azure-portal.md) pour publier des applications sur des réseaux et emplacements distincts

- [Configurer l’authentification unique](application-proxy-sso-azure-portal.md) pour votre application récemment publiée



<!--HONumber=Feb17_HO2-->


