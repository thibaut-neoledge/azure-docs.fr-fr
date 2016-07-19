<properties
	pageTitle="Prise en main de Data Catalog | Microsoft Azure"
	description="Didacticiel de bout en bout présentant des scénarios et des fonctionnalités d’Azure Data Catalog."
	documentationCenter=""
	services="data-catalog"
	authors="steelanddata"
	manager=""
	editor=""
	tags=""/>
<tags
	ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="07/06/2016"
	ms.author="spelluru"/>

# Didacticiel : Prise en main d’Azure Data Catalog
Azure Data Catalog est un service cloud entièrement géré qui sert de système d’inscription et de découverte des ressources de données d’entreprise. Pour une présentation détaillée, consultez l’article [Qu’est-ce qu’Azure Data Catalog ?](data-catalog-what-is-data-catalog.md).

Ce didacticiel vous permet de commencer à utiliser Azure Data Catalog. Dans ce didacticiel, vous allez effectuer les étapes suivantes :

| Étape | Description |
| :--- | :---------- |
| [Approvisionner Data Catalog](#provision-data-catalog) | Dans cette étape, vous allez approvisionner/configurer Azure Data Catalog. Vous effectuerez cette étape uniquement si le catalogue n’a pas été configuré auparavant. Vous ne pouvez avoir qu’un seul catalogue de données par organisation (domaine Azure Active Directory) même si plusieurs abonnements sont associés à votre compte Azure. | 
| [Inscrire des ressources de données](#register-data-assets) | Dans cette étape, vous allez inscrire les ressources de données de l’exemple de base de données AdventureWorks2014 auprès du catalogue de données. L’inscription est le processus par lequel les métadonnées structurelles clés (telles que les noms, les types et les emplacements) sont extraites de la source de données, puis copiées dans le catalogue. La source de données et les ressources de données restent au même endroit, mais les métadonnées sont utilisées par le catalogue pour faciliter leur découverte et leur compréhension. |
| [Découvrir les ressources de données](#discover-data-assets) | Dans cette étape, vous allez utiliser le portail Azure Data Catalog pour découvrir les ressources de données qui ont été inscrites à l’étape précédente. Une fois qu’une source de données a été inscrite auprès d’Azure Data Catalog, ses métadonnées sont indexées par le service. Les utilisateurs peuvent ainsi effectuer des recherches facilement pour découvrir les données dont ils ont besoin. |
| [Annoter les ressources de données](#annotate-data-assets) | Dans cette étape, vous allez fournir des annotations (descriptions, balises, documentation, experts, etc.) pour les ressources de données afin de compléter les métadonnées extraites de la source de données et de rendre la source de données plus compréhensible pour le plus grand nombre. | 
| [Se connecter aux ressources de données](#connect-to-data-assets) | Dans cette étape, vous allez ouvrir les ressources de données dans des outils clients intégrés, comme Excel et SQL Server Data Tools, et dans un outil non intégré (SQL Server Management Studio) à l’aide des informations de connexion. |
| [Gérer les ressources de données](#manage-data-assets) | Dans cette étape, vous allez découvrir comment configurer la sécurité de vos ressources de données. Data Catalog ne permet pas aux utilisateurs d’accéder aux données proprement dites. L’accès aux données est contrôlé par le propriétaire de la source de données. <br/><br/>Data Catalog permet aux utilisateurs de découvrir les sources de données et d’afficher les **métadonnées** associées aux sources inscrites dans le catalogue. Il peut arriver, cependant, que certaines sources de données ne doivent être visibles qu’à des utilisateurs spécifiques ou à des membres de groupes spécifiques. Pour ces scénarios, Data Catalog offre aux utilisateurs la possibilité de s’approprier les ressources de données inscrites dans le catalogue et de contrôler ensuite la visibilité des ressources qu’ils détiennent. | 
| [Supprimer les ressources de données](#remove-data-assets) | Dans cette étape, vous allez découvrir comment supprimer les ressources de données du catalogue de données. |  
 
## Configuration requise pour le didacticiel

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

### Abonnement Azure
Pour configurer Azure Data Catalog, vous devez être **propriétaire ou copropriétaire** d’un abonnement Azure.

Les abonnements Azure vous permettent d’organiser l'accès aux ressources du service cloud telles qu’Azure Data Catalog. Ils vous permettent également de contrôler le signalement, la facturation et le paiement des ressources utilisées. Chaque abonnement peut disposer d’une configuration de facturation et de paiement différente. Vous pouvez donc avoir différents abonnements et différents plans par département, projet, bureau régional, etc. Chaque service cloud appartient à un abonnement. Vous devez donc avoir un abonnement avant de configurer Azure Data Catalog. Pour plus d’informations, consultez [Gestion des comptes, des abonnements et des rôles d’administrateur](../active-directory/active-directory-how-subscriptions-associated-directory.md).

Si vous n'êtes pas abonné, vous pouvez créer un compte d'essai gratuit en quelques minutes. Pour plus d’informations, consultez la page [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

### Azure Active Directory
Pour configurer Azure Data Catalog, vous devez être connecté à l’aide d’un **compte d’utilisateur Azure Active Directory** dont l’utilisateur doit être propriétaire ou copropriétaire d’un abonnement Azure.

Azure Active Directory (Azure AD) permet à votre entreprise de gérer facilement les identités et les accès, à la fois dans le cloud et en local. Les utilisateurs peuvent utiliser un seul compte professionnel ou scolaire pour utiliser l'authentification unique sur n'importe quelle application web locale ou dans le cloud. Azure Data Catalog utilise Azure AD pour valider l’authentification. Pour plus d’informations, consultez l’article [Qu’est-ce qu’Azure Active Directory ?](../active-directory/active-directory-whatis.md).

### Configuration de la stratégie Active Directory

Dans certains cas, il peut arriver que les utilisateurs parviennent à se connecter au portail Azure Data Catalog, mais quand ils essaient de se connecter à l’outil de référencement des sources de données, ils obtiennent un message d’erreur qui les empêche de se connecter. Ce problème peut aussi survenir aussi bien quand l’utilisateur se trouve sur le réseau d’entreprise ou quand il se connecte en dehors du réseau d’entreprise.

L’outil de référencement utilise **l’authentification par formulaire** pour valider les ouvertures de session des utilisateurs avec Active Directory. Pour une ouverture de session réussie, l’authentification par formulaire doit être activée dans la **stratégie d’authentification globale** par un administrateur Active Directory.

La stratégie d’authentification globale permet d’activer séparément des méthodes d’authentification pour les connexions intranet et extranet, comme illustré ci-dessous. Des erreurs de connexion peuvent survenir si l'authentification par formulaire n'est pas activée pour le réseau à partir duquel l'utilisateur se connecte.

 ![Stratégie d’authentification globale d’Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Pour plus d’informations, consultez [Configuration des stratégies d’authentification](https://technet.microsoft.com/library/dn486781.aspx).

## Approvisionner Data Catalog
Vous ne pouvez approvisionner qu’un seul catalogue de données par organisation (domaine Azure Active Directory). Par conséquent, si le propriétaire ou le copropriétaire d’un abonnement Azure qui fait partie de ce domaine Active Directory a déjà créé un catalogue, vous ne pourrez pas en créer un autre, même si vous disposez de plusieurs abonnements Azure. Pour tester si un catalogue de données a été créé par un utilisateur dans votre domaine Active Directory, accédez à http://azuredatacatalog.com et vérifiez si le catalogue est répertorié. Ignorez la procédure suivante et allez à la section suivante si un catalogue a déjà été créé pour vous.

1. Accédez à [https://azure.microsoft.com/services/data-catalog](https://azure.microsoft.com/services/data-catalog)

	![Azure Data Catalog - page d’arrivée de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png), puis cliquez sur **Mise en route**.
2. Connectez-vous à l’aide d’un compte d’utilisateur **propriétaire ou copropriétaire** d’un abonnement Azure. La page suivante doit s’afficher une fois que vous vous êtes connecté.

	![Azure Data Catalog - approvisionner data catalog](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
2. Spécifiez un **nom** pour le catalogue de données, **l’abonnement** à utiliser et **l’emplacement** du catalogue.
3. Développez **Prix appliqués** et précisez **l’édition** d’Azure Data Catalog (gratuite et standard). ![Azure Data Catalog - sélectionner édition](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
4. Développez **Utilisateurs du catalogue**, puis cliquez sur **Ajouter** pour ajouter des utilisateurs au catalogue de données. Vous êtes automatiquement ajouté à ce groupe. ![Azure Data Catalog - utilisateurs](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
5. Développez **Administrateurs du catalogue**, puis cliquez sur **Ajouter** pour ajouter des administrateurs supplémentaires du catalogue de données. Vous êtes automatiquement ajouté à ce groupe. ![Azure Data Catalog - administrateurs](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
6. Cliquez sur le bouton **Créer un catalogue** pour créer le catalogue de données de votre organisation. Une fois le catalogue créé, la page d’accueil du catalogue de données doit s’afficher. ![Azure Data Catalog - créé](media/data-catalog-get-started/data-catalog-created.png)

### Rechercher le catalogue de données dans le portail Azure
1. Dans un onglet distinct dans le navigateur web ou dans une fenêtre de navigateur web distincte, accédez à [https://portal.azure.com](https://portal.azure.com) et connectez-vous en utilisant le compte que vous avez utilisé pour créer le catalogue de données à l’étape précédente.
2. Cliquez sur **Parcourir**, puis sur **Catalogue de données**.

	![Azure Data Catalog - parcourir le portail azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)
3. Le catalogue de données créé doit s’afficher.

	![Azure Data Catalog - afficher le catalogue dans une liste](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
4.  Cliquez sur le catalogue que vous avez créé ; le panneau **Catalogue de données** doit apparaître dans le portail.

	![Azure Data Catalog - panneau dans le portail](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
5. Vous pouvez afficher les propriétés du catalogue de données et les mettre à jour. Par exemple, cliquez sur **Niveau de tarification** et modifiez l’édition.

	![Azure Data Catalog - niveau de tarification](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### Exemple de base de données Adventure Works 
Dans ce didacticiel, vous allez inscrire les ressources de données (tables) de l’exemple de base de données AdventureWorks2014 pour le moteur de base de données SQL Server. Vous pouvez utiliser cependant n’importe quelle source de données prise en charge si vous préférez travailler avec des données plus familières et mieux adaptées à votre rôle. Pour obtenir la liste des sources de données prises en charge, consultez l’article [Sources de données prises en charge par Azure Data Catalog](data-catalog-dsr.md).

### Installer la base de données Adventure Works OLTP 2014
La base de données Adventure Works prend en charge les scénarios de traitement des transactions en ligne standard pour un fabricant fictif de bicyclettes (Adventure Works Cycles), y compris les produits, les ventes et les achats. Dans ce didacticiel, vous allez inscrire des informations sur les produits dans **Azure Data Catalog**.

Voici comment installer l’exemple de base de données Adventure Works :

1. Téléchargez [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) sur CodePlex.
2. Suivez les instructions de l’article [Restaurer une sauvegarde de base de données (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx) pour restaurer la base de données sur votre ordinateur. **Procédure rapide**:
	1. Lancez SQL Server Management Studio et connectez-vous au moteur de base de données SQL Server.
	2. Cliquez avec le bouton droit sur **Bases de données**, puis sélectionnez **Restaurer la base de données**.
	3. Dans la boîte de dialogue **Restaurer la base de données**, sélectionnez l’option **Appareil** comme **Source**, puis cliquez sur **Parcourir (...)**.
	4. Dans la boîte de dialogue **Sélectionner les unités de sauvegarde**, cliquez sur **Ajouter**.
	5. Accédez au dossier qui contient le fichier **AdventureWorks2014.bak**, sélectionnez le fichier, puis cliquez sur **OK** pour fermer la boîte de dialogue **Localiser le fichier de sauvegarde**.
	6. Cliquez sur **OK** pour fermer la boîte de dialogue **Sélectionner les unités de sauvegarde**.
	7. Cliquez sur **OK** pour fermer la boîte de dialogue **Restaurer la base de données**.

À présent, nous allons voir comment inscrire les ressources de données de l’exemple de base de données Adventure Works auprès **d’Azure Data Catalog**.

## Inscrire des ressources de données

Dans cet exercice, vous allez utiliser l’outil de référencement pour inscrire les ressources de données de la base de données Adventure Works auprès du catalogue. L’inscription est le processus par lequel les métadonnées structurelles clés (telles que les noms, les types et les emplacements) sont extraites de la source de données et des ressources qu’elle contient, puis copiées dans le catalogue. La source de données et les ressources de données restent au même endroit, mais les métadonnées sont utilisées par le catalogue pour faciliter leur découverte et leur compréhension.

### Voici comment inscrire une source de données

1.	Accédez à [https://azuredatacatalog.com](https://azuredatacatlog.com), puis cliquez sur **Publier des données** dans la page d’accueil.

    ![Azure Data Catalog - bouton Publier des données](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	Cliquez sur **Lancer l’application** pour télécharger, installer et exécuter **l’outil de référencement** sur votre ordinateur.

    ![Azure Data Catalog - bouton de lancement](media/data-catalog-get-started/data-catalog-launch-application.png)

4. Sur la page **Bienvenue**, cliquez sur **Connexion**, puis entrez vos informations d’identification.

	![Azure Data Catalog - boîte de dialogue Bienvenue](media/data-catalog-get-started/data-catalog-welcome-dialog.png)

5. Dans la page **Microsoft Azure Data Catalog**, double-cliquez sur **SQL Server** ou cliquez sur **SQL Server**, puis sur **Suivant**.

    ![Azure Data Catalog - Sources de données](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	Entrez les propriétés de connexion SQL Server pour **AdventureWorks2014** (voir l’exemple ci-dessous), puis cliquez sur **SE CONNECTER**.

    ![Azure Data Catalog - Paramètres de connexion à SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	La page suivante vous permet d’inscrire les métadonnées de votre ressource de données. Dans cet exemple, vous allez inscrire les objets **Production/Product** à partir de l’espace de noms AdventureWorks Production. Voici comment procéder :
    
	1. Dans l’arborescence **Hiérarchie du serveur**, développez **AdventureWorks2014**, puis cliquez sur **Production**.
	2. Appuyez sur la touche CTRL et cliquez sur **Product**, **ProductCategory**, **ProductDescription** et **ProductPhoto**.
	3. Cliquez sur la **flèche de déplacement des objets sélectionnés** (**>**). Tous les objets Product sélectionnés sont alors déplacés dans la liste **Objets à inscrire**.
			
    	![Didacticiel Azure Data Catalog - parcourir et sélectionner des objets](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
	4. Sélectionnez **Include a Preview (Inclure un aperçu)** pour inclure un aperçu instantané des données. L’instantané inclut jusqu’à 20 enregistrements de chaque table et est copié dans le catalogue.
	5. Sélectionnez **Inclure le profil de données** pour inclure un instantané des statistiques des objets du profil de données (par exemple, les valeurs minimale, maximale et moyenne d’une colonne, le nombre de lignes, etc.).
	5. Dans la zone **Add tags (Ajouter des balises)**, entrez **adventure works, cycles**. Vous ajouterez ainsi des étiquettes de recherche à ces ressources de données. Les étiquettes sont un excellent moyen d’aider les utilisateurs à trouver une source de données inscrite.
	6. (facultatif) Spécifiez le nom d’un **expert** pour ces données.
	
    	![Didacticiel Azure Data Catalog - objets à inscrire](media/data-catalog-get-started/data-catalog-objects-register.png)
    
	7. Cliquez sur **INSCRIRE**. Azure Data Catalog enregistre les objets que vous avez sélectionnés. Dans cet exercice, les objets sélectionnés à partir d’Adventure Works sont enregistrés. L’outil de référencement extrait les métadonnées de la ressource de données et les copie dans le service Azure Data Catalog. Les données restent à leur emplacement d’origine, toujours sous le contrôle des administrateurs et des stratégies du système actuel.
	
		![Azure Data Catalog - objets inscrits](media/data-catalog-get-started/data-catalog-registered-objects.png)

	8. Pour afficher les objets de source de données que vous avez inscrits, cliquez sur **Afficher le portail**. Dans le portail **Azure Data Catalog**, vérifiez que les quatre tables et la base de données sont affichés en mode Grille.
 
    	![Objets dans le portail Azure Data Catalog](media/data-catalog-get-started/data-catalog-view-portal.png)
  
	
Dans cet exercice, vous avez inscrit des objets à partir de l’exemple de base de données Adventure Works afin qu’ils soient facilement détectables par les utilisateurs de l’ensemble de votre organisation. Dans l’exercice suivant, vous apprendrez comment découvrir les ressources de données inscrites.

## Découvrir les ressources de données
Dans Azure Data Catalog, la découverte utilise deux mécanismes principaux : la recherche et le filtrage.

La **recherche** est conçue pour être intuitive et puissante. Par défaut, les termes de recherche sont comparés à toutes les propriétés du catalogue, notamment aux annotations fournies par l’utilisateur.

Le **filtrage** est conçu pour compléter la recherche. Vous pouvez sélectionner des caractéristiques spécifiques telles que les experts, le type de source de données, le type d’objet et les balises, pour afficher uniquement les ressources de données correspondantes et pour limiter les résultats de la recherche aux ressources correspondantes.

En combinant recherche et filtrage, vous pouvez parcourir rapidement les sources de données qui ont été inscrites auprès d’Azure Data Catalog pour découvrir celles qui vous sont utiles.

Dans cet exercice, vous allez utiliser le portail **Azure Data Catalog** pour découvrir les ressources de données que vous avez inscrites dans l’exercice précédent. Pour plus d’informations sur la syntaxe de recherche, consultez l’article [Data Catalog Search syntax reference (Informations de référence sur la syntaxe de recherche dans Data Catalog)](https://msdn.microsoft.com/library/azure/mt267594.aspx).

Nous allons examiner quelques exemples de découverte des ressources de données dans le catalogue.

### Recherche de base
La recherche de base vous permet d’effectuer des recherches dans le catalogue en utilisant un ou plusieurs termes de recherche. Les résultats sont toutes les ressources correspondant à une propriété quelconque avec un ou plusieurs termes spécifiés.

1. Cliquez sur le bouton **Accueil** dans le portail Azure Data Catalog. Si vous avez fermé le navigateur web, accédez à [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com).
2. Dans la zone de recherche située en haut, entrez **cycles** et cliquez sur l’icône **recherche** (ou) appuyez sur **ENTRÉE**.
	
	![Azure Data Catalog - recherche de texte de base](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Vérifiez que les quatre tables et la base de données (AdventureWorks2014) sont indiquées dans les résultats. Vous pouvez basculer entre le **mode Grille** et le **mode Liste** en cliquant sur les boutons situés en haut, comme illustré dans l’image suivante. Notez que le mot clé de recherche est mis en surbrillance dans les résultats de recherche lorsque l’option **Mettre en surbrillance** située en haut est **ACTIVÉE**. Vous pouvez également spécifier le nombre de **résultats par page** dans les résultats de recherche.

	![Azure Data Catalog - résultats de recherche de texte de base](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
	
	Le panneau **Recherches** et le panneau **Propriétés** s’affichent respectivement à gauche et à droite. Le panneau Recherche vous permet de modifier les critères de recherche et de filtrer les résultats. Le panneau Propriétés affiche les propriétés d’un objet sélectionné dans la grille/liste.

4. Cliquez sur **Product** dans les résultats de recherche. Cliquez sur les onglets **Aperçu**, **Colonnes**, **Profil de données** et **Documentation** (ou) utilisez la flèche **HAUT** pour développer le volet inférieur dans le milieu.
 
	![Azure Data Catalog - volet inférieur](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
	
	L’aperçu des données de la table Product doit être affiché dans l’onglet **Aperçu**.
5. Cliquez sur l’onglet **Colonnes** pour rechercher des informations sur les colonnes (comme **nom** et **type de données**) dans la ressource de données.
6. Cliquez sur l’onglet **Profil de données** pour consulter le profilage des données (par exemple le nombre de lignes, la taille des données, la valeur minimale dans une colonne, etc.) dans la ressource de données.
6. Filtrez les résultats à l’aide de l’option **Filtres** située sur la gauche. Par exemple, cliquez sur **Table** comme **Type d’objet**. Vous ne devez voir que les quatre tables, mais pas la base de données.

	![Azure Data Catalog - filtrer les résultats de recherche](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### Étendue de la propriété
L’étendue de la propriété vous permet de découvrir les ressources de données dans lesquelles le terme recherché correspond à la propriété spécifiée.

3. Désactivez le filtre **Table** pour **Type d’objet** dans **Filtres**.
4. Dans la zone de recherche, entrez **tags:cycles** et cliquez sur l’icône **recherche** (ou) appuyez sur **ENTRÉE**. Consultez l’article [Data Catalog Search syntax reference (Informations de référence sur la syntaxe de recherche dans Data Catalog)](https://msdn.microsoft.com/library/azure/mt267594.aspx) pour connaître toutes les propriétés que vous pouvez utiliser pour la recherche dans le catalogue de données.
3. Vérifiez que les quatre tables et la base de données (AdventureWorks2014) sont indiquées dans les résultats.

	![Data Catalog - résultats de recherche d’étendue de la propriété](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### Enregistrer la recherche 
1. Dans le volet Recherches situé sur la gauche, dans la section Recherche actuelle, cliquez sur Enregistrer pour enregistrer les critères de recherche en cours. Entrez le nom de la recherche et cliquez sur Enregistrer.
	
	![Azure Data Catalog - enregistrer la recherche](media/data-catalog-get-started/data-catalog-save-search.png)
2. Vérifiez que la recherche enregistrée s’affiche au-dessous de Recherches enregistrées.

	![Azure Data Catalog - recherches enregistrées](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Cliquez sur la flèche vers le bas pour afficher les actions que vous pouvez effectuer sur la recherche enregistrée (renommer, supprimer, définir comme recherche par défaut). ![Azure Data Catalog - options de recherche enregistrées](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### Opérateurs booléens
Les opérateurs booléens vous permettent d’élargir ou d’affiner une recherche.

2. Dans la zone de recherche, entrez **tags:cycles AND objectType:table** et cliquez sur l’icône **recherche** (ou) appuyez sur **ENTRÉE**.
3. Vérifiez que les résultats contiennent uniquement les tables, et pas la base de données.

	![Azure Data Catalog - opérateur booléen dans la recherche](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### Parenthèses de regroupement
Les parenthèses de regroupement vous permettent d’utiliser des parenthèses pour grouper les parties de la requête à isoler logiquement, notamment lorsque vous appliquez également des opérateurs booléens.

1. Dans la zone de recherche, entrez **name:product AND (tags:cycles AND objectType:table)** et cliquez sur l’icône **recherche** (ou) appuyez sur **ENTRÉE**.
2. Vérifiez que seule la table **Product** s’affiche à présent dans les résultats de recherche.

	![Azure Data Catalog - recherche par regroupement](media/data-catalog-get-started/data-catalog-grouping-search.png)

### Opérateurs de comparaison
Les opérateurs de comparaison vous permettent d’utiliser des comparaisons autres que l’égalité pour les propriétés comportant des types de données numériques et de date.

1. Dans la zone de recherche, entrez **lastRegisteredTime:>"09/06/2016"**.
2. Désactivez le filtre **Table** pour **Type d’objet** sur la gauche.
3. Cliquez sur l’icône **recherche** ou appuyez sur **ENTRÉE**.
2. Vérifiez que les tables Product, ProductCategory, ProductDescription et ProductPhoto ainsi que la base de données AdventureWorks2014 que vous avez inscrite s’affichent dans les résultats de recherche.

	![Azure Data Catalog - résultats de recherche par comparaison](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Consultez l’article [How to discover data assets (Découverte des ressources de données)](data-catalog-how-to-discover.md) pour plus d’informations sur la découverte des ressources de données et l’article [Data Catalog Search syntax reference (Informations de référence sur la syntaxe de recherche dans Data Catalog)](https://msdn.microsoft.com/library/azure/mt267594.aspx) pour connaître la syntaxe de recherche.

## Annoter les ressources de données
Dans cet exercice, vous allez utiliser le portail **Azure Data Catalog** pour annoter (ajouter des descriptions, des balises, des experts, etc.) les ressources de données préalablement inscrites dans le catalogue. Les annotations que vous ajouterez compléteront et amélioreront les métadonnées structurelles extraites de la source de données au cours de l’inscription et faciliteront la découverte et la compréhension des ressources de données.

### Voici comment annoter les ressources de données
Dans cette étape, vous allez annoter un jeu de données unique (ProductPhoto). Vous allez ajouter un nom convivial, une description, etc. à la ressource de données de ProductPhoto.

1.  Si vous avez fermé le navigateur, accédez à [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) et effectuez une recherche avec **tags:cycles** pour trouver les ressources de données que vous avez inscrites.
2. Cliquez sur **ProductPhoto** dans les résultats de la recherche.
3. Entrez **Images de produit** comme **Nom convivial** et **Photos de produit pour les documents marketing** dans le champ **Description**.

	![Azure Data Catalog - description d’une photo du produit](media/data-catalog-get-started/data-catalog-productphoto-description.png)

	La **Description** aide les autres utilisateurs à découvrir et à comprendre pourquoi et comment utiliser la ressource de données sélectionnée. Vous pouvez également ajouter des étiquettes supplémentaires et afficher les colonnes. Maintenant, vous pouvez essayer de rechercher et de filtrer pour découvrir les ressources de données à l’aide des métadonnées descriptives que vous avez ajoutées au catalogue.

Notez que vous pouvez également effectuer les opérations suivantes dans cette page :

- Ajouter des experts pour la ressource de données. Cliquez sur **Ajouter...** sous **Experts :** dans le volet droit.
- Ajouter des balises au niveau du jeu de données. Cliquez sur **Ajouter...** sous **Balises :** dans le volet droit. Une balise peut être une balise utilisateur ou une balise glossaire. L’édition Standard de Data Catalog comprend un glossaire d’entreprise qui permet aux administrateurs de catalogue de définir une taxonomie commerciale centrale. Les utilisateurs du catalogue peuvent ensuite annoter les ressources de données avec la terminologie du glossaire. Pour plus d’informations, consultez l’article [Comment configurer le glossaire métier pour un balisage géré](data-catalog-how-to-business-glossary.md).
- Ajouter des balises au niveau des colonnes. Dans le volet inférieur au milieu, cliquez sur **Ajouter...** sous **Balises** pour la colonne que vous souhaitez annoter.
- Ajouter une description au niveau des colonnes. Dans le volet inférieur au milieu, entrez **Description** pour une colonne. Vous pouvez également afficher les métadonnées de description extraites de la source de données.
- Ajouter des informations relatives au champ **Demander l’accès** qui indiquent aux utilisateurs comment demander un accès à la ressource de données.

	![Azure Data Catalog - ajouter des balises, des descriptions](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
  
- Cliquez sur l’onglet **Documentation** dans le volet inférieur au milieu et fournissez une documentation pour la ressource de données. La documentation Azure Data Catalog vous permet d’utiliser votre catalogue de données comme référentiel de contenu pour créer une narration complète de vos ressources de données.

	![Azure Data Catalog - onglet Documentation](media/data-catalog-get-started/data-catalog-documentation.png)


Vous pouvez également sélectionner plusieurs ressources de données ou leur totalité et leur ajouter une annotation. Par exemple, vous pouvez sélectionner toutes les ressources de données que vous avez inscrites et leur spécifier un expert.

![Azure Data Catalog - annoter plusieurs ressources de données](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Azure Data Catalog prend en charge l’approche des annotations basée sur le crowdsourcing, qui permet aux utilisateurs de Data Catalog d’ajouter des balises (utilisateur ou de glossaire), des descriptions et d’autres métadonnées afin que tout utilisateur ayant un point de vue sur une ressource de données et son utilisation puisse capturer ce point de vue et le mettre à la disposition des autres utilisateurs.

Consultez l’article [How to annotate data assets (Annotation des ressources de données)](data-catalog-how-to-annotate.md) pour obtenir des informations détaillées sur l’annotation des ressources de données.
 
## Se connecter aux ressources de données
Dans cet exercice, vous allez ouvrir les ressources de données dans un outil client intégré (Excel) et dans un outil non intégré (SQL Server Management Studio) à l’aide des informations de connexion.

> [AZURE.NOTE] Il est important de se rappeler qu’**Azure Data Catalog** ne donne pas aux utilisateurs accès à la source de données elle-même : il leur permet simplement de découvrir et de comprendre plus facilement cette source de données. Lorsqu’un utilisateur se connecte à une source de données, l’application cliente qu’il choisit utilisera ses informations d’identification Windows ou l’invitera à saisir des informations d’identification, le cas échéant. Si l’accès à la source de données n’a pas été préalablement accordé à l’utilisateur, il devra obtenir cet accès avant de pouvoir se connecter.

### Voici comment se connecter à une ressource de données d’Excel

1. Sélectionnez **Product** dans les résultats de la recherche. Dans la barre d’outils, cliquez sur **Ouvrir dans**, puis sélectionnez **Excel**.
 
    ![Azure Data Catalog - se connecter à une ressource de données](media/data-catalog-get-started/data-catalog-connect1.png)
5. Cliquez sur **Ouvrir** dans la fenêtre indépendante de téléchargement située en bas (cette expérience peut varier en fonction du navigateur).

	![Azure Data Catalog - télécharger un fichier de connexion excel](media/data-catalog-get-started/data-catalog-download-open.png)
6. Dans la fenêtre **Avis de sécurité Microsoft Excel**, cliquez sur **Activer**.

	![Azure Data Catalog - fenêtre indépendante de sécurité excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
7. Dans la boîte de dialogue **Importer des données**, conservez les valeurs par défaut, puis cliquez sur **OK**.

	![Azure Data Catalog - Données d’importation Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
8. La source de données s’ouvre dans Excel.

    ![Azure Data Catalog - table de produits dans Excel](media/data-catalog-get-started/data-catalog-connect2.png)

Dans cet exercice, vous vous êtes connecté aux ressources de données découvertes à l’aide **d’Azure Data Catalog**. Le portail **Azure Data Catalog** permet aux utilisateurs de se connecter directement en utilisant les applications clientes intégrées à son menu **Ouvrir dans...** et de se connecter avec l’application de leur choix, en utilisant les informations d’emplacement de connexion incluses dans les métadonnées des ressources. Exemple : vous pouvez utiliser SQL Server Management Studio pour vous connecter à la base de données AdventureWorks2014 pour accéder aux données des ressources de données inscrites dans ce didacticiel.

1. Lancez **SQL Server Management Studio**.
2. Dans la boîte de dialogue **Se connecter au serveur**, entrez le **nom du serveur** dans le volet **Propriétés** du portail Azure Data Catalog.
3. Utilisez **l’authentification** et les **informations d’identification** appropriées permettant d’accéder à la ressource de données si vous y avez déjà accès. Si vous n’y avez pas accès, utilisez les informations du champ Demander l’accès pour obtenir ce dernier.

	![Azure Data Catalog - demander l’accès](media/data-catalog-get-started/data-catalog-request-access.png)

Cliquez sur **Afficher les chaînes de connexion** pour afficher et copier les chaînes de connexion OLEDB, ODBC et ADF.NET dans le Presse-papiers pour les utiliser dans votre application.

## Gérer les ressources de données
Dans cette étape, vous allez découvrir comment configurer la sécurité de vos ressources de données. Data Catalog ne permet pas aux utilisateurs d’accéder aux données proprement dites. L’accès aux données est contrôlé par le propriétaire de la source de données.

Data Catalog permet aux utilisateurs de découvrir les sources de données et d’afficher les **métadonnées** relatives aux sources inscrites dans le catalogue. Il peut arriver, cependant, que certaines sources de données ne doivent être visibles qu’à des utilisateurs spécifiques ou à des membres de groupes spécifiques. Pour ces scénarios, Data Catalog offre aux utilisateurs la possibilité de s’approprier les ressources de données inscrites dans le catalogue et de contrôler ensuite la visibilité des ressources qu’ils détiennent.

> [AZURE.NOTE] Les fonctionnalités de gestion décrites dans cet exercice sont disponibles uniquement dans **l’édition Standard d’Azure Data Catalog**, pas dans **l’édition gratuite**. Dans **Azure Data Catalog**, vous pouvez vous approprier des ressources de données, ajouter des copropriétaires aux ressources de données et définir la visibilité de ces ressources.

### Voici comment s’approprier les ressources de données et restreindre leur visibilité

1. Accédez à [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) si vous avez fermé le navigateur web. Dans la zone de texte Rechercher, entrez **tags:cycles** et appuyez sur **ENTRÉE**.
3. Sélectionnez un élément dans la liste des résultats, par exemple **Product**, en cochant la case dans le coin supérieur droit, puis cliquez sur **Prendre possession** dans la barre d’outils, comme illustré dans l’image suivante.
4. Dans le panneau **Propriétés**, section **Gestion**, cliquez sur **Prendre possession** sous la section **Gestion** dans le volet droit.

	![Azure Data Catalog - Prendre possession](media/data-catalog-get-started/data-catalog-take-ownership.png)
5. Pour restreindre la visibilité, cliquez sur **Propriétaires et ces utilisateurs** dans la section **Visibilité** et cliquez sur **Ajouter**. Entrez l’adresse de messagerie d’utilisateur dans la zone de texte et appuyez sur ENTRÉE.

    ![Azure Data Catalog - restreindre l’accès](media/data-catalog-get-started/data-catalog-ownership.png)

## Supprimer les ressources de données

Dans cet exercice, vous allez utiliser le portail **Azure Data Catalog** pour supprimer les aperçus de données des ressources de données inscrites et pour supprimer les ressources de données du catalogue.

Dans **Azure Data Catalog**, vous pouvez supprimer une ou plusieurs ressources.

### Voici comment supprimer des ressources de données

1. Accédez à [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) si vous avez fermé le navigateur web.
2. Dans la zone de texte de recherche, entrez **tags:cycles** et appuyez sur **ENTRÉE**.
3. Sélectionnez un élément dans la liste des résultats, par exemple **ProductDescription**, en cochant la case dans le coin supérieur droit, puis cliquez sur **Supprimer** dans la barre d’outils, comme illustré dans l’image suivante.

	![Azure Data Catalog - Supprimer un élément de grille](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
	
	Si vous utilisez le mode Liste (au lieu du mode Grille), la case à cocher se situe à gauche de l’élément, comme indiqué dans l’image suivante.

	![Azure Data Catalog - Supprimer un élément de liste](media/data-catalog-get-started/data-catalog-delete-list-item.png)

	Vous pouvez également sélectionner plusieurs ressources de données et les supprimer comme indiqué ci-dessous :

	![Azure Data Catalog - Supprimer plusieurs ressources de données](media/data-catalog-get-started/data-catalog-delete-assets.png)


> [AZURE.NOTE] Par défaut, le catalogue permet aux utilisateurs d’inscrire n’importe quelle source de données et de supprimer toutes les ressources de données préalablement inscrites. Les fonctionnalités de gestion incluses dans l’**édition Standard d’Azure Data Catalog** offrent des options supplémentaires qui permettent l’appropriation de ressources, ce qui limite les fonctions de découverte et de suppression des ressources à certains utilisateurs.


## Résumé

Dans ce didacticiel, vous avez exploré les fonctionnalités essentielles **d’Azure Data Catalog**, notamment l’inscription, l’annotation, la découverte et la gestion des ressources de données d’entreprise. Maintenant que vous avez terminé ce didacticiel, il est temps de passer à la pratique. Vous pouvez commencer dès aujourd’hui en enregistrant les sources de données que vous et votre équipe utilisez, et en invitant vos collègues à utiliser le catalogue.

## Références

- [How to register data assets (Inscription des ressources de données)](data-catalog-how-to-register.md)
- [How to discover data assets (Découverte des ressources de données)](data-catalog-how-to-discover.md)
- [How to annotate data assets (Annotation des ressources de données)](data-catalog-how-to-annotate.md)
- [How to document data assets (Documentation des ressources de données)](data-catalog-how-to-documentation.md)
- [How to connect to data assets (Connexion aux ressources de données)](data-catalog-how-to-connect.md)
- [Gestion des ressources de données](data-catalog-how-to-manage.md)

<!---HONumber=AcomDC_0713_2016-->