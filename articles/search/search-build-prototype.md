<properties 
	pageTitle="Créer une application de prototype pour Azure Search | Microsoft Azure | Service de recherche cloud hébergé" 
	description="Créez votre premier prototype d'application pour prendre en main Azure Search, un service de recherche cloud hébergé." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="heidist"/>

# Générer une application de prototype pour Azure Search

Les développeurs qui évaluent Azure Search pour la recherche sur cloud hébergée commencent presque toujours avec une application de test préliminaire qui démontre la valeur que représente l'ajout d'Azure Search à une application personnalisée. Dans cet article, nous vous donnons quelques éléments pour accélérer le processus de création de prototype.
 
- Un projet C# Visual Studio qui inclut des fichiers data.json et schema.json. Le fait de disposer d'exemples de données vous permet de générer et d'exécuter immédiatement la solution, pour confirmer qu'elle fonctionne sur votre système avant d'écrire une seule ligne de code. 

	Si possible, essayez de remplacer ces fichiers de schéma et de données autonomes et déconnectés par des données de votre entreprise. Ensuite, lorsque vous exécutez l'application, l'index qui est créé est basé sur votre schéma et les documents basés sur vos fichiers de données seront immédiatement disponibles à l'utilisation dans Azure Search.

- Conseils sur la manière de structurer les données que vous allez télécharger dans Azure Search. Il vous faut un jeu de données pour chaque index. Azure Search utilise JSON pour la sérialisation de données.

- Enfin, nous vous présenterons quelques fonctionnalités supplémentaires qui feront évoluer votre prototype au niveau supérieur, avec l'intégration de fonctionnalités de recherche spécifiques, telles que la navigation à facettes, des profils de score pour optimiser les résultats, des requêtes prédictives ou avec saisie semi-automatique et une fonctionnalité liée à la page de recherche qui n'est pas disponible dans la recherche de texte intégral alternative.

Au final, vous disposerez d'une application prototype, utilisant vos données, pour illustrer une expérience de recherche robuste à l'aide d'Azure Search.

## Préparation des données

Les opérations de recherche sont exécutées sur un index de recherche que vous créez dans Azure Search. Un index comporte plusieurs parties, y compris des profils de score et des générateurs de suggestions, mais la majeure partie d'un index se compose de champs de données utilisés dans les opérations de recherche.

Le jeu de données fournissant les champs doit être un fichier de données plat (dans JSON), où chaque champ dans votre jeu de données est mappé à un champ équivalent dans l'index que vous tentez de remplir. D'autres parties de l'index, comme un profil de score ou des options CORS, sont ajoutées de manière indépendante, directement dans le schéma.

Les sources de données qui utilisent JSON en mode natif, comme une table de stockage Azure, fournissent des données plus faciles à utiliser que les données relationnelles. Une vue ou table unique fournit tous les champs dans un seul ensemble de lignes.

Pour télécharger les fichiers de données et de schéma à l'aide du code dans l'exemple d'application prototype, vous pouvez remplacer les fichiers de données et de schéma par défaut qui sont intégrés dans la solution.

> [AZURE.NOTE]Vous pouvez charger plusieurs fichiers de données, par exemple pour télécharger les données par lots, mais la structure des données doit être la même pour chaque fichier de données que vous téléchargez vers le même index. Vous ne pouvez pas joindre des index ; chaque index fonctionne comme un corpus de recherche autonome.

## Tester la solution de prototype sur votre système

1. [Création d’un service Azure Search dans le Portail Azure Classic](search-create-service-portal.md).

    Vous pouvez ajouter une version partagée (gratuite) du service à n'importe quel abonnement Azure existant. Le service partagé est souvent utilisé pour les prototypes. N'oubliez pas que le service partagé offre 50 Mo de données ou un total de 10 000 documents, selon ce qui se produit en premier. En outre, les documents et les données peuvent être répartis sur un maximum de 3 index.

    L'exécution de l'exemple d'application prototype, avec les exemples de fichiers de données intégrés, crée un index nommé « musicstoreindex », contenant 246 documents, à 278 Ko sur votre service Azure Search. Plus loin dans cette procédure détaillée, vous remplacerez cet index par un nouveau à l'aide de données Adventure Works qui consommeront jusqu'à ## documents et ## Mo.

2. [Télécharger la solution du générateur de prototypes](http://go.microsoft.com/fwlink/p/?LinkId=536479). Il s'agit d'une solution Visual Studio 2013 en C# qui génère une application de console utilisée pour créer, charger et interroger un index. Si vous ne disposez pas déjà de Visual Studio, vous pouvez obtenir la version [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) gratuite.

3. Modifiez le fichier app.config pour ajouter des paramètres de configuration qui ciblent votre service de recherche et la clé API.

	Vous pouvez obtenir l'URL et la clé API administrateur depuis le [tableau de bord du service dans le portail](search-create-service-portal.md). Pour l'URL, entrez le chemin d'accès complet du nom du service, y compris le préfixe https et le domaine `search.windows.net`.

4. Générez la solution pour vous assurer qu'elle ne contient aucune erreur. Vous devrez peut-être mettre à jour des packages pour résoudre les erreurs de build. Dans la solution, cliquez avec le bouton droit sur **Gérer les packages NuGet** pour mettre à jour les packages.

5. Exécutez la solution à l'aide des fichiers de données et de schéma intégrés. Cette étape est facultative, mais elle confirme le fonctionnement de la solution avant que vous ne passiez du temps à ajouter vos propres données. La console affiche les messages suivants :

	- Index supprimé (se produit uniquement s'il existe un index avec le nom « musicstoreindex »)
	- Index créé (un nouvel index nommé « musicstoreindex » est créé dans votre service)
	- Documents publiés (un message pour chaque fichier JSON)
	- Attente de 5 secondes (permet à l'indexation de se terminer avant la première requête)
	- Résultats de la recherche pour le terme « best » (meilleur) sans valorisation (exécute une requête simple prouvant que les données sont chargées dans l'index)

6. Arrêtez l'application et supprimez l'index pour faire de la place aux vôtres.

    > [AZURE.TIP]Vous pouvez utiliser [le portail](https://portal.azure.com) pour supprimer l'index. Cliquez sur le nom de l'index pour ouvrir le panneau et utilisez la commande **Supprimer**.

## Remplacer les fichiers JSON de données et de schéma par vos données

Dans la solution de prototype, il existe un fichiers de schéma et trois fichiers de données : data1.json, data2.json et data3.json. Le fichier de schéma (schema.json) doit être remplacé par un schéma qui décrit vos données.

Par défaut, ces fichiers se trouvent dans le dossier de la solution :

![][1]

Si vous pouvez intégrer vos données dans des fichiers JSON, vous pouvez remplacer les fichiers existants par vos données, puis exécuter l'application pour créer et charger un index. La distribution des données entre plusieurs fichiers contribue à démontrer une opération de téléchargement par lots.

D'autres approches pour charger les données incluent l'utilisation d'un indexeur (requiert une source de données Azure DocumentDB ou une source de données de base de données SQL Azure). Vous trouverez un exemple de code qui illustre les autres méthodes de chargement des données dans la [liste des vidéos et didacticiels Azure Search](search-video-demo-tutorial-list.md) sur MSDN.

### Modification de la requête

La requête par défaut intégrée à la solution fait référence à des champs et des structures issus des fichiers de données intégrés. Une fois que vous avez remplacé les fichiers de données pour utiliser vos données, vous pouvez placer le code de requête en commentaire ou le modifier pour utiliser les champs de votre schéma. Pour plus d'informations sur la syntaxe de la requête de recherche, consultez [Recherche dans des documents](https://msdn.microsoft.com/library/azure/dn798927.aspx).

### Génération et exécution de l'application

Appuyez sur **F5** pour exécuter l'application. Comme auparavant, des messages indiquant que l'index est créé, chargé et peut être interrogé doivent s'afficher.

Vous disposez maintenant d'un index opérationnel que vous pouvez utiliser comme base pour un examen approfondi.

À ce stade, vous pouvez passer à [Fiddler](search-fiddler.md) ou [Chrome Postman](search-chrome-postman.md) pour exécuter des requêtes, modifier l'un des exemples d'applications pour utiliser votre service de recherche et votre index, ou ajouter du code personnalisé qui fournit la visualisation des données.

## Ajout d'un profil de score

Nous vous suggérons de tester les [profils de score](search-get-started-scoring-profiles.md) au moyen du portail ou du code. Les profils de score augmentent l'importance d'un terme de recherche trouvé dans un champ spécifique. Par exemple, si le terme de recherche est un nom de ville, vous vous attendez à ce que des documents contenant *Seattle* dans le champ Ville apparaissent plus haut dans les résultats que des documents contenant *Seattle* dans un champ Description.

L'ajout d'un profil de score modifié l'index. Vous devrez régénérer et recharger l'index à chaque fois que vous modifiez le schéma. Par conséquent, pensez à ajouter des segments de code de profil de score dans votre prototype (ou modifiez les exemples de profil de score pour utiliser votre code d'indexation).

Consultez [Ajout d'un profil de score](https://msdn.microsoft.com/library/dn798928.aspx) pour obtenir la documentation de référence sur les profils de score.

## Ajout d'un générateur de suggestions

Le générateur de suggestions fait référence à la fonctionnalité de recherche populaire qui fournit une liste de termes de recherche possibles en fonction des entrées de texte de l'utilisateur (le fait de taper « wea » affiche une liste de termes de recherche à saisie semi-automatique pour « weather », « weather channel », « weather underground », etc.).

Pour ajouter des générateurs de suggestions, ajoutez une section au schéma d'index qui spécifie les champs utilisés pour générer des requêtes prédictives ou avec saisie semi-automatique. Les champs contenant des noms ou des chaînes plus courtes ayant des valeurs non répétitives fonctionnent le mieux. Consultez [Création d'index](https://msdn.microsoft.com/library/dn798928.aspx) pour plus de détails.

## Test d'un analyseur de langage

Les analyseurs de langage fournissent les règles linguistiques pour faire la distinction entre les mots essentiels et non-essentiels, les formes de racine et même les synonymes. Par défaut, Azure Search utilise l'analyseur de langage Lucene pour l'anglais. Vous pouvez spécifier différents analyseurs comme attribut d'index sur des champs spécifiques. Ceci vous permet de générer des schémas et des documents qui contiennent des champs utilisant différents analyseurs (par exemple, une application multilingue peut combiner des champs en français et en anglais côte à côte dans le même document). Consultez [Support multilingue](https://msdn.microsoft.com/library/dn879793.aspx) pour plus de détails.

## Étapes suivantes

Dans les sections précédentes, vous avez modifié l'index pour ajouter des fonctionnalités supplémentaires à votre prototype. À ce stade, les modifications de votre index sont presque terminées (nous n'avons pas abordé l'activation de CORS, le seul changement de schéma restant que vous pourriez apporter).

À présent, tout prototypage supplémentaire est susceptible de vous guider dans deux directions différentes : vers l'extérieur avec un accent sur l'interface utilisateur, en ajoutant éventuellement une navigation à facettes ou d'autres filtres permettant d'affiner la recherche, ou une exploration approfondie des aspects de la synchronisation des données de votre solution. De nombreuses solutions ont des données volatiles. Pour de nombreux développeurs, la synchronisation des mises à jour des données entre les systèmes de base de données transactionnelle et un index Azure Search constitue la priorité suivante une fois que les comportements de recherche de base sont vérifiés.

Suivez ces liens pour en savoir plus :

- [Flux de travail standard pour les projets de développement à l'aide d'Azure Search](search-workflow.md)

- [Personnalisation de l'indexeur Azure Search](search-indexers-customization.md)

- [Navigation à facettes dans Azure Search](search-faceted-navigation.md)

- [Navigation dans les résultats de recherche d'Azure Search](search-pagination-page-layout.md)


<!--Image references-->
[1]: ./media/search-build-prototype/azsearch-datafiles.png
 

<!---HONumber=AcomDC_1203_2015-->