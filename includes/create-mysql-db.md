#Création d'une base de données MySQL dans Azure

Ce guide présente l'utilisation de [ClearDB] pour créer une base de données MySQL à partir du [Magasin Azure] et la création d'une base de données MySQL comme ressource liée lors de la création d'un [site Web Azure][waws]. [ClearDB] est un fournisseur de services database-as-a-service à tolérance de panne permettant d’exécuter et de gérer des bases de données MySQL dans les centres de données Azure et de s’y connecter à partir d’une application.

##Sommaire
* [Création d'une base de données MySQL à partir de l'Azure Store](#CreateFromStore)
* [Création d'une base de données MySQL comme ressource liée pour un site web Azure](#CreateForWebSite)

> [AZURE.NOTE]Lors de la création d'une base de données MySQL pendant le processus de création du site web, vous pouvez créer seulement une base de données gratuite. La création d'une base de données MySQL à partir du Magasin Azure vous permet de créer une base de données gratuite ou de choisir une des options payantes.

<h2><a id="CreateFromStore"></a>Création d’une base de données MySQL à partir de l’Azure Store</h2>

Pour créer une base de données MySQL à partir de l’[Azure Store], procédez comme suit :

1. Connectez-vous au [portail de gestion Azure][portal].
2. Cliquez sur **+Nouveau** au bas de la page, puis sélectionnez **Marketplace**.

	![Sélectionner un module complémentaire dans le marketplace](./media/create-mysql-db/select-store.png)

3. Sélectionnez **ClearDB MySQL Database**, puis cliquez sur la flèche en bas du cadre.

	![Sélectionner la base de données MySQL ClearDB](./media/create-mysql-db/select-cleardb-mysql.png)

4. Sélectionnez un plan, indiquez le nom de la base de données, sélectionnez une région, puis cliquez sur la flèche en bas du cadre.

	![Acheter une base de données MySQL dans le marketplace](./media/create-mysql-db/purchase-mysql.png)

5. Cliquez sur la coche pour effectuer l’achat.

	![Passer en revue et terminer l’achat](./media/create-mysql-db/complete-mysql-purchase.png)

6. Une fois la base de données créée, vous pouvez la gérer à partir de l’onglet **ADD-ONS** du portail de gestion.

	![Gérer une base de données MySQL dans le portail Azure](./media/create-mysql-db/manage-mysql-add-on.png)

7. Pour obtenir les informations de connexion à la base de données, cliquez sur **CONNECTION INFO** en bas de la page (comme illustré ci-dessus).

	![Informations de connexion MySql](./media/create-mysql-db/mysql-conn-info.png)


<h2><a id="CreateForWebSite"></a>Création d'une base de données MySQL comme ressource liée pour un site web Azure</h2>

Pour créer une base de données MySQL comme ressource liée lors de la création d’un [site Web Azure][waws], procédez comme suit :

1. Connectez-vous au [portail de gestion Azure][portal].
2. Cliquez sur **+NOUVEAU** en bas de la page, puis sélectionnez **CALCUL**, **SITE WEB** et **CRÉER AVEC BASE DE DONNÉES**.

	![Créer un site Web avec une base de données](./media/create-mysql-db/custom_create.png)

3. Indiquez une **URL** pour le site web, sélectionnez une **RÉGION**, puis choisissez **Créer une base de données MySQL** dans la liste déroulante **BASE DE DONNÉES**. Vous pouvez éventuellement remplacer le nom par défaut par la chaîne de connexion. Cliquez sur la flèche en bas de la page.

	![Détails du site Web](./media/create-mysql-db/provide-website-details.png)

4. Indiquez le **NOM** de la base de données, sélectionnez sa **RÉGION** (elle doit être identique à celle du site web), acceptez les conditions juridiques de ClearDB, puis cliquez sur la coche en bas du cadre.

	![Fournir des détails MySQL](./media/create-mysql-db/provide-mysql-details.png)

5. Une fois le site web créé, cliquez sur son nom pour accéder à son tableau de bord.

	![Accéder au tableau de bord du site Web](./media/create-mysql-db/go-to-website-dashboard.png)

6. Cliquez sur **CONFIGURE**.

	![Accéder à l’onglet Configurer](./media/create-mysql-db/go-to-configure-tab.png)

7. Faites défiler jusqu’à la section **connection strings**, puis cliquez sur **Show Connection Strings**.

	![Afficher la chaîne de connexion](./media/create-mysql-db/show-conn-string.png)

8. Copiez la chaîne de connexion pour l’utiliser dans l’application.

	![Chaîne de connexion affichée](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE]L'application du site peut accéder aux chaînes de connexion par leur nom. Dans les applications .NET, les chaînes de connexion sont disponibles dans l'objet **connectionStrings**. Dans les autres langages de programmation, les chaînes de connexion sont accessibles par des variables d’environnement. Pour plus d'informations, consultez la rubrique [Configuration des sites Web][configure].

[ClearDB]: http://www.cleardb.com/
[waws]: /documentation/services/web-sites/
[Azure Store]: ../articles/store.md
[Magasin Azure]: ../articles/store.md
[portal]: http://manage.windowsazure.com
[configure]: ../article/app-service-web/web-sites-configure.md

<!--HONumber=52-->
