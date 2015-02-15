La galerie met à votre disposition une large gamme d'applications web développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Les applications Web créées à partir de la galerie ne requièrent pas l'installation de logiciel autre que le navigateur Web utilisé pour se connecter au portail de gestion Azure. 

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

- créer un site via la galerie ;

- déployer le site via le portail Azure.
 
Vous allez configurer un blog WordPress avec un modèle par défaut. L'illustration suivante présente l'application terminée :


![Wordpress blog][13]

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Création d'un compte Azure](http://www.windowsazure.com/fr-fr/develop/php/tutorials/create-a-windows-azure-account/"%20target="_blank").

## Création d'un site web sur le portail

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2. Cliquez sur l'icône **Nouveau** dans le coin inférieur gauche du tableau de bord.
	
	![Create New][5]

3. Cliquez sur l'icône **Site web**, puis sur **À partir de la galerie**.
	
	![Create From Gallery][6]

4. Recherchez l'icône WordPress dans la liste et cliquez dessus, puis cliquez sur **Suivant**.
	
	![WordPress from list][7]

5. Dans la page **Configurer votre application**, entrez ou sélectionnez des valeurs pour tous les champs :
	
- Entrez le nom d'URL de votre choix.	
- Laissez l'option **Créer une base de données MySQL** sélectionnée dans le champ **Base de données**.
- Sélectionnez la région la plus proche de vous.

	![configure your app][8]

6. Cliquez ensuite sur **Suivant**.

7. Dans la page **Créer une base de données**, vous pouvez indiquer le nom de la nouvelle base de données MySQL ou utiliser le nom par défaut. Sélectionnez la région la plus proche de vous comme emplacement d'hébergement. Cochez la case en bas de l'écran pour accepter les conditions d'utilisation de ClearDB pour votre base de données MySQL hébergée. Cliquez ensuite sur la coche pour terminer la création du site. 
	
	![create database][9]

Une fois que vous avez cliqué sur **Terminé**, Azure lance les opérations de configuration et de déploiement. Pendant la configuration et le déploiement du site web, l'état de ces opérations est affiché en bas de la page Sites web. Une fois que toutes les opérations sont effectuées, un message d'état final est affiché lorsque le site a été déployé.

## Lancement et gestion du site WordPress

1. Cliquez sur votre nouveau site sur la page **Sites web** pour ouvrir le tableau de bord du site.

	![launch dashboard][10]

2. Dans la page de gestion **Tableau de bord**, faites défiler et cliquez sur le lien sur la gauche sous **URL du site** pour ouvrir la page d'accueil du site.

	![site URL][11] 

3. Entrez les informations de configuration appropriées requises par WordPress et cliquez sur **Installer WordPress** pour finaliser la configuration et ouvrir la page de connexion du site web.

	![login to WordPress][12]

4. Connectez-vous au nouveau site web WordPress en entrant le nom d'utilisateur et le mot de passe spécifiés sur la page **Bienvenue**.

5. Vous obtenez un nouveau site WordPress ressemblant au site ci-dessous.  

	![your WordPress site][13]






[5]: ./media/website-from-gallery/wordpressgallery-01.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/wordpressgallery-03.png
[8]: ./media/website-from-gallery/wordpressgallery-04.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/wordpressgallery-06.png
[11]: ./media/website-from-gallery/wordpressgallery-07.png
[12]: ./media/website-from-gallery/wordpressgallery-08.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png






<!--HONumber=42-->
