<properties 
	pageTitle="Prise en main de Microsoft Sites Web Azure avec Java" 
	description="Ce didacticiel vous montre comment déployer un site Web Java dans Microsoft Azure." 
	services="web-sites" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Prise en main de sites Web Azure et de Java

Ce didacticiel vous montre comment créer un site Web sur Microsoft Azure avec Java, soit via la galerie d'applications Azure, soit via l'interface utilisateur de configuration de site Web Azure. 

Si vous ne souhaitez pas utiliser ces techniques (par exemple, si vous préférez personnaliser votre conteneur d'applications), consultez [Téléchargement d'un site Web Java personnalisé sur Azure](../web-sites-java-custom-upload).

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Microsoft Azure. Si vous ne possédez pas de compte, vous pouvez <a href="/fr-fr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activer les avantages de votre abonnement MSDN</a> ou <a href="/fr-fr/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">obtenir une évaluation gratuite</a>. 
> 
> Si vous voulez prendre en main Sites Web Azure avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/?language=java">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Sites Web Azure. Aucun numéro de carte de crédit, ni engagement de quelque sorte ne sont exigés.

# Créer un site Web Java avec la galerie d'applications Azure

Ces informations vous indiquent comment utiliser la galerie d'applications Azure pour sélectionner un conteneur d'applications Java (Apache Tomcat ou Jetty) pour votre site Web.

L'exemple suivant montre comment s'affiche un site Web conçu avec Tomcat à partir de la galerie d'applications :

![Web site using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

L'exemple suivant montre comment s'affiche un site Web conçu avec Jetty à partir de la galerie d'applications :

![Web site using Jetty](./media/web-sites-java-get-started/jetty.png)

1. Connectez-vous au portail de gestion Microsoft Azure.
2. Cliquez sur **Nouveau**, sur **Calculer**, sur **Site Web**, puis sur **À partir de la galerie**.
3. Dans la liste des applications, sélectionnez l'un des serveurs d'applications Java, tels que **Apache Tomcat** ou **Jetty**.
4. Cliquez sur **Suivant**.
5. Spécifiez un nom d'URL
6. Sélectionnez une région. Par exemple, **Ouest des États-Unis**.
7. Cliquez sur **Terminé**.

Votre site Web est créé en quelques instants. Pour afficher le site Web, sur le portail de gestion Azure, dans l'affichage **Sites Web**, attendez que le statut indiqué soit **En cours d'exécution**, puis cliquez sur l'URL du site Web.

Une fois que vous avez créé le site Web avec un conteneur d'applications, consultez la section **Étapes suivantes** pour retrouver les informations concernant le téléchargement de votre application vers le site Web.

# Créer un site Web Java avec l'interface utilisateur de configuration Azure

Ces informations vous indiquent comment vous servir de l'interface utilisateur de configuration Azure pour sélectionner un conteneur d'applications Java (Apache Tomcat ou Jetty) pour votre site Web.

1. Connectez-vous au portail de gestion Microsoft Azure.
2. Cliquez sur **Nouveau**, **Calculer**, **Site Web**, puis **Création rapide**.
3. Spécifiez un nom d'URL
4. Sélectionnez une région. Par exemple, **Ouest des États-Unis**.
5. Cliquez sur **Terminé**. Votre site Web est créé en quelques instants. Pour afficher le site Web, sur le portail de gestion Azure, dans l'affichage **Sites Web**, attendez que le statut indiqué soit **En cours d'exécution**, puis cliquez sur l'URL du site Web.
6. Toujours au sein du portail de gestion Azure, dans l'affichage **Sites Web**, cliquez sur le nom de votre site Web pour ouvrir le tableau de bord.
7. Cliquez sur **Configurer**.
8. Dans la section **Général**, activez **Java** en cliquant sur la version disponible.
9. Les options pour le conteneur Web s'affichent (par exemple Tomcat et Jetty). Cliquez sur le conteneur que vous souhaitez utiliser. 
10. Cliquez sur **Enregistrer**. 

Votre site Web devient un site Java en quelques instants. Pour vérifier qu'il est effectivement basé sur Java, sur le portail de gestion Azure, dans l'affichage **Sites Web**, attendez que le statut indiqué soit **En cours d'exécution**, puis cliquez sur l'URL du site Web. La page affiche un message indiquant que le nouveau site Web est basé sur Java.

Une fois que vous avez créé le site Web avec un conteneur d'applications, consultez la section **Étapes suivantes** pour retrouver les informations concernant le téléchargement de votre application vers le site Web.

# Étapes suivantes

Vous avez maintenant un serveur d'applications Java exécuté en tant que site Web Java sur Azure. Pour ajouter votre propre application ou page Web, consultez [Ajout d'une application ou d'une page Web à votre site Web Java](../web-sites-java-add-app).


<!--HONumber=42-->
