# Création d'une base de données MySQL dans Azure

Ce guide présente l'utilisation de [ClearDB][1] pour créer une base de
données MySQL à partir de l'[Azure Store](/en-us/store/overview/) et la
création d'une base de données MySQL comme ressource liée lors de la
création d'un [site Web Azure](/en-us/manage/services/web-sites/).
[ClearDB][1] est un fournisseur de services database-as-a-service à
tolérance de panne permettant d'exécuter et de gérer des bases de
données MySQL dans les centres de données Azure et de s'y connecter à
partir d'une application.
## Sommaire

* [Création d'une base de données MySQL à partit de l'Azure
  Store](#CreateFromStore)
* [Création d'une base de données MySQL comme ressource liée pour un
  site Web Azure](#CreateForWebSite)

 
<div  class="dev-callout"> 
<b>Remarque</b> 
<p>Lors de la création d’une base de données MySQL pendant le processus de création du site Web, vous pouvez uniquement créer une base de données gratuite. La création d’une base de données MySQL à partir de l’Azure Store vous permet de créer une base de données gratuite ou de choisir une des options payantes.</p> 
</div>

 <h2><a  id="CreateFromStore" ></a>Création d'une base de données MySQL à partit de l'Azure Store</h2>


Pour créer une base de données MySQL à partir de l'[Azure
Store](/en-us/store/overview/), procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][2].
2.  Cliquez sur **+NEW** en bas de la page, puis sélectionnez **STORE**.
    
    ![Sélectionner un module complémentaire dans le
    marketplace](./media/create-mysql-db/select-store.png)

3.  Sélectionnez **ClearDB MySQL Database**, puis cliquez sur la flèche
    en bas du cadre.
    
    ![Sélectionner la base de données MySQL
    ClearDB](./media/create-mysql-db/select-cleardb-mysql.png)

4.  Sélectionnez un plan, indiquez le nom de la base de données,
    sélectionnez une région, puis cliquez sur la flèche en bas du cadre.
    
    ![Acheter une base de données MySQL dans le
    marketplace](./media/create-mysql-db/purchase-mysql.png)

5.  Cliquez sur la coche pour effectuer l'achat.
    
    ![Passer en revue et terminer
    l’achat](./media/create-mysql-db/complete-mysql-purchase.png)

6.  Une fois la base de données créée, vous pouvez la gérer à partir de
    l'onglet **ADD-ONS** du portail de gestion.
    
    ![Gérer une base de données MySQL dans le portail
    Azure](./media/create-mysql-db/manage-mysql-add-on.png)

7.  Pour obtenir les informations de connexion à la base de données,
    cliquez sur **CONNECTION INFO** en bas de la page (comme illustré
    ci-dessus).
    
    ![Informations de connexion
    MySql](./media/create-mysql-db/mysql-conn-info.png)

<h2><a  id="CreateForWebSite" ></a>Création d'une base de données MySQL comme ressource liée pour un site Web Azure</h2>


Pour créer une base de données MySQL comme ressource liée lors de la
création d'un [site Web Azure](/en-us/manage/services/web-sites/),
procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][2].
2.  Cliquez sur **+NEW** en bas de la page, puis sélectionnez
    **COMPUTE**, **WEB SITE** et **CREATE WITH DATABASE**.
    
    ![Créer un site Web avec une base de
    données](./media/create-mysql-db/custom_create.png)

3.  Indiquez une **URL** pour le site Web, sélectionnez une **RÉGION**,
    puis choisissez **Create a new MySQL database** dans la liste
    déroulante **DATABASE**. Vous pouvez éventuellement remplacer le nom
    par défaut par la chaîne de connexion. Cliquez sur la flèche en bas
    de la page.
    
    ![Détails du site
    Web](./media/create-mysql-db/provide-website-details.png)

4.  Indiquez le **NOM** de la base de données, sélectionnez sa
    **RÉGION** (elle doit être identique à celle du site Web), acceptez
    les conditions juridiques de ClearDB, puis cliquez sur la coche en
    bas du cadre.
    
    ![Fournir des détails
    MySQL](./media/create-mysql-db/provide-mysql-details.png)

5.  Une fois le site Web créé, cliquez sur son nom pour accéder à son
    tableau de bord.
    
    ![Accéder au tableau de bord du site
    Web](./media/create-mysql-db/go-to-website-dashboard.png)

6.  Cliquez sur **CONFIGURE**.
    
    ![Accéder à l’onglet
    Configurer](./media/create-mysql-db/go-to-configure-tab.png)

7.  Faites défiler jusqu'à la section **connection strings**, puis
    cliquez sur **Show Connection Strings**.
    
    ![Afficher la chaîne de
    connexion](./media/create-mysql-db/show-conn-string.png)

8.  Copiez la chaîne de connexion pour l'utiliser dans l'application.
    
    ![Chaîne de connexion
    affichée](./media/create-mysql-db/shown-conn-string.png)

 
<div  class="dev-callout"> 
<b>Remarque</b> 
<p>L’application du site peut accéder aux chaînes de connexion par leur nom. Dans les applications .NET, les chaînes de connexion sont disponibles dans l’objet <b>connectionStrings</b>. Dans les autres langages de programmation, les chaînes de connexion sont accessibles par des variables d’environnement. Pour plus d’informations, consultez la rubrique <a  href="/en-us/manage/services/web-sites/how-to-configure-websites/">Configuration des sites Web</a>.</p> 
</div>

 

[1]: http://www.cleardb.com/
[2]: http://manage.windowsazure.com
