<properties
	pageTitle="Inscription pour l'authentification Twitter | Microsoft Azure"
	description="Découvrez comment utiliser l'authentification Twitter avec votre application Azure Mobile Services."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/15/2015"
	ms.author="glenga"/>

#Inscrire des applications pour la connexion à Twitter avec Mobile Services

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Cette rubrique montre comment inscrire vos applications afin d'utiliser Twitter pour l'authentification auprès d'Azure Mobile Services.

>[AZURE.NOTE]Ce didacticiel concerne [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/), une solution conçue pour vous aider à créer des applications mobiles évolutives pour n'importe quelle plateforme. Grâce à Mobile Services, synchronisez des données, authentifiez des utilisateurs et envoyer des notifications Push en toute simplicité. Cette page prend en charge le didacticiel d’[Ajout d’authentification à votre application](mobile-services-ios-get-started-users.md) qui montre comment permettre aux utilisateurs de se connecter à votre application. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services](mobile-services-ios-get-started).

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Twitter avec une adresse électronique vérifiée. Pour créer un compte Twitter, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Accédez au site web [Twitter Developers](http://go.microsoft.com/fwlink/p/?LinkId=268300), connectez-vous avec vos identifiants Twitter, puis cliquez sur **Create New App**.

2. Renseignez les champs **Nom**, **Description** et **site web** pour votre application, puis entrez l’un des formats d’URL suivants dans **URL de rappel**.

	+ **Backend .NET** : `https://<mobile_service>.azure-mobile.net/signin-twitter`
	+ **Backend JavaScript** : `https://<mobile_service>.azure-mobile.net/login/twitter`

	 >[AZURE.NOTE]Assurez-vous d’utiliser le format de chemin d’accès d’URL de redirection correct pour votre type de serveur principal Mobile Services. Si ce n’est pas le cas, l’authentification échouera. &nbsp;

   	![][2]

3.  En bas de la page, lisez les conditions et acceptez-les, puis cliquez sur **Créer votre application Twitter**.

   	Cette opération inscrit l'application et affiche les détails de la demande.

6. Cliquez sur l’onglet **Clés et jetons d’accès** dans le tableau de bord de votre application et notez les valeurs **Clé client** et **Code secret client**.

    > [AZURE.NOTE]La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

7. Cliquez sur l’onglet **Paramètres**, faites défiler vers le bas et vérifiez que la case **Autoriser la connexion à Twitter via cette application** est cochée, puis cliquez sur **Mettre à jour les paramètres**.

Vous pouvez désormais utiliser une connexion Twitter pour l'authentification dans votre application en fournissant les valeurs de clé et de secret à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_1203_2015-->