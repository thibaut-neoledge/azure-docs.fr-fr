<properties
	pageTitle="Modèle d’application v2.0 | Microsoft Azure"
	description="Inscription d’une application auprès de Microsoft pour l’activation de la connexion et l’intégration des applications au modèle d’application v2.0."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Version préliminaire du modèle d’application v2.0 : Inscription d’une application auprès de Microsoft

Pour générer une application prenant en charge à la fois les connexions à MSA et Azure AD, vous devez d’abord l’inscrire auprès de Microsoft. Si vous ne pouvez pas utiliser une application existante avec Azure AD ou MSA, il est temps d’en créer une.

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration au service Azure AD mis à la disposition générale, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

## Visiter le portail d’inscription des applications Microsoft
Commencez tout d’abord par accéder à [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com). Il s’agit du nouveau portail d’inscription des applications où vous pouvez gérer absolument tout sur vos applications Microsoft.

Connectez-vous à l’aide d’un compte Microsoft personnel, professionnel ou scolaire. Si vous en êtes dépourvu, inscrivez-vous pour obtenir un nouveau compte personnel. Cela ne sera pas long. Nous patientons ici.

Vous avez terminé ? À présent, consultez votre liste d’applications Microsoft, qui est probablement vide. Nous allons y remédier.

<!-- TODO: Verify strings here -->
Cliquez sur **Ajouter une application**, et attribuez-lui un nom. Le portail attribue à votre application un ID d’application global unique que vous utiliserez ultérieurement dans votre code. Si votre application inclut un composant côté serveur, qui nécessite des jetons d’accès pour appeler des API (à savoir Office, Azure ou tierces), créez également ici un \*\*secret d’application\*\*. <!-- TODO: Link for app secrets -->

Ajoutez ensuite les plateformes que votre application utilisera. - Pour les applications web, indiquez un **URI de redirection** où les messages de connexion peuvent être envoyés. - Dans le cas des applications mobiles, copiez l’URI de redirection par défaut créé automatiquement pour vous.

Si vous le souhaitez, vous pouvez personnaliser l’apparence de votre page de connexion dans la section Profil. Avant de continuer, veillez à cliquer sur **Enregistrer**.

## Générer une application de démarrage rapide
Maintenant que vous disposez d’une application Microsoft, vous pouvez suivre l’un de nos didacticiels de démarrage rapide pour prendre en main le modèle d’application v2.0. Voici quelques recommandations :

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=August15_HO7-->