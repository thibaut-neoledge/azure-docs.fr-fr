<properties
	pageTitle="Prise en main d’Azure Search dans Java | Microsoft Azure | Service de recherche cloud hébergé"
	description="Comment créer une application de recherche hébergée dans le cloud sur Azure en utilisant le langage de programmation Java."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Prise en main d'Azure Search dans Java

Apprenez à créer une application Java personnalisée, qui utilise Azure Search pour ses fonctionnalités de recherche. Ce didacticiel utilise l’[API REST du service Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) pour créer les objets et opérations utilisés dans cet exercice.

Nous avons utilisé les logiciels suivants pour générer et tester cet exemple :

- [Environnement de développement intégré (IDE) Eclipse pour développeurs Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Veillez à télécharger la version EE. Une des étapes de vérification nécessite une fonctionnalité présente uniquement dans cette édition.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

Pour exécuter cet exemple, vous devez disposer d’un service Azure Search, auquel vous pouvez vous connecter dans le [Portail Azure Classic](https://portal.azure.com).

> [AZURE.TIP]Téléchargez le code source de ce didacticiel dans la [démonstration Java d'Azure Search](http://go.microsoft.com/fwlink/p/?LinkId=530197).

## À propos des données

Cet exemple d'application utilise des données de l’[USGS (United States Geological Services)](http://geonames.usgs.gov/domestic/download_data.htm), concernant l'État de Rhode Island pour réduire la taille du jeu de données. Nous allons utiliser ces données pour créer une application de recherche qui renvoie des bâtiments importants, tels que les hôpitaux et les écoles, ainsi que des caractéristiques géologiques, telles que les ruisseaux, les lacs et les sommets.

Dans cette application, le programme **SearchServlet.java** crée et charge l'index à l'aide d'une construction de type [Index](https://msdn.microsoft.com/library/azure/dn798918.aspx), en récupérant le jeu de données USGS filtré à partir d’une base de données SQL Azure publique. Les informations d'identification et de connexion à la source de données en ligne sont fournies dans le code du programme. Pour accéder aux données, aucune configuration supplémentaire n'est nécessaire.

> [AZURE.NOTE]Nous avons filtré ce jeu de données pour ne pas dépasser la limite de 10 000 documents du niveau de tarification gratuit. Si vous utilisez le niveau standard, cette limite ne s'applique pas et vous pouvez modifier ce code pour utiliser un jeu de données plus important. Pour plus d'informations sur la capacité de chaque niveau de tarification, consultez la section [Limites et contraintes](search-limits-quotas-capacity.md).

## À propos des fichiers de programme

La liste suivante décrit les fichiers qui sont pertinents pour cet exemple.

- Search.jsp : fournit l'interface utilisateur.
- SearchServlet.java : fournit des méthodes (semblables à un contrôleur dans MVC).
- SearchServiceClient.java : gère les descripteurs HTTP.
- SearchServiceHelper.java : classe d’utilitaire qui fournit des méthodes statiques.
- Document.Java : fournit le modèle de données.
- config.properties : définit la clé API et l'URL du service de recherche.
- Pom.XML : dépendance Maven.


## Créer le service

1. Connectez-vous au [Portail Azure Classic](https://portal.azure.com).

2. Dans la barre de lancement, cliquez sur **Nouveau** > **Données + stockage** > **Recherche**.

     ![][1]

3. Définissez le nom du service, le niveau de tarification, le groupe de ressources, l'abonnement et l'emplacement. Ces paramètres sont requis et ne sont plus modifiables une fois le service approvisionné.

     ![][2]

	- Le **nom du service** doit être unique, en minuscules et contenir moins de 15 caractères sans espaces. Il devient partie intégrante du point de terminaison de votre service Azure Search. Consultez [Règles d'affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) pour plus d'informations sur les conventions d'affectation des noms.

	- Le **niveau de tarification** détermine la capacité et la facturation. Les deux niveaux offrent les mêmes fonctionnalités, mais à des niveaux de ressources différents.

		- La **version gratuite** s'applique aux clusters partagés avec d'autres abonnés. Elle offre une capacité suffisante pour tester les didacticiels et écrire du code de validation technique, mais elle n'est pas destinée aux applications de production. En général, le déploiement d'un service gratuit ne prend que quelques minutes.
		- La version **standard** s'applique aux ressources dédiées et est hautement évolutive. À l'origine, un service standard est fourni avec un réplica et une partition, mais vous pouvez ajuster la capacité une fois le service créé. Le déploiement d'un service standard prend plus de temps, environ quinze minutes.

	- Les **groupes de ressources** sont des conteneurs pour les services et les ressources utilisés dans un but commun. Par exemple, si vous créez une application de recherche personnalisée basée sur Azure Search, Sites web Azure ou le stockage d'objets blob, vous pouvez créer un groupe de ressources qui réunit ces services dans les pages de gestion du portail.

	- L’**abonnement** vous permet de choisir votre abonnement, si vous en avez plusieurs.

	- L’**emplacement** correspond à la zone géographique du centre de données. Actuellement, toutes les ressources doivent s'exécuter dans le même centre de données. La répartition de ressources entre plusieurs centres de données n'est pas prise en charge.

4. Cliquez sur **Créer** pour approvisionner le service.

Surveillez les notifications dans la barre de lancement. Une notification s'affiche lorsque le service est prêt à être utilisé.

<a id="sub-2"></a>
## Recherche du nom et de la clé API de votre service Azure Search

Une fois le service créé, vous pouvez revenir au portail pour obtenir l'URL et la `api-key`. Pour vous connecter à votre service de recherche, vous devez saisir l'URL et une `api-key` afin d’authentifier l'appel.

1. Dans la barre de lancement, cliquez sur **Accueil** puis sur le service de recherche pour ouvrir le tableau de bord du service.

2. Le tableau de bord des services affiche des vignettes contenant des informations essentielles, ainsi que l'icône de clé permettant d'accéder aux clés administrateur.

  	![][3]

3. Copiez l'URL du service et une clé d’administration. Vous en aurez besoin plus tard, pour les ajouter au fichier **config.properties**.

## Téléchargement des fichiers exemples

1. Accédez à [AzureSearchJavaDemo](http://go.microsoft.com/fwlink/p/?LinkId=530197) sur Github.

2. Cliquez sur **Download ZIP**, enregistrez le fichier ZIP sur le disque, puis extrayez tous les fichiers qu'il contient. Si vous le souhaitez, vous pouvez extraire ces fichiers dans votre espace de travail Java pour faciliter la recherche du projet ultérieurement.

3. Les fichiers exemples sont en lecture seule. Avec le bouton droit de la souris, cliquez sur le dossier et désactivez l'attribut de lecture seule.

Toutes les modifications et instructions d'exécution ultérieures seront effectuées sur les fichiers de ce dossier.

## Importer le projet

1. Dans Eclipse, choisissez **File** > **Import** > **General** > **Existing Projects into Workspace**.

    ![][4]

2. Dans **Select root directory**, accédez au dossier contenant les fichiers exemples. Sélectionnez le dossier qui contient le dossier .project. Le projet doit s'afficher sélectionné dans la liste **Projects**.

    ![][12]

3. Cliquez sur **Terminer**.

4. Utilisez **Project Explorer** pour afficher et modifier les fichiers. Si l’explorateur n’est pas ouvert, cliquez sur **Window** > **Show View** > **Project Explorer** ou utilisez le raccourci.

## Configurer l'URL et la clé API du service

1. Dans **Project Explorer**, double-cliquez sur **config.properties** pour modifier les paramètres de configuration qui contient le nom du serveur et la clé API.

2. Reportez-vous aux étapes précédentes dans cet article, où vous avez trouvé l’URL et la clé API du service dans le [Portail Azure Classic](https://portal.azure.com), afin d’obtenir les valeurs que vous devez maintenant saisir dans **config.properties**.

3. Dans **config.properties**, remplacez « Api Key » par la clé API de votre service. Ensuite, le nom du service (le premier composant de l'URL http://servicename.search.windows.net) remplace « ServiceName » dans le même fichier.

	![][5]

## Configuration des environnements de projet, de génération et d'exécution

1. Dans Project Explorer dans Eclipse, cliquez avec le bouton droit sur le projet, puis cliquez sur **Properties** > **Project Facets**.

2. Sélectionnez **Dynamic Web Module**, **Java** et **JavaScript**.

    ![][6]

3. Cliquez sur **Apply**.

4. Sélectionnez **Window** > **Preferences** > **Server** > **Runtime Environments** > **Add...**.

5. Développez Apache et sélectionnez la version du serveur Apache Tomcat que vous avez précédemment installée. Sur notre système, nous avons installé la version 8.

	![][7]

6. Dans la page suivante, spécifiez le répertoire d'installation de Tomcat. Sur un ordinateur Windows, il s'agit très probablement du répertoire C:\\Program Files\\Apache Software Foundation\\Tomcat *version*.

6. Cliquez sur **Terminer**.

7. Sélectionnez **Window** > **Preferences** > **Java** > **Installed JREs** > **Add**.

8. Dans **Add JRE**, sélectionnez **Standard VM**.

10. Cliquez sur **Next**.

11. Dans JRE Definition de la page d’accueil de JRE, cliquez sur **Directory**.

12. Accédez à **Program Files** > **Java** et sélectionnez le JDK que vous avez installé précédemment. Il est important de sélectionner le JDK comme JRE.

13. Dans Installed JREs, sélectionnez le **JDK**. Vos paramètres doivent être similaires au contenu de la capture d'écran suivante.

    ![][9]

14. Si vous le souhaitez, sélectionnez **Window** > **Web Browser** > **Internet Explorer** pour ouvrir l’application dans un navigateur externe. Un navigateur externe vous donne une meilleure expérience de l’application Web.

    ![][8]

La phase de configuration est maintenant terminée. Vous allez maintenant créer et exécuter le projet.

## Créer le projet

1. Dans Project Explorer, cliquez avec le bouton droit sur le nom du projet et sélectionnez **Run As** > **Maven build...** pour configurer le projet.

    ![][10]

8. Dans le champ Goals de Edit Configuration, saisissez « clean install », puis cliquez sur **Run**.

Des messages d'état s’affichent dans la fenêtre de la console. Le message BUILD SUCCESS indique que le projet a été généré sans erreur.

## Exécution de l'application

Dans cette dernière étape, vous allez exécuter l'application dans un environnement d'exécution de serveur local.

Si ce n’est déjà fait, vous devez spécifier un environnement d'exécution de serveur dans Eclipse.

1. Dans Project Explorer, développez **WebContent**.

5. Cliquez avec le bouton droit sur **Search.jsp**, puis sélectionnez **Run As** > **Run on Server**. Sélectionnez le serveur Apache Tomcat, puis cliquez sur **Run**.

> [AZURE.TIP]Si vous avez stocké votre projet dans un espace de travail personnalisé, vous devrez désigner cet emplacement dans **Run Configuration** pour éviter une erreur lors du démarrage du serveur. Dans Project Explorer, cliquez avec le bouton droit sur **Search.jsp**, puis cliquez sur **Run As** > **Run Configurations**. Sélectionnez le serveur Apache Tomcat. Cliquez sur **Arguments**. Cliquez sur **Workspace** ou **File System** pour définir le dossier contenant le projet.

Lorsque vous exécutez l'application, une fenêtre de navigateur affiche un champ de recherche permettant d'entrer des termes.

Attendez environ une minute avant de cliquer sur **Search**, le temps que le service crée et charge l'index. Si vous obtenez une erreur HTTP 404, Patientez encore un peu avant de réessayer.

## Exécution d'une recherche sur les données USGS

Le jeu de données USGS comprend les enregistrements qui correspondent à l'État de Rhode Island. Si vous cliquez sur **Search** et que le champ de recherche est vide, vous obtiendrez les 50 premières entrées, ce qui correspond au paramétrage par défaut.

Saisissez un terme pour que le moteur de recherche puisse travailler. Essayez d'entrer le nom d’une figure locale. « Roger Williams » fut le premier gouverneur de Rhode Island. De nombreux parcs, bâtiments et écoles portent son nom.

![][11]

Vous pouvez également essayer les termes suivants :

- Pawtucket
- Pembroke
- goose +cape

## Étapes suivantes

Ceci est le premier didacticiel Azure Search basé sur Java et le jeu de données USGS. Au fil du temps, nous le compléterons avec des fonctionnalités de recherche supplémentaires que vous souhaiterez peut-être utiliser dans vos solutions personnalisées.

Si vous avez déjà une certaine maîtrise d’Azure Search, vous pouvez utiliser cet exemple comme un tremplin pour d’autres expérimentations, notamment pour améliorer la [page de recherche](search-pagination.md) ou mettre en place la [navigation par facettes](search-faceted-navigation.md). Vous pouvez également améliorer la page des résultats en lui ajoutant des compteurs et en traitant les documents par lots afin que les utilisateurs puissent parcourir les résultats.

Vous découvrez Azure Search ? Nous vous recommandons de suivre les autres didacticiels pour comprendre ce que vous pouvez créer. Consultez les autres ressources disponibles dans notre [page de documentation](http://azure.microsoft.com/documentation/services/search/). Vous pouvez également cliquer sur les liens dans notre [liste de vidéos et de didacticiels](search-video-demo-tutorial-list.md) pour obtenir des informations supplémentaires.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png

<!---HONumber=Nov15_HO4-->