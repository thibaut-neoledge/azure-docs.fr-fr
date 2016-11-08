---
title: 'Azure Active Directory B2C : inscription d’application | Microsoft Docs'
description: Inscription de votre application auprès d’Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: mbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: swkrish

---
# Azure Active Directory B2C : inscription de votre application
## Configuration requise
Pour générer une application acceptant l’inscription et la connexion des consommateurs, vous devez commencer par inscrire cette application auprès d’un client Azure Active Directory B2C. Obtenez votre propre client en suivant la procédure décrite dans [Création d’un client Azure AD B2C](active-directory-b2c-get-started.md). Après avoir suivi toutes les étapes de cet article, le panneau de fonctionnalités B2C doit être épinglé sur votre Tableau d’accueil.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Accéder au panneau de fonctionnalités B2C
Si le panneau de fonctionnalités B2C est épinglé à votre Tableau d’accueil, vous pouvez le voir dès que vous vous connectez au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur général du client B2C.

Vous pouvez également accéder au panneau en cliquant sur **Parcourir**, puis sur **Azure AD B2C** dans le panneau de navigation à gauche du [Portail Azure](https://portal.azure.com/).

> [!IMPORTANT]
> Vous devez être administrateur général du client B2C pour accéder au volet des fonctionnalités B2C. L’administrateur général ou l’utilisateur de tout autre client ne pourra pas y accéder. Vous pouvez basculer vers votre client B2C en utilisant le sélecteur de client dans le coin supérieur droit du Portail Azure.
> 
> 

## Inscription d'une application
1. Dans le panneau de fonctionnalités B2C du Portail Azure, cliquez sur **Applications**.
2. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
3. Entrez un **nom** pour l’application qui décrira votre application aux consommateurs. Par exemple, entrez « Application B2C Contoso ».
4. Si vous écrivez une application basée sur le web, positionnez le commutateur **Inclure l'application web/API web** sur **Oui**. Les **URL de réponse** sont des points de terminaison auxquels Azure AD B2C renvoie les jetons demandés par votre application. Par exemple, entrez :`https://localhost:44321/`. Si votre application web appelle également certaines API web sécurisées par Azure AD B2C, vous voudrez également créer une **Clé secrète d’application** en cliquant sur le bouton **Générer une clé**.
   
   > [!NOTE]
   > Une **Clé secrète d’application** est une information d’identification de sécurité importante, qui doit être correctement sécurisée.
   > 
   > 
5. Si vous écrivez une application mobile, positionnez le commutateur **Inclure un client natif** sur **Oui**. Notez la valeur par défaut **URI de redirection** créée automatiquement pour vous.
6. Cliquez sur **Créer** pour inscrire votre application.
7. Cliquez sur l’application que vous venez de créer, puis notez l’**ID du client d’application** global unique à intégrer ultérieurement dans votre code.

> [!IMPORTANT]
> Les applications créées dans le panneau de fonctionnalités B2C doivent être gérées dans le même emplacement. Si vous modifiez des applications B2C à l’aide de PowerShell ou d’un autre portail, elles ne sont plus prises en charge et ne fonctionneront probablement pas avec Azure AD B2C.
> 
> 

## Générer une application de démarrage rapide
À présent que vous avez une application inscrite auprès d’Azure AD B2C, vous pouvez suivre l’un de nos didacticiels de démarrage rapide afin de devenir opérationnel. Voici quelques recommandations :

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0831_2016-->