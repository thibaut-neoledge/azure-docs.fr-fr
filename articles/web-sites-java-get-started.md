<properties linkid="develop-java-tutorials-web-site-get-started" urlDisplayName="Get started with Azure" pageTitle="Get started with Microsoft Azure Web Sites using Java" metaKeywords="" description="This tutorial shows you how to deploy a Java web site to Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Get started with Azure and Java" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

Prise en main des sites Web Azure et de Java
============================================

Ce didacticiel vous présente comment créer un site Web sur Microsoft Azure avec Java, soit via la galerie d'applications Azure, soit sur l'interface utilisateur de configuration de site Web Azure.

Si vous ne souhaitez pas utiliser ces techniques (par exemple, si vous préférez personnaliser votre conteneur d'applications), voir [Téléchargement d'un site Web Java personnalisé sur Azure](../web-sites-java-custom-upload)

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez [activer les avantages de votre abonnement MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [obtenir une évaluation gratuite](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

Création d'un site Web Java avec la galerie d'applications Azure
================================================================

Ces informations vous indiquent comment utiliser la galerie d'applications Azure pour sélectionner un conteneur d'applications Java (Apache Tomcat ou Jetty) pour votre site Web.

L'exemple suivant illustre comment s'afficherait un site Web conçu avec Tomcat à partir de la galerie d'applications :

![Site Web utilisant Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

L'exemple suivant illustre comment s'afficherait un site Web conçu avec Jetty à partir de la galerie d'applications :

![Site Web utilisant Jetty](./media/web-sites-java-get-started/jetty.png)

1.  Connectez-vous au portail de gestion Microsoft Azure.
2.  Cliquez sur **New**, **Compute**, **Web Site** et **From Gallery**.
3.  Dans la liste des applications, sélectionnez un des serveurs d'application Java, tel que **Apache Tomcat** ou **Jetty**.
4.  Cliquez sur **Suivant**.
5.  Spécifiez un nom d'URL
6.  Sélectionnez une région. Par exemple, **Ouest des États-Unis**.
7.  Cliquez sur **Terminé**.

Votre site Web est créé en quelques instants. Pour afficher le site Web, au sein du portail de gestion Azure, dans la vue **Web Sites**, attendez que le statut indiqué soit **Running**, puis cliquez sur l'URL du site Web.

Maintenant que vous avez créé le site Web avec un conteneur d'applications, reportez-vous à la section **Étapes suivantes** pour retrouver les informations concernant le téléchargement de votre application vers le site Web.

Création d'un site Web Java avec l'interface utilisateur de configuration Azure
===============================================================================

Ces informations vous indiquent comment utiliser l'interface utilisateur de configuration Azure pour sélectionner un conteneur d'applications Java (Apache Tomcat ou Jetty) pour votre site Web.

1.  Connectez-vous au portail de gestion Microsoft Azure.
2.  Cliquez sur **New**, **Compute**, **Web Site**, puis **Quick Create**.
3.  Spécifiez un nom d'URL
4.  Sélectionnez une région. Par exemple, **Ouest des États-Unis**.
5.  Cliquez sur **Terminé**. Votre site Web est créé en quelques instants. Pour afficher le site Web, au sein du portail de gestion Azure, dans la vue **Web Sites**, attendez que le statut indiqué soit **Running**, puis cliquez sur l'URL du site Web.
6.  Toujours au sein du portail de gestion Azure; dans la vue **Web Sites**, cliquez sur le nom de votre site Web afin de l'ouvrir sur le tableau de bord.
7.  Cliquez sur **Configurer**.
8.  Dans la section **General**, activez **Java** en cliquant sur la version disponible.
9.  Les options pour le conteneur Web s'affichent (par exemple Tomcat et Jetty). Cliquez sur le conteneur que vous souhaitez utiliser.
10. Cliquez sur **Enregistrer**.

Votre site Web passe sous Java en quelques instants. Pour confirmer qu'il est effectivement basé sur Java, au sein du portail de gestion Azure, dans la vue **Web Sites**, attendez que le statut indiqué soit **Running**, puis cliquez sur l'URL du site Web. La page affiche un message indiquant que le nouveau site est basé sur Java.

Maintenant que vous avez créé le site Web avec un conteneur d'applications, reportez-vous à la section **Étapes suivantes** pour retrouver les informations concernant le téléchargement de votre application vers le site Web.

Étapes suivantes
================

Vous avez maintenant un serveur d'applications Java exécuté comme votre site Web Java sur Azure. Pour ajouter votre propre application ou page Web, voir [Ajout d'une application ou d'une page Web à votre site Web Java](../web-sites-java-add-app).

