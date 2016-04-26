<properties
   pageTitle="Azure Data Catalog prise en main du catalogue de données | Microsoft Azure"
   description="Didacticiel de bout en bout présentant des scénarios et des fonctionnalités d’Azure Data Catalog."
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="04/15/2016"
   ms.author="derrickv"/>

# Prise en main d’Azure Data Catalog

Cet article présente de bout en bout les scénarios et les fonctionnalités d’**Azure Data Catalog**. Une fois inscrit au service, suivez ces étapes pour créer un catalogue de données puis inscrire, annoter et découvrir des sources de données.

## Configuration requise pour le didacticiel

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure** : si vous n’en avez pas, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
- **Azure Active Directory** : Azure Data Catalog utilise [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour la gestion des identités et des accès.
- **Sources de données** : les fonctionnalités Azure Data Catalog permettent de découvrir des sources de données. Ce didacticiel utilise l’exemple de base de données Adventure Works, mais vous pouvez utiliser n’importe quelle source de données prise en charge si vous préférez travailler avec des données plus familières et mieux adaptées à votre rôle. Pour obtenir la liste des sources de données prises en charge, consultez [Sources de données prises en charge](data-catalog-dsr.md).

> [AZURE.NOTE] Pour plus d'informations sur les abonnements Azure et Azure Active Directory, consultez [Conditions préalables à l’utilisation d’Azure Data Catalog](data-catalog-prerequisites.md).

Commençons par installer l’exemple de base de données Adventure Works.

## Exercice 1 : installation d’un exemple de base de données Adventure Works

Dans cet exercice, vous installez l’exemple Adventure Works pour le moteur de base de données SQL Server qui est utilisé dans les exercices qui suivent.

### Installer la base de données Adventure Works OLTP 2014

La base de données Adventure Works prend en charge les scénarios de traitement des transactions en ligne standard pour un fabricant fictif de bicyclettes (Adventure Works Cycles), y compris les produits, les ventes et les achats. Dans ce didacticiel, vous allez inscrire des informations sur les produits dans **Azure Data Catalog**.

Voici comment installer l’exemple de base de données Adventure Works.

Pour installer l’exemple de base de données Adventure Works, vous pouvez restaurer une sauvegarde AdventureWorks2014 située dans [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) sur CodePlex. Un moyen de restaurer la base de données consiste à exécuter un script T-SQL dans SQL Server Management Studio.

**Installer l’exemple de base de données Adventure Works avec un script T-SQL**

1.	Créez un dossier nommé C:\\DataCatalog\_GetStarted. Si vous utilisez un autre nom de dossier, veillez à modifier le chemin d’accès dans le script T-SQL ci-dessous.
2.	Téléchargez [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661).
3.	Décompressez Adventure Works 2014 Full Database Backup.zip dans C:\\DataCatalog\_GetStarted. Le script ci-dessous suppose que le fichier de sauvegarde se trouve dans le dossier C:\\DataCatalog\_GetStarted\\Adventure Works 2014 Full Database Backup\\AdventureWorks2014.bak.
4.	À partir de **SQL Server Management Studio**, dans la barre d’outils **Standard**, cliquez sur **Nouvelle requête**.
5.	Exécutez le code T-SQL suivant dans la fenêtre de requête.

**Exécutez le script ci-dessous pour installer la base de données Adventure Works 2014.**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
    	-- AdventureWorks2014.bak file location
    	FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

    	-- AdventureWorks2014.mdf database location
    	WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

    	-- AdventureWorks2014.ldf log location
    	MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

Au lieu d’exécuter un script T-SQL, vous pouvez restaurer la base de données à l’aide de SQL Server Management Studio. Consultez [Restaurer une sauvegarde de base de données (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx).

Dans cet exercice, vous avez installé l’exemple de base de données Adventure Works utilisé dans les exercices restants. Dans l’exercice suivant, vous allez découvrir comment inscrire des sources de données **Azure Data Catalog** à partir de tables dans l’exemple de base de données Adventure Works.

## Exercice 2 : enregistrement des sources de données

Dans cet exercice, vous allez utiliser l’outil de référencement pour **Azure Data Catalog** afin d’inscrire vos sources de données Adventure Works dans le catalogue. L’inscription est le processus par lequel les métadonnées structurelles clés (telles que les noms, les types et les emplacements) sont extraites de la source de données et des ressources qu’elle contient, puis copiées dans le catalogue. Les sources de données et leurs données restent au même endroit, mais les métadonnées sont utilisées par le catalogue pour les rendre plus facilement détectables et compréhensibles.

### Voici comment inscrire une source de données

1.	Accédez à https://azure.microsoft.com/services/data-catalog, puis cliquez sur **Prise en main**.
2.	Connectez-vous au portail **Azure Data Catalog**, puis cliquez sur **Publier des données**.

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	Cliquez sur **Lancer l’application**.

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. Sur la page **Bienvenue**, cliquez sur **Connexion**, puis entrez vos informations d’identification.
5. Sur la page **Microsoft Azure Data Catalog**, double-cliquez sur **SQL Server** ou cliquez sur **SQL Server**, puis sur **Suivant**.

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	Entrez les propriétés de connexion SQL Server pour AdventureWorks2014 (voir l’exemple ci-dessous), puis cliquez sur **SE CONNECTER**.

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	La page suivante est celle où vous inscrivez les métadonnées de votre source de données. Dans cet exemple, vous allez inscrire les objets **Production/Product** à partir de l’espace de noms AdventureWorks Production. Voici comment procéder :

    a. Dans l’arborescence **Hiérarchie du serveur**, cliquez sur **Production**.

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b. Appuyez sur la touche CTRL et cliquez sur Product, ProductCategory, ProductDescription et ProductPhoto.

    c. Cliquez sur la flèche de déplacement des objets sélectionnés (**>**). Tous les objets Product sélectionnés sont alors déplacés dans la liste **Objets à inscrire**.

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d. Dans le champ **Ajouter des étiquettes**, entrez une description et/ou joignez une photo. Vous ajouterez ainsi des étiquettes de recherche à ces ressources de données. Les étiquettes sont un excellent moyen d’aider les utilisateurs à trouver une source de données inscrite.

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e. **Facultatif** : vous pouvez **Inclure un aperçu** et **Ajouter un expert de source de données**.

    f. Cliquez sur **INSCRIRE**. **Azure Data Catalog** inscrit les objets sélectionnés. Dans cet exercice, les objets sélectionnés à partir d’Adventure Works sont enregistrés.

    g. Pour afficher les objets de source de données que vous avez inscrits, cliquez sur **Afficher le portail**. Dans le portail **Azure Data Catalog**, vous pouvez afficher les objets de source de données sous forme de **mosaïques** ou de **liste**.

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

Dans cet exercice, vous avez inscrit des objets à partir de l’exemple de base de données Adventure Works afin qu’ils soient facilement détectables par les utilisateurs de l’ensemble de votre organisation. Dans l’exercice suivant, vous apprendrez comment découvrir les ressources de données inscrites.

## Exercice 3 : découverte des ressources de données inscrites

Dans cet exercice, vous allez utiliser le portail **Azure Data Catalog** pour découvrir les ressources de données inscrites et afficher leurs métadonnées. **Azure Data Catalog** fournit plusieurs outils permettant de découvrir des ressources de données, dont la recherche par mot clé simple, les filtres interactifs et une syntaxe de recherche avancée pour les utilisateurs « avancés ».

### Voici comment découvrir des ressources enregistrées

**Azure Data Catalog** fournit une syntaxe de recherche simple mais puissante qui vous permet de créer facilement des requêtes qui renvoient les données dont les utilisateurs ont besoin. Pour plus d’informations sur **Azure Data Catalog**, consultez [les informations de référence sur la syntaxe de recherche Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx).

**Azure Data Catalog** offre les options de recherche suivantes :

- Recherche par mot clé
- Filtre
- Recherche avancée

Vous pouvez également affiner les ressources de données à afficher. **Azure Data Catalog** offre les options d’affichage suivantes :

- Afficher les propriétés
- Afficher les colonnes
- Afficher un aperçu

Dans cet exemple, vous allez utiliser une recherche par mot clé. Dans **Azure Data Catalog**, plusieurs techniques de requête permettent de réaliser une recherche. Cet exemple utilise une requête de recherche de type **Regroupement**.

**Techniques de requête**

|Technique|Utilisation|Exemple
|---|---|---
|Étendue de la propriété|Renvoie des sources de données uniquement si le terme de recherche est mis en correspondance avec la propriété spécifiée|name:product
|Opérateurs logiques|Étendre ou limite une recherche à l’aide d’opérations booléennes, comme décrit dans la section Opérateurs booléens sur cette page|finance NOT entreprise
|Parenthèses de regroupement|Utilisez des parenthèses pour grouper les parties de la requête à isoler logiquement, notamment lorsque vous appliquez également des opérateurs booléens|name:product AND (tags:illustration OR tags:photo)
|Opérateurs de comparaison|Utilisez des comparaisons autres que l’égalité pour les propriétés comportant des types de données numériques et de date|creationTime:>11/05/14

Dans cet exemple, vous effectuez un **regroupement** pour rechercher des ressources de données pour lesquelles le nom correspond à « product » et les balises correspondent à « illustration » ou les balises correspondent à « photo ».

1. Accédez à https://azure.microsoft.com/services/data-catalog, cliquez sur **Prise en main**, puis connectez-vous au portail **Azure Data Catalog**.
2. Dans la zone **Rechercher dans le catalogue de données**, entrez une requête de type **Grouping** : (tags:description OR tags:photo).
3. Cliquez sur l’icône de recherche ou appuyez sur Entrée. **Azure Data Catalog** affichera les ressources de données correspondant à cette requête de recherche.

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

Dans cet exercice, vous avez utilisé le portail **Azure Data Catalog** pour découvrir et afficher les ressources de données Adventure Works inscrites dans le catalogue.

<a name="annotating"/>
## Exercice 4 : annotation des sources de données inscrites

Dans cet exercice, vous allez utiliser le portail **Azure Data Catalog** pour annoter des ressources de données préalablement inscrites dans le catalogue. Les annotations que vous ajouterez compléteront et amélioreront les métadonnées structurelles extraites de la source de données au cours de l’inscription et faciliteront la découverte et la compréhension des ressources de données. Comme chaque utilisateur du **catalogue de données** peut ajouter ses propres annotations, ces données sont faciles à partager par tous les utilisateurs ayant un point de vue sur celles-ci.

### Voici comment annoter les ressources de données

1. Accédez à https://azure.microsoft.com/services/data-catalog, cliquez sur **Prise en main**, puis connectez-vous au portail **Azure Data Catalog**.
2. Cliquez sur **Découvrir**.
3. Choisissez une ou plusieurs **ressources de données**. Dans cet exemple, choisissez **ProductPhoto** puis entrez « Photos de produit pour documents de marketing ».
4. Entrez une **Description** qui aide les autres utilisateurs à découvrir et à comprendre pourquoi et comment utiliser la ressource de données sélectionnée. Par exemple, entrez « Images du produit ». Vous pouvez également ajouter des étiquettes supplémentaires et afficher les colonnes.
5. Maintenant, vous pouvez essayer de rechercher et de filtrer pour découvrir les ressources de données à l’aide des métadonnées descriptives que vous avez ajoutées au catalogue.

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

Dans cet exercice, vous avez ajouté des informations descriptives aux ressources de données inscrites afin que les utilisateurs du catalogue puissent découvrir les sources de données à l’aide de termes qu’ils comprennent.

## Exercice 5 : métadonnées en crowdsourcing

Dans cet exercice, vous allez travailler avec un autre utilisateur pour ajouter des métadonnées aux ressources de données du catalogue. L’approche des annotations basée sur le crowdsourcing d’**Azure Data Catalog** permet aux utilisateurs d’ajouter des étiquettes, des descriptions et d’autres métadonnées afin que tout utilisateur ayant un point de vue sur une ressource de données et son utilisation puisse capturer ce point de vue et le mettre à disposition des autres utilisateurs.

> [AZURE.NOTE] Si aucun autre utilisateur ne peut travailler avec vous sur ce didacticiel, ne vous inquiétez pas ! Tout utilisateur qui accède au catalogue de données peut ajouter son propre point de vue au moment qui lui convient. Cette approche des métadonnées basée sur le crowdsourcing permet de développer le contenu du catalogue et la richesse de ses métadonnées au fil du temps.

### Voici comment enrichir les métadonnées des ressources de données par crowdsourcing

Demandez à un collègue de répéter l’exercice [Annotation des sources de données inscrites](#annotating) ci-dessus. Une fois que votre collègue a ajouté une description à une ressource de données, par exemple ProductPhoto, vous pouvez voir plusieurs annotations.

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

Dans cet exercice, vous avez exploré l’approche des métadonnées basée sur le crowdsourcing d’**Azure Data Catalog**, par laquelle chaque utilisateur du catalogue peut annoter les ressources de données qu’il découvre.

## Exercice 6 : connexion à des sources de données

Dans cet exercice, vous allez utiliser le portail **Azure Data Catalog** pour vous connecter à des sources de données en utilisant Microsoft Excel.

> [AZURE.NOTE] Il est important de se rappeler qu’**Azure Data Catalog** ne donne pas aux utilisateurs accès à la source de données elle-même : il leur permet simplement de découvrir et de comprendre plus facilement cette source de données. Lorsqu’un utilisateur se connecte à une source de données, l’application cliente qu’il choisit utilisera ses informations d’identification Windows ou l’invitera à saisir des informations d’identification, le cas échéant. Si l’accès à la source de données n’a pas été préalablement accordé à l’utilisateur, il devra obtenir cet accès avant de pouvoir se connecter.

### Voici comment se connecter à une source de données à partir d’Excel

1. Accédez à https://azure.microsoft.com/services/data-catalog, cliquez sur **Prise en main**, puis connectez-vous au portail **Azure Data Catalog**.
2. Cliquez sur **Découvrir**.
3. Choisissez une ressource de données. Dans cet exemple, choisissez ProductCategory.
4. Choisissez **Ouvrir dans** > **Excel**.

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. Dans la fenêtre **Avis de sécurité Microsoft Excel**, cliquez sur **Activer**.
6. Ouvrez le fichier **ProductCategory.odc**.
7. La source de données s’ouvre dans Excel.

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

Dans cet exercice, vous vous êtes connecté aux sources de données découvertes à l’aide d’**Azure Data Catalog**. Le portail **Azure Data Catalog** permet aux utilisateurs de se connecter directement en utilisant les applications clientes intégrées à son menu **Ouvrir dans...** et de se connecter avec l’application de leur choix, en utilisant les informations d’emplacement de connexion incluses dans les métadonnées des ressources.

## Exercice 7 : suppression des métadonnées des sources de données

Dans cet exercice, vous allez utiliser le portail **Azure Data Catalog** pour supprimer les aperçus de données des ressources de données inscrites et pour supprimer les ressources de données du catalogue.

> [AZURE.NOTE] Par défaut, le catalogue permet aux utilisateurs d’inscrire n’importe quelle source de données et de supprimer toutes les ressources de données préalablement inscrites. Les fonctionnalités de gestion incluses dans l’**édition Standard d’Azure Data Catalog** offrent des options supplémentaires qui permettent l’appropriation de ressources, ce qui limite les fonctions de découverte et de suppression des ressources à certains utilisateurs.

Dans **Azure Data Catalog**, vous pouvez supprimer une ressource individuelle ou plusieurs ressources.

### Voici comment supprimer plusieurs ressources de données

1. Accédez à https://azure.microsoft.com/services/data-catalog, cliquez sur **Prise en main**, puis connectez-vous au portail **Azure Data Catalog**.
2. Cliquez sur **Découvrir**.
3. Choisissez une ou plusieurs ressources de données.
4. Cliquez sur **Supprimer**.

Dans cet exercice, vous avez supprimé des ressources de données inscrites dans le catalogue.

## Exercice 8 : gestion des sources de données inscrites

Dans cet exercice, vous allez utiliser les fonctionnalités de gestion d’**Azure Data Catalog** pour vous approprier des ressources de données, contrôler ce que les utilisateurs peuvent découvrir et comment les utilisateurs gèrent ces ressources.

> [AZURE.NOTE] Les fonctionnalités de gestion décrites dans cet exercice sont disponibles uniquement dans l’**édition Standard d’Azure Data Catalog**, pas dans l’**édition gratuite**. Dans **Azure Data Catalog**, vous pouvez vous approprier des ressources de données, ajouter des copropriétaires aux ressources de données et définir la visibilité de ces ressources.

### Voici comment s’approprier les ressources de données et restreindre leur visibilité

1. Accédez à https://azure.microsoft.com/services/data-catalog, cliquez sur **Prise en main**, puis connectez-vous au portail **Azure Data Catalog**.
2. Cliquez sur **Découvrir**.
3. Choisissez une ou plusieurs ressources de données.
4. Dans le panneau **Propriétés**, section **Gestion**, cliquez sur **Prendre possession**.
5. Pour restreindre la visibilité, cliquez sur **Propriétaires et ces utilisateurs**.

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

Dans cet exercice, vous avez exploré les fonctionnalités de gestion **Azure Data Catalog** et limité la visibilité des ressources de données sélectionnées.

## Résumé

Dans ce didacticiel, vous avez exploré les fonctionnalités essentielles d’**Azure Data Catalog**, notamment l’inscription, l’annotation, la découverte et la gestion des sources de données d’entreprise. Maintenant que vous avez terminé ce didacticiel, il est temps de passer à la pratique. Vous pouvez commencer dès aujourd’hui en enregistrant les sources de données que vous et votre équipe utilisez, et en invitant vos collègues à utiliser le catalogue.

<!---HONumber=AcomDC_0420_2016-->