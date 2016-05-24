<properties
	pageTitle="Sécurité OAUTH dans les connecteurs SaaS et les applications API | Azure"
	description="En savoir plus sur la sécurité OAUTH dans les connecteurs et les applications API dans Azure App Service ; architecture microservices ; saas"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="mandia"/>


# En savoir plus sur la sécurité OAUTH dans les connecteurs SaaS

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Nombreux sont les connecteurs SaaS, tels que Facebook, Twitter, DropBox, etc. qui nécessitent que les utilisateurs s’authentifient à l’aide du protocole OAUTH. Lorsque vous utilisez ces connecteurs SaaS à partir de Logic Apps, nous fournissons une expérience utilisateur simplifiée où vous pouvez cliquer sur « Autoriser » dans le concepteur de Logic Apps. Lorsque vous donnez votre **autorisation**, vous êtes invité à vous connecter (si ce n'est pas déjà) et à fournir votre consentement pour la connexion au service SaaS en votre nom. Une fois que vous avez donné votre consentement et votre autorisation, vos applications logiques peuvent accéder à ces services SaaS.

## Création de votre propre application SaaS
Cette expérience simplifiée est possible parce que nous avons au préalable créé et enregistré notre application dans ces services SaaS. Dans certains cas, vous souhaiterez peut-être vous inscrire et utiliser votre propre application. Cela est nécessaire, par exemple, lorsque vous souhaitez utiliser ces connecteurs SaaS dans vos applications personnalisées. Cet exemple utilise le connecteur DropBox, mais le processus est identique pour tous les connecteurs qui s’appuient sur OAUTH.

Même dans le cadre des applications logiques, vous pouvez utiliser votre propre application au lieu d'utiliser l'application par défaut que nous proposons. Si le bouton « Autoriser » ne parvient pas à établir la connexion, vous pouvez essayer de créer votre propre application. La liste suivante décrit ces étapes pour le connecteur Twitter :

1. Ouvrez votre connecteur Twitter dans le portail Azure en version préliminaire. Accédez à **Parcourir** > **Applications API**. Sélectionnez votre connecteur Twitter : ![][1]

2. Sélectionnez **Paramètres** > **Authentification** : ![][2]

3. Copiez la valeur de l'**URI de redirection** : ![][3]

4. Accédez à [Twitter](http://apps.twitter.com) et **Création d'une application**. Dans la propriété **URL de rappel**, collez la valeur de l'**URI de redirection** copiée à partir de votre connecteur Twitter : ![][4]
5. Lorsque votre application Twitter est créée, sélectionnez **Clé et jetons d'accès**. Copiez ces valeurs.
6. Dans les paramètres d'authentification du connecteur Twitter, collez ces valeurs dans les propriétés **ID client** et **Clé secrète client** : ![][5]  
7. Enregistrez les paramètres du connecteur.  

À présent, vous devez être en mesure d’utiliser votre connecteur à partir de Logic Apps. Lorsque vous utilisez ce connecteur à partir de Logic Apps, il utilise votre application au lieu de l’application par défaut.

> [AZURE.NOTE] Si vous avez précédemment autorisé une application, vous devrez peut-être l'autoriser à nouveau.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

<!---HONumber=AcomDC_0511_2016-->