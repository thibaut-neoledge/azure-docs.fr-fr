<properties
   pageTitle="Présentation d’Azure Data Catalog"
   description="Présentation de Microsoft Azure Data Catalog, y compris de ses fonctionnalités et des problèmes qu’il peut résoudre. Azure Data Catalog fournit des fonctionnalités qui permettent à tous les utilisateurs (analystes, scientifiques, développeurs) d’inscrire, de détecter, de comprendre et d’utiliser des sources de données."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="08/21/2015"
   ms.author="maroche"/>

# Qu’est-ce qu’Azure Data Catalog ?

Microsoft **Azure Data Catalog** est un service cloud entièrement géré qui sert de système d’inscription et de détection des sources de données d'entreprise. **Azure Data Catalog** fournit des fonctionnalités qui permettent à tous les utilisateurs (des analystes aux scientifiques de données, en passant par les développeurs) d’inscrire, de détecter, de comprendre et d’utiliser des sources de données.

## Description du problème - motivation et vue d'ensemble

La détection de sources de données d’entreprise est un processus organique basé sur des connaissances tribales. Elle présente de nombreux défis aux entreprises qui souhaitent tirer le meilleur parti de leurs informations.

-	Les utilisateurs ne savent pas que ces sources de données existent, sauf s’ils entrent en contact avec elle dans le cadre d'un autre processus ; aucun emplacement central ne leur permet d’inscrire des sources de données.
-	Un utilisateur ne peut pas se connecter aux données à l'aide d'une application cliente s’il ne connaît pas l’emplacement d’une source de données ; les expériences de consommation de données requièrent que les utilisateurs connaissent la chaîne de connexion ou le chemin d'accès.
-	L’utilisateur ne peut pas comprendre les utilisations prévues des données s’il ne connaît pas l’emplacement d’une source de données ; les sources de données et la documentation résident à des emplacements différents et elles sont consommées via différentes expériences.
-	Si un utilisateur souhaite obtenir des précisions sur une ressource d'informations, il doit localiser l’expert ou l'équipe responsable des données et solliciter ces experts en mode hors connexion ; aucune connexion explicite n’est établie entre les données et les personnes pouvant apporter leurs points de vue d’expert sur leurs utilisations.
-  Sauf si un utilisateur comprend le processus de demande d’accès à la source de données, la découverte de la source de données et de sa documentation ne lui permet toujours pas d'accéder aux données qu'il requiert.

Pendant que les consommateurs de données relèvent ces défis, les utilisateurs responsables de la production et de la gestion des ressources d’informations relèvent, quant à eux, des défis qui leur sont propres.

-	Annoter des sources de données avec des métadonnées descriptives est souvent peine perdue, car les applications clientes ignorent généralement les descriptions stockées dans la source de données.
-	Créer une documentation sur les sources de données est souvent peine perdue, car sa synchronisation avec la source de données doit être permanente. De plus, les utilisateurs ne font généralement pas confiance aux documentations, car ils les considèrent comme obsolètes.
- Restreindre l'accès à la source de données et veiller à ce que les consommateurs de données sachent comment demander l'accès est un défi récurrent.

Créer et gérer une documentation sur une source de données est une tâche plutôt longue et complexe. Le défi consistant à rendre disponible cette documentation pour tous les utilisateurs de la source de données l’est souvent encore plus.

Lorsqu’ils sont combinés, ces défis représentent un obstacle majeur pour les entreprises qui souhaitent encourager et promouvoir l'utilisation et la compréhension des données d'entreprise.

## Description du service

**Azure Data Catalog** est conçu pour résoudre ces problèmes et permettre aux entreprises de tirer le meilleur parti de leurs ressources d’informations existantes, en les rendant facilement détectables et compréhensibles pour les utilisateurs qui ont besoin de ces données gérées.

**Azure Data Catalog** fournit un service cloud dans lequel des données source peuvent être inscrites. Les données restent à leur emplacement existant, mais une copie des métadonnées, ainsi qu'une référence à l'emplacement de la source de données, sont ajoutées à **Azure Data Catalog**. Ces métadonnées sont également indexées de manière à ce que chaque source de données soit facilement détectable via la recherche, et compréhensible pour les utilisateurs qui la découvrent.

Une fois qu'une source de données a été inscrite, ses métadonnées peuvent ensuite être enrichies, par l'utilisateur qui a réalisé l’inscription ou par d'autres utilisateurs de l'entreprise. Tous les utilisateurs peuvent annoter une source de données en fournissant des descriptions, des balises ou d'autres métadonnées, telles que de la documentation, et traiter la demande d’accès à la source de données. Ces métadonnées descriptives complètent les métadonnées structurelles (telles que les noms de colonnes et les types de données) inscrites à partir de la source de données, pour faciliter leur détection et leur compréhension.

La détection, la compréhension et l’utilisation des sources de données sont le principal objectif de l’inscription des sources. Lorsque les utilisateurs de l'entreprise ont besoin de données pour mener leurs efforts (aide à la décision, développement d'applications, science de données ou toute autre tâche qui nécessite des données spécifiques), ils peuvent utiliser l’expérience de détection d’**Azure Data Catalog** pour rechercher rapidement les données qui correspondent à leurs besoins, comprendre les données afin d’évaluer leur aptitude à l’emploi et utiliser ces données en ouvrant la source de données via l’outil de leur choix.

## Inscription des sources de données

L’inscription des sources de données est réalisée via l’outil d’inscription des sources de données d’**Azure Data Catalog** Vous pouvez télécharger cette application à partir du portail ** Azure Data Catalog**.

Le processus d’inscription comporte trois étapes de base :

1.	Se connecter à une source de données : l'utilisateur spécifie l'emplacement de la source de données et les informations d'identification pour se connecter à la source de données, telle qu'une instance SQL Server.
2.	Sélectionner des objets à inscrire : l'utilisateur sélectionne les objets de l'emplacement spécifié qui doivent être inscrits via **Azure Data Catalog**. Il peut s’agir d’un ensemble de tables présentes sur toutes les bases de données du serveur, ou d’un sous-ensemble de tables et de vues spécifiques.
3.	Terminer l’inscription : l'utilisateur termine le processus et l'outil d'inscription des sources de données extrait les métadonnées structurelles à partir de la source de données, qui envoie ensuite les métadonnées au service cloud **Azure Data Catalog**.

> [AZURE.NOTE]Dans la version préliminaire, **Azure Data Catalog** prend actuellement en charge les sources de données et les types de ressources suivants :

- Table SQL Server
- Vue SQL Server
- Table de base de données Oracle
- Vue de base de données Oracle
- Dimension multidimensionnelle SQL Server Analysis Services
- Mesure multidimensionnelle SQL Server Analysis Services
- Indicateur clé de performance multidimensionnel SQL Server Analysis Services
- Table tabulaire SQL Server Analysis Services
- Rapport SQL Server Reporting Services
- Azure Storage Blob
- Répertoire de stockage Azure

Les sources de données et les types de ressources supplémentaires seront inclus dans la version préliminaire d’**Azure Data Catalog**.

> [AZURE.IMPORTANT]L’inscription d’une source de données dans **Azure Data Catalog** ne copie pas les données à partir de la source de données, sauf si vous sélectionnez « Inclure une version préliminaire » dans l’outil d’inscription des sources de données. L’inscription copie les métadonnées des sources de données, et non les données. Exemples de métadonnées : les noms de tables et autres objets de sources de données, les noms et les types des données, les noms de colonnes et d'autres attributs de sources de données. Les métadonnées incluent également l’emplacement de la source de données, pour que les utilisateurs qui détectent les données source à l’aide d’**Azure Data Catalog** puissent se connecter à la source de données. Si vous sélectionnez « Inclure une version préliminaire », l’outil d’inscription des sources de données copiera sur **Azure Data Catalog** un petit jeu d’enregistrements qui sera affiché, pour les utilisateurs qui détectent la source de données, dans le portail **Azure Data Catalog**.

## Enrichissement des métadonnées de sources de données

Une fois l’inscription terminée, les sources de données peuvent être détectées et consommées, mais la valeur true d’**Azure Data Catalog** provient du fait que les métadonnées descriptives se trouvent dans la même expérience que les métadonnées structurelles extraites de la source de données Ces métadonnées supplémentaires offrent trois avantages importants :

-	Les sources de données inscrites sont plus facilement détectables. Les métadonnées fournies par l’utilisateur sont ajoutées à l’index de recherche d’**Azure Data Catalog**. Ceci permet aux utilisateurs de détecter les données à l'aide de termes et de concepts qui ne se trouvent peut-être pas dans la source de données d'origine. Le fait d’attribuer, par exemple, un nom convivial « client » à une table de base de données contenant des données client, nommée « tbl\_c45 », permettra aux utilisateurs qui recherchent des données client de la détecter plus facilement. Le fait de fournir une description qui inclut les noms de rapports, les tableaux de bord ou les procédés qui utilisent les données, permettra également aux utilisateurs qui utilisent ces artefacts en aval comme termes de recherche, de détecter plus facilement la source de données.
-	Les sources de données inscrites sont plus facilement compréhensibles une fois détectées. Les métadonnées fournies par l’utilisateur sont présentées aux utilisateurs d’**Azure Data Catalog** qui affichent la source de données annotée, qui leur permet de fournir un contexte et des informations supplémentaires. En règle générale, les sources de données ne fournissent aucune description ou documentation significative, et celles qui en fournissent sont généralement destinées aux administrateurs ou développeurs de base de données. En enrichissant les sources de données d’**Azure Data Catalog** avec des balises et des descriptions appropriées aux audiences, les utilisateurs peuvent s’assurer que ceux qui détectent les données peuvent comprendre leurs descriptions et leurs utilisations prévues.
-  Chaque source de données inscrite peut inclure des informations d'accès de requête, pour que les utilisateurs puissent facilement comprendre et suivre les processus existants pour demander l'accès à la source de données et ses données.

> [AZURE.NOTE]Chaque utilisateur d’** Azure Data Catalog** peut ajouter ses propres balises et descriptions de ressources de données et d’attributs. **Azure Data Catalog** suivra la valeur et la source de chaque annotation et affichera l’utilisateur et la date à laquelle l’annotation a été ajoutée. Cette approche de crowdsourcing portant sur les métadonnées permet de s’assurer que chaque utilisateur ayant un point de vue sur les données et leurs utilisations peut l’exprimer et partager ses ressources avec l’ensemble des utilisateurs.

## Exploration, détection et compréhension

L’objectif de l’inscription et de l’enrichissement des sources de données dans **Azure Data Catalog** est de faire en sorte que ces données puissent être détectées, comprises et utilisées par les utilisateurs au sein de l’entreprise. Le portail **Azure Data Catalog** est l’outil principal de ce processus.

Le portail **Azure Data Catalog** fournit deux mécanismes principaux d’exploration et de détection de données : la recherche et le filtrage.

Pour rechercher des sources de données dans **Azure Data Catalog**, entrez simplement un terme de recherche dans la zone de recherche du portail **Azure Data Catalog**. Le portail affichera une vignette pour chaque source de données inscrite qui correspond au terme de recherche ; les vignettes contiendront le nom, la description et les balises attribués à la source de données, ainsi que d'autres informations générales.

Pour filtrer le contenu d’**Azure Data Catalog**, sélectionnez simplement un ou plusieurs filtres présentés dans le portail **Azure Data Catalog**. Ceci ne permettra d’afficher dans le portail que les vignettes correspondant aux critères de filtre qui ont été spécifiés. Vous pouvez filtrer les sources de données sans effectuer de recherche, ou bien filtrer les résultats d'une recherche.

Pour afficher des informations plus détaillées sur une source de données et découvrir si elle est appropriée à la tâche à accomplir, cliquez simplement sur la vignette de la source de données ; le volet Propriétés apparaîtra et contiendra toutes ses métadonnées.

Des boutons supplémentaires apparaîtront en haut du volet Propriétés :

1.	Version préliminaire : cliquer sur ce bouton fera apparaître le jeu statique d'enregistrements de la version préliminaire à partir de la source de données si la version préliminaire a été sélectionnée lors de l’inscription de la source de données.
2.	Schéma : cliquer sur ce bouton fera apparaître le schéma de la source de données, y compris les noms de colonnes, les types de données et les métadonnées figurant dans les colonnes d’**Azure Data Catalog**.

> [AZURE.NOTE]N’oubliez pas que l’expérience de **détection** peut être un point d’entrée dans l’expérience d’**enrichissement** et non seulement dans l’expérience de **consommation**. L’approche de crowdsourcing qu’apporte **Azure Data Catalog** signifie que tous les utilisateurs qui détectent des sources de données inscrites peuvent partager leur avis sur les données, en plus de les utiliser.

## Suppression des métadonnées de sources de données

Une fois la source de données inscrite, il est parfois nécessaire de supprimer la référence de la source de données à partir d’**Azure Data Catalog**. Ceci peut être dû à l'évolution des besoins de l'entreprise ou du système source qui a été supprimé. Quelle que soit la raison, **Azure Data Catalog** permet de supprimer plus facilement des sources de données en choisissant simplement de les supprimer de manière à ce qu’elles ne puissent plus être détectées et utilisées.

> [AZURE.IMPORTANT]Lorsque vous supprimez une source de données à partir d’**Azure Data Catalog**, vous ne supprimez en réalité que les métadonnées qui sont stockées dans le service **Azure Data Catalog**. Cela n’affecte encore aucun cas la source de données d'origine.

## Utilisation des sources de données

L'objectif ultime de la détection de données est de rechercher les données dont vous avez besoin et de les utiliser dans l'outil de données de votre choix. L'expérience de consommation de données dans Azure Data Catalog active cette fonctionnalité de deux manières.

1.	Pour les applications clientes qui sont directement prises en charge par **Azure Data Catalog**, les utilisateurs peuvent cliquer sur le menu **Ouvrir dans** de la vignette de la source de données se trouvant dans le portail. L’application cliente se lancera alors en se connectant à la source de données sélectionnée.
2.	Pour toutes les applications clientes, les utilisateurs peuvent utiliser les informations de connexion affichées dans le volet Propriétés pour une source de données sélectionnée. Cette information inclut tous les détails (tels que le nom du serveur, le nom de la base de données et le nom de l'objet) requis pour se connecter aux données et peut être copiée dans l'expérience de connexion de l'outil client. Si les détails d'accès de requête ont été fournis pour une source de données, cette information s'affichera à côté des détails de connexion.

> [AZURE.NOTE]Dans la version préliminaire d’Azure Data Catalog, seuls Microsoft Excel et SQL Server Reporting Services Report Manager seront directement pris en charge et disponibles dans le menu **Ouvrir dans**.

<!---HONumber=Oct15_HO3-->