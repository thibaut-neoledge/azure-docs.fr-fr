---
title: "Évaluer une application web Azure AD B2C | Microsoft Docs"
description: "Utiliser un environnement de test Azure AD B2C pour évaluer les procédures utilisateur de connexion, d’inscription, de modification d’un profil et de réinitialisation du mot de passe"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 2a51f15fd38a901548dcf4c56922f9715c328463
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-web-application-configured-with-azure-ad-b2c"></a>Évaluer une application web configurée avec Azure AD B2C

Azure Active Directory B2C assure la gestion des identités cloud pour vous aider à protéger votre application, votre entreprise et vos clients.  Ce guide de démarrage rapide utilise un exemple d’application de liste de tâches pour illustrer :

* l’utilisation d’une stratégie **d’inscription ou de connexion** pour créer ou se connecter avec un fournisseur d’identité sociale ou un compte local à l’aide d’une adresse e-mail ; 
* l’appel d’une API sécurisée par Azure AD B2C pour créer et modifier des éléments de tâche.

## <a name="prerequisites"></a>Composants requis

* Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
    - **Développement web et ASP.NET**

* Un compte de réseau social Facebook, Google, Microsoft ou Twitter. Si vous n’avez pas de compte de réseau social, une adresse de messagerie valide est nécessaire.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

[Téléchargez ou clonez l’exemple d’application](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) à partir de GitHub.

## <a name="run-the-app-in-visual-studio"></a>Exécuter l’application dans Visual Studio

Dans l’exemple de dossier de projet de l’application, ouvrez la solution `B2C-WebAPI-DotNet.sln` dans Visual Studio. 

La solution comprend deux projets :

* **TaskWebApp** : application web MVC ASP.NET qui permet à un utilisateur de gérer les éléments de sa liste de tâches.  
* **TaskService** : API web ASP.NET backend qui gère toutes les opérations CRUD effectuées sur les éléments de la liste des tâches d’un utilisateur. L’application web appelle cette API et affiche les résultats.

Pour ce guide de démarrage rapide, vous devez exécuter les projets `TaskWebApp` et `TaskService` simultanément. 

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Définir les projets de démarrage...**. 
2. Sélectionnez la case d’option **Plusieurs projets de démarrage**.
3. Pour les deux projets, définissez le paramètre **Action** sur **Démarrer**. Cliquez sur **OK**.

![Définir la page de démarrage dans Visual Studio](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Sélectionnez **Déboguer > Démarrer le débogage** pour générer et exécuter les deux applications. Chaque application s’ouvre dans son propre onglet de navigateur :

* `https://localhost:44316/` : cette page correspond à l’application web ASP.NET. Dans ce démarrage rapide, vous allez interagir directement avec cette application.
* `https://localhost:44332/` : cette page correspond à l’API web appelée par l’application web ASP.NET.

## <a name="create-an-account"></a>Créer un compte

Cliquez sur le lien **S’inscrire / Se connecter** dans l’application web ASP.NET pour démarrer le workflow **d’inscription ou de connexion**. Lorsque vous créez un compte, vous pouvez utiliser un compte de fournisseur d’identité sociale existant ou un compte de messagerie.

![Exemple d’application web ASP.NET](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>S’inscrire au moyen d’un fournisseur d’identité sociale

Pour vous inscrire au moyen d’un fournisseur d’identité sociale, cliquez sur le bouton en regard du fournisseur d’identité que vous souhaitez utiliser. Si vous préférez utiliser une adresse e-mail, passez à la section [S’inscrire au moyen d’une adresse e-mail](#sign-up-using-an-email-address).

![Fournisseur de connexion ou d’inscription](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Vous devez vous authentifier (vous connecter) à l’aide des informations d’identification de votre compte social et autoriser l’application à lire les informations de votre compte. En accordant l’accès à l’application, celle-ci peut extraire des informations de profil de votre compte de réseau social, par exemple votre nom et votre ville de résidence. 

![Authentification et autorisation à l’aide d’un compte de réseau social](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

Terminez le processus de connexion pour le fournisseur d’identité. Par exemple, pour Twitter, cliquez sur le bouton **Se connecter**.

Les informations de profil de votre nouveau compte sont déjà prérenseignées à partir des informations obtenues sur votre compte de réseau social.

![Informations de profil d’inscription du nouveau compte](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

Mettez à jour les champs Nom d’affichage, Fonction et Ville, puis cliquez sur **Continuer**.  Les valeurs que vous entrez sont utilisées pour le profil de votre compte d’utilisateur Azure AD B2C.

Vous venez de créer un compte d’utilisateur Azure AD B2C qui utilise un fournisseur d’identité. 

Étape suivante : [passez à la section Afficher vos revendications](#view-your-claims).

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

Si vous choisissez de ne pas vous authentifier à l’aide d’un compte de réseau social, vous pouvez créer un compte d’utilisateur Azure AD B2C en utilisant simplement une adresse e-mail valide. Un compte d’utilisateur local Azure AD B2C utilise Azure Active Directory comme fournisseur d’identité. Pour utiliser votre adresse e-mail, cliquez sur le lien **Vous ne possédez pas encore de compte ? Inscrivez-vous maintenant**.

![Connexion ou inscription au moyen d’une adresse e-mail](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-email-web.png)

Entrez une adresse e-mail valide et cliquez sur **Envoyer le code de vérification**. Une adresse e-mail valide est requise pour recevoir le code de vérification d’Azure AD B2C. 

Entrez le code de vérification reçu par e-mail, puis cliquez sur **Vérifier le code**.

Ajoutez vos informations de profil et cliquez sur **Créer**.

![S’inscrire avec le nouveau compte à l’aide de la messagerie](media/active-directory-b2c-quickstarts-web-app/sign-up-new-account-profile-email-web.png)

Vous venez de créer un nouveau compte d’utilisateur local Azure AD B2C.

## <a name="reset-your-password"></a>Réinitialiser votre mot de passe

Si vous avez créé votre compte à l’aide d’une adresse e-mail, Azure AD B2C possède des fonctionnalités permettant aux utilisateurs de réinitialiser leur mot de passe. Pour modifier le profil que vous avez créé, cliquez sur le nom de votre profil dans la barre de menus et sélectionnez **Réinitialiser le mot de passe**.

Pour vérifier votre adresse e-mail, saisissez-la et cliquez sur **Envoyer le code de vérification**. Un code de vérification vous est envoyé à votre adresse de messagerie.

Entrez le code de vérification que vous avez reçu par e-mail et cliquez sur **Vérifier le code**.

Après avoir vérifié votre adresse e-mail, cliquez sur **Continuer**.

Entrez votre nouveau mot de passe, puis cliquez sur **Continuer**.

## <a name="view-your-claims"></a>Afficher vos revendications

Cliquez sur **Revendications** dans la barre de menus de l’application web pour afficher les revendications associées à votre dernière action. 

![S’inscrire avec le nouveau compte à l’aide de la messagerie](media/active-directory-b2c-quickstarts-web-app/view-claims-sign-up-web.png)

Dans cet exemple, la dernière action portait sur l’expérience de *connexion ou d’inscription*. Notez que le **Type de revendication** `http://schemas.microsoft.com/claims/authnclassreference` est `b2c_1_susi`, ce qui signifie que la dernière action était une inscription ou une connexion. Si la dernière action était une réinitialisation du mot de passe, le **Type de revendication** serait `b2c_1_reset`.

## <a name="edit-your-profile"></a>Modifier votre profil

Azure Active Directory B2C fournit des fonctionnalités permettant aux utilisateurs de mettre à jour leurs profils. Dans la barre de menus de l’application web, cliquez sur le nom de votre profil et sélectionnez **Modifier le profil** pour modifier le profil que vous avez créé.

![Modifier le profil](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Modifiez votre **Nom d’affichage** et votre **Ville**.  Cliquez sur **Continuer** pour mettre à jour votre profil.

![Mettre à jour le profil](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Notez que votre nom d’affichage se met à jour en haut à droite de la page une fois que vous avez modifié votre nom. 

Cliquez sur **Revendications**. Les modifications apportées au **Nom d’affichage** et à la **Ville** apparaissent dans les revendications.

![Afficher des réclamations](media/active-directory-b2c-quickstarts-web-app/view-claims-update-web.png)

 Notez que le **Type de revendication** `http://schemas.microsoft.com/claims/authnclassreference` est passé à `b2c_1_edit_profile`, ce qui indique que la dernière action exécutée était une modification du profil. Notez également que le nom et la ville contiennent maintenant les nouvelles valeurs *Sara S.* et *Seattle*.

## <a name="access-a-resource"></a>Accéder à une ressource

Cliquez sur **Liste de tâches** pour entrer et modifier des éléments de votre liste des tâches. L’application web ASP.NET inclut un jeton d’accès dans la requête émise à la ressource d’API web pour demander l’autorisation d’effectuer des opérations sur les éléments de la liste des tâches de l’utilisateur. 

Entrez du texte dans la zone de texte **Nouvel élément**. Cliquez sur **Ajouter** pour appeler l’API web sécurisée Azure AD B2C qui ajoute un élément de liste des tâches.

![Ajouter un élément de la liste des tâches](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

## <a name="other-scenarios"></a>Autres scénarios

Autres scénarios d’évaluation :

* Déconnectez-vous de l’application et cliquez sur **Liste de tâches**. Notez la façon dont vous êtes invité à vous connecter et comment vos éléments de liste sont conservés. 
* Créez un nouveau compte à l’aide d’un autre type de compte. Par exemple, utilisez un fournisseur d’identité sociale si vous avez déjà créé un compte à l’aide d’une adresse e-mail.

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante consiste à créer votre propre locataire Azure AD B2C et à configurer l’exemple pour qu’il s’exécute à l’aide de votre locataire. 

> [!div class="nextstepaction"]
> [Créer un locataire Azure Active Directory B2C dans le portail Azure](active-directory-b2c-get-started.md)