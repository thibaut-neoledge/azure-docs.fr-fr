<properties
   pageTitle="Azure Data Catalog prise en main du catalogue de données"
   description="Didacticiel de bout en bout des scénarios et des fonctionnalités d’Azure Data Catalog"
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Prise en main d’Azure Data Catalog

Cet article est un didacticiel de bout en bout expliquant les scénarios et les fonctionnalités de la version préliminaire publique d’Azure Data Catalog. Après vous être inscrit pour recevoir la version préliminaire, vous pouvez suivre ces étapes pour créer un catalogue de données puis enregistrer, annoter et découvrir des sources de données.

## Configuration requise pour le didacticiel

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-	**Un abonnement Azure** : si vous n’en possédez pas, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Consultez l'article [Essai gratuit](http://azure.microsoft.com/pricing/free-trial/) pour plus d'informations.
-	**Azure Active Directory** : Azure Data Catalog utilise [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) pour la gestion des identités et des accès.
-	**Des sources de données** : Azure Data Catalog comprend des fonctionnalités de détection de sources de données. Pour poursuivre le didacticiel, vous devez avoir accès à une ou plusieurs sources de données. Le didacticiel est construit sur des exemples de bases de données Adventure Works, mais vous pouvez utiliser n’importe quelle source de données prise en charge si vous préférez travailler avec des données plus familières et mieux adaptées à votre rôle.

## Exercice 1 : installation d’un exemple de base de données Adventure Works

Dans cet exercice, vous installez l’exemple Adventure Works pour le moteur de base de données SQL Server et SQL Server Analysis Services Multidimensional. Ces exemples sont utilisés dans les exercices suivants.

> [AZURE.NOTE]Cet exercice est facultatif. Les exercices restants du didacticiel sont écrits de manière à référencer les exemples de bases de données Adventure Works, mais vous pouvez également choisir d’ignorer cet exercice et de travailler avec vos propres sources de données. Les étapes de l’installation d’Adventure Works sont les suivantes.

### Installer les bases de données Adventure Works OLTP 2014 et Data Warehouse

La base de données Adventure Works OLTP prend en charge les scénarios de traitement des transactions en ligne standard pour un fabricant fictif de bicyclettes (Adventure Works Cycles), y compris la fabrication, les ventes et les achats. La base de données Adventure Works DW montre comment construire un entrepôt de données.

Les bases de données se trouvent dans http://msftdbprodsamples.codeplex.com/ et peuvent être installées en suivant les étapes décrites dans [Installation des exemples de bases de données Adventure Works 2014].

Dans cet exercice, vous avez installé les exemples de bases de données Adventure Works qui sont utilisés dans les exercices restants. Si vous avez choisi d’ignorer cet exercice et d’utiliser vos propres sources de données d’entreprise, préparez-vous à mémoriser des noms, des balises et d’autres métadonnées.

## Exercice 2 : enregistrement des sources de données

Dans cet exercice, vous allez utiliser l’outil d’enregistrement d’Azure Data Catalog pour enregistrer vos sources de données avec le catalogue. L’enregistrement est le processus par lequel les métadonnées structurelles clés (telles que les noms, les types et les emplacements) sont extraites de la source de données et des ressources qu’elle contient, puis copiées dans le catalogue. Les sources de données et leurs données restent au même endroit, mais les métadonnées sont utilisées par le catalogue pour les rendre plus facilement détectables et compréhensibles.

### Voici comment enregistrer une source de données

1.	Connectez-vous au portail Azure Data Catalog.

    ![register1][1]

2.	Faites défiler vers le bas, puis cliquez sur **Publier les données**.

    ![register2][2]
3.	Cliquez sur **Lancer l’application**.
4.	Sur la page **Bienvenue**, cliquez sur **Connexion**, puis entrez vos informations d’identification.
5.	Sur la page **Microsoft Azure Data Catalog**, cliquez sur **SQL Server**.

    ![register3][3]
6.	Entrez votre **Nom du serveur**, puis cliquez sur **CONNEXION**.
7.	La page suivante est celle où vous enregistrerez les métadonnées de votre source de données. Dans cet exemple, vous allez enregistrer les objets **Product** à partir de l’espace de noms AdventureWorks Production. Voici comment procéder :
    
    a. Dans l’arborescence, cliquez sur **Production**.

    b. Appuyez sur la touche CTRL et cliquez sur Product, ProductCategory, ProductDescription et ProductPhoto.

    ![register4][4]

    c. Cliquez sur la flèche de déplacement des objets sélectionnés (\*\*>\*\*). Tous les objets Product sont alors déplacés dans la liste **À enregistrer**.

    ![register5][5]

    d. **Facultatif** : vous pouvez **Inclure un aperçu** et **Ajouter un expert de source de données**.

    e. Dans le champ **Ajouter des balises**, entrez une description, joignez une photo. Vous ajouterez ainsi des balises de recherche à ces ressources de données. Les balises sont un excellent moyen d’aider les utilisateurs à trouver une source de données enregistrée.
 
    f. Cliquez sur **ENREGISTRER**. Azure Data Catalog enregistre les objets que vous avez sélectionnés. Dans cet exercice, les objets sélectionnés à partir d’Adventure Works sont enregistrés.

    ![register6][6]

    g. Pour afficher les objets de source de données que vous avez enregistrés, cliquez sur **Afficher Portail**. Sur le portail Azure Data Catalog, vous pouvez afficher les objets de source de données sous forme de **vignettes** ou de **liste**.

    ![register7][7]

Dans cet exercice, vous avez enregistré des objets à partir des exemples de base de données Adventure Works afin qu’ils soient facilement détectables par les utilisateurs de l’ensemble de votre organisation. Dans l’exercice suivant, vous apprendrez comment découvrir les ressources de données enregistrées.

## Exercice 3 : découverte des ressources de données enregistrées

Dans cet exercice, vous allez utiliser le portail Azure Data Catalog pour découvrir les ressources de données enregistrées et afficher leurs métadonnées. Azure Data Catalog fournit plusieurs outils permettant de découvrir des ressources de données, dont la recherche par mot clé simple, les filtres interactifs et une syntaxe de recherche avancée pour les utilisateurs « avancés ».

### Voici comment découvrir des ressources enregistrées

**Azure Data Catalog** fournit une syntaxe de recherche simple mais puissante qui vous permet de créer facilement des requêtes qui renvoient les données dont les utilisateurs ont besoin. Pour plus d’informations sur **Azure Data Catalog**, consultez les informations de référence sur la syntaxe de recherche.

**Azure Data Catalog** offre les options de recherche suivantes :

- Recherche par mot clé
- Filtre
- Recherche avancée

Vous pouvez également affiner les ressources de données à afficher. **Azure Data Catalog** offre les options d’affichage suivantes :

- Afficher les propriétés
- Afficher les colonnes
- Afficher un aperçu

Dans cet exemple, vous allez utiliser une recherche par mot clé. Dans **Azure Data Catalog**, plusieurs techniques de requête permettent de réaliser une recherche. Cet exemple utilise une requête de recherche de type **Regroupement**.

**Techniques de requête**<table><tr><td><b>Technique</b></td><td><b>Utilisation</b></td><td><b>Exemple</b></td></tr><tr><td>Étendue de propriété</td><td>Renvoie uniquement les sources de données pour lesquelles le terme de recherche correspond à la propriété spécifiée</td><td>name:product</td></tr><tr><td>Opérateurs logiques</td><td>Élargit ou restreint une recherche à l’aide d’opérations booléennes, comme décrit sur cette même page dans la section Opérateurs booléens</td><td>finance NOT corporate</td></tr><tr><td>Regroupement avec parenthèses</td><td>Permet de regrouper des éléments de la requête entre parenthèses pour les isoler de manière logique, notamment avec les opérateurs booléens</td><td>namem:product AND (tags:illustration OR tags:photo)</td></tr><tr><td>Opérateurs de comparaison</td><td>Utilisent des comparaisons autres que l’égalité pour les propriétés associées à des données de type numérique et date</td><td>creationTime:&gt;11/05/14</td></tr></table>

Dans cet exemple, vous effectuez un **regroupement** pour rechercher des ressources de données pour lesquelles le nom correspond à « product » et les balises correspondent à « illustration » ou les balises correspondent à « photo ».

1.	Connectez-vous au portail **Azure Data Catalog**.
2.	Cliquez sur **Découvrir**.
3.	Dans la zone **Recherche**, entrez une requête de type **Regroupement** : (balises:description OR balises:photo). 
4.	Cliquez sur l’icône de recherche ou appuyez sur Entrée. **Azure Data Catalog** affichera les ressources de données correspondant à cette requête de recherche.
    
    ![recherche][8]

Dans cet exercice, vous avez utilisé le portail **Azure Data Catalog** pour détecter et afficher les ressources de données enregistrées dans le catalogue.

## Exercice 4 : annotation des sources de données enregistrées

Dans cet exercice, vous utiliserez le portail **Azure Data Catalog** pour annoter des ressources de données préalablement enregistrées dans le catalogue. Les annotations que vous ajouterez compléteront et amélioreront les métadonnées structurelles extraites de la source de données au cours de l’enregistrement et faciliteront la découverte et la compréhension des ressources de données. Comme chaque utilisateur du **catalogue de données** peut ajouter ses propres annotations. Ces données sont faciles à partager par tous les utilisateurs ayant un point de vue sur celles-ci.

### Voici comment annoter les ressources de données

1.	Connectez-vous au portail **Azure Data Catalog**.
2.	Cliquez sur **Découvrir**.
3.	Choisissez une ou plusieurs **Ressources de données**. Dans cet exemple, choisissez **ProductPhoto** puis entrez « Photos de produit pour documents de marketing ».
4.	Entrez une **Description** qui aidera les autres utilisateurs à découvrir et comprendre pourquoi et comment utiliser la ressource de données sélectionnée. Par exemple, entrez « Images du produit ». Vous pouvez également ajouter des balises supplémentaires et afficher les colonnes.
5.	Maintenant, vous pouvez essayer de rechercher et de filtrer pour découvrir les ressources de données à l’aide des métadonnées descriptives que vous avez ajoutées au catalogue.

    ![annoter][9]

Dans cet exercice, vous avez ajouté des informations descriptives aux ressources de données enregistrées afin que les utilisateurs du catalogue puissent découvrir la source de données à l’aide de termes qu’ils comprennent.

## Exercice 5 : métadonnées en crowdsourcing

Dans cet exercice, vous allez travailler avec un autre utilisateur pour ajouter des métadonnées aux ressources de données du catalogue. L’approche des annotations basée sur le crowdsourcing d’Azure Data Catalog permet aux utilisateurs d’ajouter des balises, des descriptions et d’autres métadonnées afin que tout utilisateur ayant un point de vue sur une ressource de données et son utilisation puisse capturer ce point de vue et le mettre à disposition des autres utilisateurs.

> [AZURE.NOTE]Si aucun autre utilisateur ne peut travailler avec vous sur ce didacticiel, ne vous inquiétez pas ! Tout utilisateur qui accède au catalogue de données peut ajouter son propre point de vue au moment qui lui convient. Cette approche des métadonnées basée sur le crowdsourcing permet de développer le contenu du catalogue et la richesse de ses métadonnées au fil du temps.

### Voici comment enrichir les métadonnées des ressources de données par crowdsourcing

Demandez à un collègue de répéter l’exercice **Annotation des sources de données enregistrées** ci-dessus. Une fois que votre collègue aura ajouté une description à une ressource de données, par exemple ProductPhoto, vous pourrez voir plusieurs annotations.

Dans cet exercice, vous avez exploré l’approche des métadonnées basée sur le crowdsourcing d’Azure Data Catalog, par laquelle chaque utilisateur du catalogue peut annoter les ressources de données qu’il découvre. Exercice : connexion à des sources de données. Dans cet exercice, vous allez utiliser le portail **Azure Data Catalog** pour vous connecter aux sources de données à l’aide de Microsoft Excel.

> [AZURE.NOTE]Il est important de rappeler qu’**Azure Data Catalog** ne donne pas aux utilisateurs accès à la source de données elle-même : il leur permet simplement de découvrir et de comprendre plus facilement cette source de données. Lorsqu’un utilisateur se connecte à une source de données, l’application cliente qu’il choisit utilisera ses informations d’identification Windows ou l’invitera à saisir des informations d’identification, le cas échéant. Si l’accès à la source de données n’a pas été préalablement accordé à l’utilisateur, il devra obtenir cet accès avant de pouvoir se connecter.

### Voici comment se connecter à une source de données à partir d’Excel

1.	Connectez-vous au portail **Azure Data Catalog**.
2.	Cliquez sur **Découvrir**.
3.	Choisissez une ressource de données. Dans cet exemple, choisissez ProductCategory.
4.	Choisissez **Ouvrir dans** > **Excel**.

    ![connect1][10]
5.	Dans la fenêtre **Avis de sécurité Microsoft Excel**, cliquez sur **Activer**.
6.	Ouvrez le fichier **ProductCategory.odc**.
7.	La source de données s’ouvre dans Excel.

    ![connect2][11]

Dans cet exercice, vous vous êtes connecté aux sources de données découvertes à l’aide d’Azure Data Catalog. Le portail **Azure Data Catalog** permet aux utilisateurs de se connecter directement à l’aide d’applications clientes intégrées à son menu **Ouvrir dans...** et de se connecter à l’aide de n’importe quelle application de leur choix en utilisant les informations relatives à l’emplacement de la connexion incluses dans les métadonnées des ressources.

## Exercice 6 : suppression des métadonnées des sources de données

Dans cet exercice, vous utiliserez le portail **Azure Data Catalog** pour supprimer les aperçus de données des ressources de données enregistrées et pour supprimer les ressources de données du catalogue.

> [AZURE.NOTE]Par défaut, le catalogue permet aux utilisateurs d’enregistrer n’importe quelle source de données et de supprimer toutes les ressources de données préalablement enregistrées. Les fonctionnalités de gestion incluses dans l’**édition Standard d’Azure Data Catalog** fournissent des options supplémentaires permettant de vous approprier des ressources et de restreindre les fonctions de découverte et de suppression des ressources à certains utilisateurs.

Dans **Azure Data Catalog**, vous pouvez supprimer l’aperçu de la suppression d’une ressource individuelle ou de plusieurs ressources de données.

### Voici comment supprimer plusieurs ressources de données

1.	Connectez-vous au portail **Azure Data Catalog**.
2.	Cliquez sur **Découvrir**.
3.	Choisissez une ou plusieurs ressources de données.
4.	Cliquez sur **Supprimer**.

Dans cet exercice, vous avez supprimé des ressources de données enregistrées dans le catalogue.

## Exercice 7 : gestion des sources de données enregistrées

Dans cet exercice, vous allez utiliser les fonctionnalités de gestion d’**Azure Data Catalog** pour vous approprier des ressources de données et contrôler les utilisateurs qui peuvent détecter et gérer ces ressources.

Remarque : les fonctionnalités de gestion décrites dans cet exercice sont disponibles uniquement dans l’édition Standard d’Azure Data Catalog, pas dans l’édition gratuite. Dans **Azure Data Catalog**, vous pouvez vous approprier des ressources de données, ajouter des copropriétaires aux ressources de données et définir la visibilité de ces ressources.

### Voici comment s’approprier les ressources de données et restreindre leur visibilité

1.	Connectez-vous au portail **Azure Data Catalog**.
2.	Cliquez sur **Découvrir**.
3.	Choisissez une ou plusieurs ressources de données.
4.	Dans le panneau **Propriétés**, section **Gestion**, cliquez sur **Appropriation**.
5.	Pour restreindre la visibilité, cliquez sur **Propriétaires & ces utilisateurs**.

    ![propriété][12]

Dans cet exercice, vous avez exploré les fonctionnalités de gestion du catalogue et limité la visibilité des ressources de données sélectionnées.

## Résumé

Dans ce didacticiel, vous avez exploré les fonctionnalités essentielles de la version d’évaluation d’**Azure Data Catalog**, notamment l’enregistrement, l’annotation, la découverte et la gestion des sources de données d’entreprise. Maintenant que vous avez terminé ce didacticiel, il est temps de passer à la pratique. Vous pouvez commencer dès aujourd’hui en enregistrant les sources de données que vous et votre équipe utilisez, et en invitant vos collègues à utiliser le catalogue.


<!--Image references-->
[1]: ./media/data-catalog-get-started/register1.png
[2]: ./media/data-catalog-get-started/register2.png
[3]: ./media/data-catalog-get-started/register3.png
[4]: ./media/data-catalog-get-started/register4.png
[5]: ./media/data-catalog-get-started/register5.png
[6]: ./media/data-catalog-get-started/register6.png
[7]: ./media/data-catalog-get-started/register7.png
[8]: ./media/data-catalog-get-started/search.png
[9]: ./media/data-catalog-get-started/annotate.png
[10]: ./media/data-catalog-get-started/connect1.png
[11]: ./media/data-catalog-get-started/connect2.png
[12]: ./media/data-catalog-get-started/ownership.png

<!---HONumber=July15_HO5-->