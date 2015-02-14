#Création d'une base de données MySQL dans Azure

Ce guide montre comment utiliser [ClearDB] pour créer une base de données MySQL à partir de [Microsoft Azure Store] et comment créer une base de données MySQL comme ressource liée lorsque vous créez un [site web Azure][waws]. [ClearDB] est un fournisseur de services database-as-a-service à tolérance de panne permettant d'exécuter et de gérer des bases de données MySQL dans les centres de données Azure et de s'y connecter à partir d'une application.  

##Sommaire
* [Procédure : création d'une base de données MySQL à partir du Magasin Azure](#CreateFromStore)
* [Procédure : création d'une base de données MySQL comme ressource liée pour un site web Azure](#CreateForWebSite)

> [AZURE.NOTE] Lors de la création d'une base de données MySQL pendant le processus de création du site web, vous pouvez créer seulement une base de données gratuite. La création d'une base de données MySQL à partir du Magasin Azure vous permet de créer une base de données gratuite ou de choisir une des options payantes.

<h2><a id="CreateFromStore"></a>Procédure : création d'une base de données MySQL à partir du Magasin Azure</h2>

Pour créer une base de données MySQL à partir de [Microsoft Azure Store], procédez comme suit :

1. Connectez-vous au [portail de gestion Azure][portail].
2. Cliquez sur **+NOUVEAU** en bas de la page, puis sélectionnez **STORE**.

	![Select add-on from store](./media/create-mysql-db/select-store.png)

3. Sélectionnez **ClearDB MySQL Database** puis cliquez sur la flèche en bas du cadre.

	![Select ClearDB MySQL Database](./media/create-mysql-db/select-cleardb-mysql.png)

4. Sélectionnez un plan, indiquez le nom de la base de données, sélectionnez une région, puis cliquez sur la flèche en bas du cadre.

	![Purchase MySQL database from store](./media/create-mysql-db/purchase-mysql.png)

5. Cliquez sur la coche pour effectuer l'achat.

	![Review and complete your purchase](./media/create-mysql-db/complete-mysql-purchase.png)

6. Une fois la base de données créée, vous pouvez la gérer à partir de l'onglet **ADD-ONS** du portail de gestion.

	![Manage MySQL database in Azure portal](./media/create-mysql-db/manage-mysql-add-on.png)

7. Pour obtenir les informations de connexion à la base de données, cliquez sur **INFORMATIONS DE CONNEXION** en bas de la page (comme illustré ci-dessus).

	![MySql connection information](./media/create-mysql-db/mysql-conn-info.png) 


<h2><a id="CreateForWebSite"></a>Procédure : création d'une base de données MySQL comme ressource liée pour un site web Azure</h2>

Pour créer une base de données MySQL comme ressource liée lors de la création d'un [site web Azure][waws], procédez comme suit :

1. Connectez-vous au [portail de gestion Azure][portail].
2. Cliquez sur **+NOUVEAU** en bas de la page, puis sélectionnez **CALCUL**, **SITE WEB** et **CRÉER AVEC BASE DE DONNÉES**.

	![Create website with database](./media/create-mysql-db/custom_create.png)

3. Indiquez une **URL** pour le site web, choisissez une **RÉGION**, puis sélectionnez **Créer une base de données MySQL** dans la liste déroulante **BASE DE DONNÉES**. Vous pouvez éventuellement remplacer le nom par défaut par la chaîne de connexion. Cliquez sur la flèche en bas de la page.

	![Provide website details](./media/create-mysql-db/provide-website-details.png) 

4. Indiquez le **NOM** de la base de données, sélectionnez sa **RÉGION** (elle doit être identique à celle du site web), acceptez les conditions juridiques de ClearDB, puis cliquez sur la coche en bas du cadre.

	![Provide MySQL details](./media/create-mysql-db/provide-mysql-details.png)

5. Une fois le site web créé, cliquez sur son nom pour accéder à son tableau de bord.

	![Go to web site dashboard](./media/create-mysql-db/go-to-website-dashboard.png)

6. Cliquez sur **CONFIGURER**.

	![Go to configure tab](./media/create-mysql-db/go-to-configure-tab.png)

7. Faites défiler jusqu'à la section **Chaînes de connexion**, puis cliquez sur **Afficher les chaînes de connexion**. 

	![Show connection string](./media/create-mysql-db/show-conn-string.png)

8. Copiez la chaîne de connexion pour l'utiliser dans l'application.

	![Shown connection string](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE] L'application du site peut accéder aux chaînes de connexion par leur nom. Dans les applications .NET, les chaînes de connexion sont disponibles dans l'objet **connectionStrings**. Dans les autres langages de programmation, les chaînes de connexion sont accessibles par des variables d'environnement. Pour plus d'informations, consultez la rubrique [Configuration des sites web][configuration].

[ClearDB]: http://www.cleardb.com/
[waws]: /fr-fr/documentation/services/web-sites/
[Microsoft Azure Store]: /fr-fr/gallery/store/
[portail]: http://manage.windowsazure.com
[configuration]: ../web-sites-configure/
<!--HONumber=42-->
