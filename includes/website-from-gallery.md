La galerie met à votre disposition une large gamme d'applications Web
développées par Microsoft, par des sociétés tierces ou par des
initiatives de logiciel open source. Les applications Web créées à
partir de la galerie ne requièrent pas l'installation de logiciel autre
que le navigateur Web utilisé pour se connecter au portail de gestion
Azure.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

* créer un site via la galerie ;

* déployer le site via le portail Azure.

Vous allez configurer un blog WordPress avec un modèle par défaut.
L'illustration suivante présente l'application terminée :

![Blog WordPress](./media/website-from-gallery/wordpressgallery-09.png)

<div class="dev-callout"><strong>Remarque</strong>
<p>Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page <a  href="http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Création d’un compte Azure</a>.</p>
</div>
## Création d'un site Web dans le portail

1.  Connectez-vous au [portail de gestion Azure][1].

2.  Cliquez sur l'icône **New** dans le coin inférieur gauche du tableau
    de bord.
    
    ![Création](./media/website-from-gallery/wordpressgallery-01.png)

3.  Cliquez sur l'icône **Web Site**, puis sur **From Gallery**.
    
    ![Créer à partir de la
    galerie](./media/website-from-gallery/wordpressgallery-02.png)

4.  Recherchez l'icône WordPress dans la liste et cliquez dessus, puis
    cliquez sur **Suivant**.
    
    ![WordPress dans la
    liste](./media/website-from-gallery/wordpressgallery-03.png)

5.  Sur la page **Configure Your App**, entrez ou sélectionnez des
    valeurs pour tous les champs :

* Entrez le nom d'URL de votre choix.
* Sélectionnez **Create a new MySQL database** dans le champ
  **Database**.
* Sélectionnez la région la plus proche de vous.
  
    ![Configurer une
  application](./media/website-from-gallery/wordpressgallery-04.png)

1.  Cliquez ensuite sur **Suivant**.

2.  Sur la page **Create New Database**, vous pouvez indiquer le nom de
    la nouvelle base de données MySQL ou utiliser le nom par défaut.
    Sélectionnez la région la plus proche de vous comme emplacement
    d'hébergement. Cochez la case en bas de l'écran pour accepter les
    conditions d'utilisation de ClearDB pour votre base de données MySQL
    hébergée. Cliquez ensuite sur la coche pour terminer la création du
    site.
    
    ![créer une base de
    données](./media/website-from-gallery/wordpressgallery-05.png)

Une fois que vous avez cliqué sur **Terminé**, Azure lance les
opérations de configuration et de déploiement. Pendant la configuration
et le déploiement du site Web, l'état de ces opérations est affiché en
bas de la page Sites Web. Une fois que toutes les opérations sont
effectuées, un message d'état final est affiché lorsque le site a été
déployé.
## Lancement et gestion du site WordPress

1.  Cliquez sur le nouveau site sur la page **Web Sites** pour ouvrir le
    tableau de bord du site.
    
    ![lancer le tableau de
    bord](./media/website-from-gallery/wordpressgallery-06.png)

2.  Sur la page de gestion **Dashboard**, faites défiler et cliquez sur
    le lien sur la gauche sous **Site Url** pour ouvrir la page
    d'accueil du site.
    
    ![URL du site](./media/website-from-gallery/wordpressgallery-07.png)

3.  Entrez les informations de configuration appropriées requises par
    WordPress et cliquez sur **Installer WordPress** pour finaliser la
    configuration et ouvrir la page de connexion du site Web.
    
    ![connexion à
    WordPress](./media/website-from-gallery/wordpressgallery-08.png)

4.  Connectez-vous au nouveau site Web WordPress en entrant le nom
    d'utilisateur et le mot de passe spécifiés sur la page
    **Bienvenue**.

5.  Vous obtenez un nouveau site WordPress ressemblant au site
    ci-dessous.
    
    ![votre site
    WordPress](./media/website-from-gallery/wordpressgallery-09.png)



[1]: http://manage.windowsazure.com
