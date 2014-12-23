<properties urlDisplayName="Get started with Azure" pageTitle="Prise en main de Sites Web Microsoft Azure avec Java" metaKeywords="" description="This tutorial shows you how to deploy a Java website to Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Get started with Azure and Java" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Prise en main de sites web Azure et de Java

Ce didacticiel vous montre comment créer un site web sur Microsoft Azure avec Java, soit via la galerie d'applications Azure, soit via l'interface utilisateur de configuration de site web Azure. 

Si vous ne souhaitez pas utiliser ces techniques (par exemple, si vous préférez personnaliser votre conteneur d'applications), consultez la page [Téléchargement d'un site web Java personnalisé sur Azure](../web-sites-java-custom-upload).

> [WACOM.NOTE]Pour effectuer ce didacticiel, vous avez besoin d'un compte Microsoft Azure. Si vous n'avez pas de compte, vous pouvez <a href="/fr-fr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activer les avantages de votre abonnement MSDN</a> ou <a href="/fr-fr/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">obtenir une évaluation gratuite</a>.

# Créer un site web Java avec la galerie d'applications Azure

Ces informations vous indiquent comment utiliser la galerie d'applications Azure pour sélectionner un conteneur d'applications Java (Apache Tomcat ou Jetty) pour votre site web.

L'exemple suivant montre comment s'affiche un site web conçu avec Tomcat à partir de la galerie d'applications :

![Web site using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

L'exemple suivant montre comment s'affiche un site web conçu avec Jetty à partir de la galerie d'applications :

![Web site using Jetty](./media/web-sites-java-get-started/jetty.png)

1. Connectez-vous au portail de gestion Microsoft Azure.
2. Cliquez sur **Nouveau**, sur **Calculer**, sur **Site web**, puis sur **À partir de la galerie**.
3. Dans la liste des applications, sélectionnez un des serveurs d'application Java, tel que **Apache Tomcat** ou **Jetty**.
4. Cliquez sur **Next**.
5. Spécifiez un nom d'URL
6. Sélectionnez une région. Par exemple, **Ouest des États-Unis**.
7. Cliquez sur **Terminé**.

Votre site web est créé en quelques instants. Pour afficher le site web, au sein du portail de gestion Azure, dans l'affichage **Sites web**, attendez que le statut indiqué soit **En cours d'exécution**, puis cliquez sur l'URL du site web.

Une fois que vous avez créé le site web avec un conteneur d'applications, consultez la section **Étapes suivantes** pour retrouver les informations concernant le téléchargement de votre application vers le site web.

# Créer un site web Java avec l'interface utilisateur de configuration Azure

Ces informations vous indiquent comment vous servir de l'interface utilisateur de configuration Azure pour sélectionner un conteneur d'applications Java (Apache Tomcat ou Jetty) pour votre site web.

1. Connectez-vous au portail de gestion Microsoft Azure.
2. Cliquez sur **Nouveau**, **Calculer**, **Site web**, puis **Création rapide**.
3. Spécifiez un nom d'URL
4. Sélectionnez une région. Par exemple, **Ouest des États-Unis**.
5. Cliquez sur **Terminé**. Votre site web est créé en quelques instants. Pour afficher le site web, au sein du portail de gestion Azure, dans l'affichage **Sites web**, attendez que le statut indiqué soit **En cours d'exécution**, puis cliquez sur l'URL du site web.
6. Toujours au sein du portail de gestion Azure, dans l'affichage **Sites Web**, cliquez sur le nom de votre site web pour ouvrir le 
tableau de bord.
7. Cliquez sur **Configurer**.
8. Dans la section **Général**, activez **Java** en cliquant sur la version disponible.
9. Les options pour le conteneur web s'affichent (par exemple Tomcat et Jetty). Cliquez sur le conteneur que vous souhaitez utiliser. 
10. Cliquez sur **Enregistrer**. 

Votre site web devient un site Java en quelques instants. Pour vérifier qu'il est effectivement basé sur Java, au sein du portail de gestion Azure, dans l'affichage **Sites Web**, attendez que le statut indiqué soit **En cours d'exécution**, puis cliquez sur l'URL du site web. La page affiche un message indiquant que le nouveau site web est basé sur Java.

Une fois que vous avez créé le site web avec un conteneur d'applications, consultez la section **Étapes suivantes** pour retrouver les informations concernant le téléchargement de votre application vers le site web.

# Étapes suivantes

Vous avez maintenant un serveur d'applications Java exécuté en tant que site web Java sur Azure. Pour ajouter votre propre application ou page web, consultez la page [Ajout d'une application ou d'une page web à votre site web Java](../web-sites-java-add-app).
