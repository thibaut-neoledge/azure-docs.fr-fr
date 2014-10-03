La galerie met à votre disposition une large gamme d’applications Web développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Les applications Web créées à partir de la galerie ne requièrent pas l’installation de logiciel autre que le navigateur Web utilisé pour se connecter au portail de gestion Azure.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer un site via la galerie ;

-   déployer le site via le portail Azure.

Vous allez configurer un blog WordPress avec un modèle par défaut. L'illustration suivante présente l'application terminée :

![Blog WordPress][]

<div class="dev-callout"><strong>Remarque</strong>
<p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Vous pouvez cr&eacute;er un compte d&rsquo;&eacute;valuation gratuit en quelques minutes. Pour plus d&rsquo;informations, consultez la page <a href="http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Cr&eacute;ation d&rsquo;un compte Azure</a>.</p>
</div>

## Création d'un site web sur le portail

1.  Connectez-vous au [portail de gestion Azure][].

2.  Cliquez sur l’icône **New** dans le coin inférieur gauche du tableau de bord.

    ![Création][]

3.  Cliquez sur l'icône **Site web**, puis sur **À partir de la galerie**.

    ![Créer à partir de la galerie][]

4.  Recherchez l’icône WordPress dans la liste et cliquez dessus, puis cliquez sur **Suivant**.

    ![WordPress dans la liste][]

5.  Sur la page **Configure Your App**, entrez ou sélectionnez des valeurs pour tous les champs :

-   Entrez le nom d’URL de votre choix.
-   Sélectionnez **Create a new MySQL database** dans le champ **Database**.
-   Sélectionnez la région la plus proche de vous.

    ![Configurer une application][]

1.  Cliquez ensuite sur **Suivant**.

2.  Sur la page **Create New Database**, vous pouvez indiquer le nom de la nouvelle base de données MySQL ou utiliser le nom par défaut. Sélectionnez la région la plus proche de vous comme emplacement d’hébergement. Cochez la case en bas de l’écran pour accepter les conditions d’utilisation de ClearDB pour votre base de données MySQL hébergée. Cliquez ensuite sur la coche pour terminer la création du site.

    ![créer une base de données][]

Une fois que vous avez cliqué sur **Terminé**, Azure lance les opérations de configuration et de déploiement. Pendant la configuration et le déploiement du site web, l'état de ces opérations est affiché en bas de la page Sites web. Une fois que toutes les opérations sont effectuées, un message d’état final est affiché lorsque le site a été déployé.

## Lancement et gestion du site WordPress

1.  Cliquez sur votre nouveau site sur la page **Sites web** pour ouvrir le tableau de bord du site.

    ![lancer le tableau de bord][]

2.  Sur la page de gestion **Dashboard**, faites défiler et cliquez sur le lien sur la gauche sous **Site Url** pour ouvrir la page d’accueil du site.

    ![URL du site][]

3.  Entrez les informations de configuration appropriées requises par WordPress et cliquez sur **Installer WordPress** pour finaliser la configuration et ouvrir la page de connexion du site web.

    ![connexion à WordPress][]

4.  Connectez-vous au nouveau site web WordPress en entrant le nom d'utilisateur et le mot de passe spécifiés sur la page **Bienvenue**.

5.  Vous obtenez un nouveau site WordPress ressemblant au site ci-dessous.

    ![votre site WordPress][Blog WordPress]

  [Blog WordPress]: ./media/website-from-gallery/wordpressgallery-09.png
  [Création d’un compte Azure]: http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Création]: ./media/website-from-gallery/wordpressgallery-01.png
  [Créer à partir de la galerie]: ./media/website-from-gallery/wordpressgallery-02.png
  [WordPress dans la liste]: ./media/website-from-gallery/wordpressgallery-03.png
  [Configurer une application]: ./media/website-from-gallery/wordpressgallery-04.png
  [créer une base de données]: ./media/website-from-gallery/wordpressgallery-05.png
  [lancer le tableau de bord]: ./media/website-from-gallery/wordpressgallery-06.png
  [URL du site]: ./media/website-from-gallery/wordpressgallery-07.png
  [connexion à WordPress]: ./media/website-from-gallery/wordpressgallery-08.png
