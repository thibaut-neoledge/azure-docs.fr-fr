<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : inscription d’application | Microsoft Azure"
	description="Inscription de votre application auprès d’Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory entreprise-client (B2C) en version préliminaire : comment inscrire votre application

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Conditions préalables

Pour créer une application qui accepte l’inscription et la connexion des consommateurs, vous devez commencer par inscrire cette application auprès d’un annuaire Azure Active Directory B2C. Obtenez votre propre annuaire en procédant de la manière décrite [ici](active-directory-b2c-get-started.md). Si vous avez suivi toutes les étapes de cet article, le panneau de fonctionnalités B2C doit être épinglé sur votre Tableau d’accueil.

## Accéder au panneau de fonctionnalités B2C

Vous pouvez accéder au panneau de fonctionnalités B2C de l’une des deux manières suivantes.

### 1\. Accès direct via le portail Azure en version préliminaire

Si le panneau de fonctionnalités B2C est épinglé sur votre Tableau d’accueil, vous pouvez le voir dès que vous vous connectez au [portail Azure en version préliminaire](https://portal.azure.com/) en tant qu’administrateur général du répertoire B2C.

Vous pouvez également accéder directement au panneau de fonctionnalités B2C en naviguant vers l’URL [https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com) où **{directory}** est remplacé par le nom utilisé au moment de la création de l’annuaire (par exemple, contosob2c), puis en vous connectant en tant qu’administrateur général du répertoire B2C. Vous pouvez marquer ce lien à l’aide d’un signet afin de vous y référer ultérieurement.

### 2\. Accès via le portail Azure

Connectez-vous au [portail Azure](https://manage.windowsazure.com/) en tant qu’administrateur d’abonnements (à l’aide du compte professionnel ou scolaire, ou du compte Microsoft utilisé pour l’inscription à Azure). Accédez à l’extension Active Directory sur la gauche, puis cliquez sur votre répertoire B2C. Sous l’onglet **Démarrage rapide** (le premier à s’ouvrir), cliquez sur **Gestion des paramètres B2C** sous **Administrer**. Cela a pour effet d’ouvrir le panneau de fonctionnalités B2C dans une nouvelle fenêtre ou un nouvel onglet du navigateur.

Le lien **Gestion des paramètres B2C** figure également sous l’onglet **Configurer** (dans la section **Administration B2C**) .

## Inscription d'une application

1. Dans le panneau de fonctionnalités B2C sur le portail Azure en version préliminaire, cliquez sur **Applications**.
2. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
3. Le **Nom** de l’application décrit votre application pour les consommateurs. Par exemple, entrez « Application B2C Contoso ».
4. Si vous écrivez une application basée sur le web, positionnez le commutateur **Inclure l’application web/API web** sur **Oui**. Les **URL de réponse** sont des points de terminaison auxquels Azure AD B2C retourne les jetons que votre application demande. Par exemple, entrez `https://localhost:44321/`. Si votre application inclut un composant côté serveur (API) qui doit être sécurisé, vous pouvez également créer (et copier) un **Secret d’application** en cliquant sur le bouton **Générer une clé**.

    > [AZURE.NOTE]Un **secret d’application** est une information d’identification de sécurité importante.

5. Si vous écrivez une application mobile, positionnez le commutateur **Inclure un client natif** sur **Oui**. Copiez vers le bas la valeur par défaut **URI de redirection** créée automatiquement pour vous.
6. Cliquez sur **Créer** pour inscrire votre application.
7. Cliquez sur l’application que vous venez de créer, puis copiez vers le bas l’**ID d’application** global unique à utiliser ultérieurement dans votre code.

## Générer une application de démarrage rapide

À présent que vous avez une application inscrite auprès d’Azure AD B2C, vous pouvez suivre l’un de nos didacticiels de démarrage rapide afin de devenir opérationnel. Voici quelques recommandations :

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=Sept15_HO3-->