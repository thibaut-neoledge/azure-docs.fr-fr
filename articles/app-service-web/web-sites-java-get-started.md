<properties
	pageTitle="Création d’une application web Java dans Azure App Service | Microsoft Azure"
	description="Ce didacticiel vous explique comment déployer une application web Java dans Azure App Service."
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="01/09/2016"
	ms.author="robmcm"/>

# Créer une application web Java dans Azure App Service

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

Ce didacticiel vous explique comment créer une [application web Java dans Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) à l’aide du [portail Azure](https://portal.azure.com/). Le portail Azure est une interface web que vous pouvez utiliser pour gérer les ressources Azure.

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous ne possédez pas d’un compte, vous pouvez [activer les avantages de votre abonnement Visual Studio][] ou [obtenir un essai gratuit][].
>
> Si vous souhaitez commencer à utiliser Azure App Service avant d’ouvrir un compte Azure, accédez à [Essayer App Service][]. Là, vous pouvez créer immédiatement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ni aucun engagement.

## Options pour les applications Java

Vous pouvez utiliser différentes méthodes pour configurer une application Java dans une application web App Service.

1. Créer une application, puis configure les **paramètres de l’application**

	App Service fournit plusieurs versions de Tomcat et Jetty configurées par défaut. Si l’application que vous allez héberger doit fonctionner avec l’une des versions intégrées, cette méthode de configuration d’un conteneur web sera la plus simple à utiliser lorsque vous voulez simplement télécharger un fichier war dans un conteneur web. Pour cette méthode, créez une application dans le portail Azure et accédez au panneau **Paramètres de l’application** pour choisir votre version de Java ainsi que le conteneur web Java souhaité. Lorsque vous utilisez cette méthode, Java et le conteneur web sont tous les deux exécutés à partir des fichiers de programme. Les autres méthodes placent le conteneur et éventuellement, la machine virtuelle Java dans l’espace disque. Lorsque vous utilisez ce modèle, vous ne disposez pas d’un accès en modification dans cette partie du système de fichiers, ce qui signifie par exemple que vous ne pouvez pas configurer le fichier *server.xml* ou placer des fichiers de bibliothèque dans le dossier */lib*. Pour plus d’informations, consultez la section [Créer et configurer une application web Java](#appsettings) de ce didacticiel.
	
2. Utiliser un modèle à partir d’Azure Marketplace

	Azure Marketplace contient des modèles permettant de créer et configurer automatiquement des applications web Java avec les conteneurs web Tomcat ou Jetty. Les conteneurs web définis par les modèles sont configurables. Pour plus d’informations, consultez la section [Utiliser un modèle Java à partir d’Azure Marketplace](#marketplace) de ce didacticiel.
 
  
3. Créer une application, puis copier et modifier manuellement les fichiers de configuration

	Vous pouvez héberger une application Java personnalisée qui ne se déploiera pas dans un des conteneurs web fournis par App Service. Voici quelques raisons qui pourraient vous amener à adopter cette approche :
	
	* Votre application Java nécessite une version de Tomcat ou Jetty qui n’est pas directement prise en charge par App Service ou n’est pas fournie dans la galerie.
	* Votre application Java collecte les demandes HTTP et ne se déploie pas en tant que WAR dans un conteneur web existant.
	* Vous souhaitez configurer vous-même le conteneur web de A à Z. 
	* Vous souhaitez utiliser une version de Java qui n’est pas pris en charge dans App Service et voulez la télécharger vous-même.

	Dans ces cas-là, vous pouvez créer une application à l’aide du portail Azure et fournir ensuite manuellement les fichiers d’exécution appropriés. Dans ce cas, les fichiers seront comptabilisés par rapport à vos quotas d’espace de stockage associé à votre plan App Service. Pour plus d’informations, consultez [Charger une application web Java personnalisée dans Azure](https://acom-sandbox.azurewebsites.net/fr-FR/documentation/articles/web-sites-java-custom-upload/).

## <a name="portal"></a> Créer et configurer une application web Java

Cette section explique comment créer une application web et la configurer pour Java à l’aide du panneau **Paramètres de l’application** du portail.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Cliquez sur **Nouveau > Web + Mobile > Application web**.

	![](./media/web-sites-java-get-started/newwebapp.png)

4. Entrez un nom pour l’application web dans la zone **Application web**.

	Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera {nom}.azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge s’affiche dans la zone de texte.

5. Sélectionnez un **Groupe de ressources** ou créez-en un.

	Pour plus d’informations sur les groupes de ressources, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](../resource-group-portal.md).

6. Sélectionnez un **plan App Service/emplacement** ou créez-en un.

	Pour plus d’informations sur les plans App Service, consultez [Présentation des plans d’Azure App Service](../azure-web-sites-web-hosting-plans-in-depth-overview.md).

7. Cliquez sur **Create**.

	![](./media/web-sites-java-get-started/newwebapp2.png)
 
8. Une fois l’application web créée, cliquez sur **Applications web > {votre application web}**.
 
	![](./media/web-sites-java-get-started/selectwebapp.png)

9. Dans le panneau **Application web**, cliquez sur **Paramètres**.

10. Cliquez sur **Paramètres de l’application**.

11. Sélectionnez la **version Java** souhaitée.

12. Sélectionnez la **version Java mineure** souhaitée. Si vous sélectionnez **La plus récente**, votre application utilisera la dernière version mineure disponible dans App Service pour cette version majeure de Java. L’élément le **plus récent** est unique pour les applications Java créées à partir de **Paramètres d’Application**. Si vous créez votre application Java à partir de la galerie, vous devez gérer votre propre conteneur et les modifications de la machine virtuelle Java.

12. Sélectionnez le **conteneur web** souhaité. Si vous sélectionnez un nom de conteneur qui commence par **La plus récente**, votre application sera maintenue à la dernière version de cette version majeure du conteneur web disponible dans App Service.

	![](./media/web-sites-java-get-started/versions.png)

13. Cliquez sur **Enregistrer**.

	Dans quelques instants, votre application web basculera sur Java et sera configurée pour utiliser le conteneur sélectionné.

14. Cliquez sur **Applications web > {votre nouvelle application web}**.

15. Cliquez sur l’**URL** afin d’accéder au nouveau site.

	La page web confirme que vous avez créé une application web basée sur Java.


## <a name="marketplace"></a> Utiliser un modèle Java à partir d’Azure Marketplace

Cette section vous montre comment utiliser Microsoft Azure Marketplace pour créer une application web Java. Le même processus général peut également servir à créer une application mobile ou API Java.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Cliquez sur **Nouveau > Marketplace**.

	![](./media/web-sites-java-get-started/newmarketplace.png)

3. Cliquez sur **web + Mobile**.

	Il vous faudra éventuellement faire défiler le contenu sur la gauche pour afficher le panneau **Marketplace**, à partir duquel vous pouvez sélectionner **web + Mobile**.

4. Dans la zone textuelle de recherche, saisissez le nom d’un serveur d’application Java, comme **Apache Tomcat** ou **Jetty**, puis appuyez sur Entrée.

5. Dans les résultats de recherche, cliquez sur le serveur d'applications Java.

	![](./media/web-sites-java-get-started/webmobilejetty.png)

6. Dans le premier panneau **Apache Tomcat** ou **Jetty**, cliquez sur **Créer**.

	![](./media/web-sites-java-get-started/jettyblade.png)

7. Dans le panneau **Apache Tomcat** ou **Jetty** suivant, attribuez un nom à l’application web dans la zone **Application web**.

	Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera {nom}.azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge s’affiche dans la zone de texte.

8. Sélectionnez un **Groupe de ressources** ou créez-en un.

	Pour plus d’informations sur les groupes de ressources, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](../resource-group-portal.md).

9. Sélectionnez un **plan App Service/emplacement** ou créez-en un.

	Pour plus d’informations sur les plans App Service, consultez [Présentation des plans d’Azure App Service](../azure-web-sites-web-hosting-plans-in-depth-overview.md).

10. Cliquez sur **Create**.

	![](./media/web-sites-java-get-started/jettyportalcreate2.png)

	Azure termine la création de la nouvelle application web dans un délai bref, généralement inférieur à une minute.

11. Cliquez sur **Applications web > {votre nouvelle application web}**.

12. Cliquez sur l’**URL** afin d’accéder au nouveau site.

	![](./media/web-sites-java-get-started/jettyurl.png)

	Tomcat s’accompagne d’un ensemble de pages par défaut. Si vous avez sélectionné Tomcat, vous obtiendrez donc une page semblable à l’exemple suivant.

	![Application web utilisant Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

	Si vous avez sélectionné Jetty, une page similaire à l’exemple suivant s’affiche. Jetty ne possède pas d’ensemble de pages par défaut. Le JSP utilisé pour un site Java vide sera donc réutilisé ici.

	![Application web utilisant Jetty](./media/web-sites-java-get-started/jetty.png)

Maintenant que vous avez créé l’application web avec un conteneur d’applications, consultez la section [Étapes suivantes](#next-steps) pour accéder aux informations concernant le chargement de votre application dans l’application web.


## Étapes suivantes

À ce stade, vous disposez d’un serveur d’applications Java s’exécutant dans votre application web, dans Azure App Service. Pour déployer votre propre code dans l’application web, consultez la page [Ajout d’une application ou d’une page web à votre application web Java](web-sites-java-add-app.md).

Pour plus d’informations sur le développement des applications Java dans Azure, consultez le [Centre de développement Java](/develop/java/).

<!-- External Links -->
[activer les avantages de votre abonnement Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[obtenir un essai gratuit]: http://go.microsoft.com/fwlink/?LinkId=623901

[Essayer App Service]: http://go.microsoft.com/fwlink/?LinkId=523751

<!---HONumber=AcomDC_0128_2016-->