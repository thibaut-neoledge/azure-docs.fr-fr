---
title: 'Azure Active Directory B2C : création d’un client Azure Active Directory B2C | Microsoft Docs'
description: Rubrique sur la création d’un client Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/30/2016
ms.author: swkrish

---
# Azure Active Directory B2C : création d’un client Azure AD B2C
Pour commencer à utiliser Microsoft Azure Active Directory (Azure AD) B2C, suivez les trois étapes décrites dans cet article.

## Étape 1 : souscrire un abonnement Azure
Vous pouvez ignorer cette étape si vous possédez déjà un abonnement Azure. Dans le cas contraire, souscrivez [un abonnement Azure](../active-directory/sign-up-organization.md) et accédez à Azure AD B2C.

## Étape 2 : créer un client Azure AD B2C
Procédez comme suit pour créer un client Azure AD B2C. Actuellement, les fonctionnalités B2C ne peuvent pas être activées dans vos clients existants.

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) en tant qu’administrateur d’abonnements. Il s’agit du compte professionnel ou scolaire, ou du compte Microsoft que vous avez utilisé pour vous inscrire à Azure.
2. Cliquez sur **Nouveau** > **Services d’application** > **Active Directory** > **Annuaire** > **Création personnalisée**.
   
    ![Capture d’écran du début de la création d’un client](./media/active-directory-b2c-get-started/new-directory.png)
3. Choisissez un **nom**, un **nom de domaine** et un **pays ou une région** pour votre client.
4. Activez l’option **Ceci est un répertoire B2C**.
5. Cliquez sur la coche pour achever la configuration.
   
    ![Capture d’écran de la case à cocher pour créer un répertoire B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)
6. Votre client est désormais créé et apparaît dans l’extension Active Directory. Vous êtes par ailleurs administrateur général du client. Vous pouvez ajouter d’autres administrateurs généraux en fonction des besoins.
   
   > [!IMPORTANT]
   > Si vous envisagez d’utiliser un client B2C pour une application de production, consultez l’article sur [les clients de mise à l’échelle pour production/clients B2C de la version préliminaire](active-directory-b2c-reference-tenant-type.md). Notez qu’il existe des problèmes connus liés à la suppression d’un client B2C existant et à sa recréation sous le même nom de domaine. Vous devez créer un client B2C portant un nom de domaine différent.
   > 
   > 

## Étape 3 : accéder au panneau de fonctionnalités B2C sur le portail Azure
1. Accédez à l’extension Active Directory dans la barre de navigation sur le côté gauche.
2. Recherchez votre client sous l’onglet **Répertoire**, puis cliquez dessus.
3. Cliquez sur l’onglet **Configurer**.
4. Cliquez sur le lien **Gestion des paramètres B2C** dans la section **Administration B2C**.
   
    ![Capture d’écran de configuration de répertoire B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)
5. Le portail Azure avec le panneau de fonctionnalités B2C s’ouvre dans un nouvel onglet ou une nouvelle fenêtre de navigateur.
   
   > [!IMPORTANT]
   > Cela peut prendre jusqu’à 2 à 3 minutes avant que votre client soit accessible sur le portail Azure. Le fait de patienter avant de réessayer résoudra le problème. Si ce n’est pas le cas, veuillez contacter le support technique.
   > 
   > 
6. Épinglez ce panneau sur votre Tableau d’accueil pour en faciliter l’accès. (L’outil Épingler est marqué en rouge dans le coin supérieur droit du panneau de fonctionnalités).
   
    ![Capture d’écran du panneau de fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)
   
   > [!NOTE]
   > Vous pouvez gérer les utilisateurs et groupes, la configuration de la réinitialisation du mot de passe libre-service et les fonctionnalités de promotion professionnelle de votre client via le [portail Azure Classic](https://manage.windowsazure.com/).
   > 
   > 

## Accès facile au panneau de fonctionnalités B2C sur le portail Azure
Pour améliorer la détectabilité, nous avons ajouté un raccourci dans le panneau de fonctionnalités B2C sur le portail Azure.

1. Connectez-vous au portail Azure en tant qu’administrateur général de votre client B2C. Si vous êtes déjà connecté à un autre client, basculez entre les clients (dans le coin supérieur droit).
2. Cliquez sur **Parcourir** dans le volet de navigation de gauche.
3. Cliquez sur **Azure AD B2C** pour accéder au panneau de fonctionnalités B2C.
   
    ![Capture d’écran du bouton Parcourir du panneau de fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

## Étapes suivantes
Découvrez comment inscrire une application avec Azure AD B2C et créer une application de démarrage rapide en lisant [Azure Active Directory B2C : inscription de votre application](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0831_2016-->