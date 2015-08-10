<properties 
	pageTitle="Utilisation des profils de score dans Azure Search" 
	description="Prise en main des profils de score dans Azure Search" 
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
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Utilisation des profils de score dans Azure Search

Les profils de score sont une fonctionnalité de Microsoft Azure Search. Ils personnalisent le calcul des scores de recherche, influençant le classement des éléments dans une liste de résultats de recherche. Vous pouvez considérer les profils de score comme un moyen de modéliser la pertinence, par la mise en avant d'éléments répondant à des critères prédéfinis. Par exemple, supposons que votre application est un site de réservation d'hôtel en ligne. En insistant sur le champ `location`, les recherches qui incluent un terme comme Seattle entraînent des scores élevés pour les éléments dont le champ `location` contient Seattle. Notez que vous pouvez avoir plusieurs profils de score, ou aucun, si le score par défaut est suffisant pour votre application.

Pour vous aider à tester les profils de score, vous pouvez télécharger un exemple d'application qui utilise les profils de score pour modifier le classement des résultats de recherche. L'exemple est une application console (peut-être pas très réaliste pour le développement d'applications du monde réel) utile comme outil d'apprentissage.

L'exemple d'application illustre des comportements de score à l'aide de données fictives, appelées `musicstoreindex`. La simplicité de l'exemple d'application facilite la modification des profils de score et des requêtes. L'utilisateur peut voir les effets immédiats sur le classement quand le programme est exécuté.

<a id="sub-1"></a>
## Conditions préalables

L’exemple d’application est écrit en C# à l’aide de Visual Studio 2013. Essayez l'édition [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) si vous ne disposez pas déjà d'une copie de Visual Studio.

Vous avez besoin d'un abonnement Azure et d'un service Azure Search pour suivre ce didacticiel. Reportez-vous à la rubrique [Création d’un service Search dans le portail](search-create-service-portal.md) pour obtenir de l’aide sur la configuration du service.

[AZURE.INCLUDE [Pour suivre ce didacticiel, vous avez besoin d'un compte Azure :](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Téléchargement de l'exemple d'application

Accédez à la [démonstration de profils de score d'Azure Search](https://azuresearchscoringprofiles.codeplex.com/) sur Codeplex pour télécharger l'exemple d'application décrit dans ce didacticiel.

Dans l'onglet Code source, cliquez sur **Télécharger** pour obtenir un fichier .zip de la solution.

 ![][12]

<a id="sub-3"></a>
## Modification du fichier app.config

1. Après avoir extrait les fichiers, ouvrez la solution dans Visual Studio pour modifier le fichier de configuration.
1. Dans l'Explorateur de solutions, double-cliquez sur **app.config**. Ce fichier spécifie le point de terminaison de service et une `api-key` utilisée pour authentifier la demande. Ces valeurs sont disponibles dans le portail de gestion Azure.
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez au tableau de bord du service pour Azure Search.
1. Cliquez sur la vignette **Propriétés** pour copier l'URL du service.
1. Cliquez sur la vignette **Clés** pour copier l'`api-key`.

Quand vous avez terminé d'ajouter l'URL et `api-key` à app.config, les paramètres de l'application doivent ressembler à ceci :

   ![][11]


<a id="sub-4"></a>
## Exploration de l'application

Vous êtes presque prêt à générer et à exécuter l'application, mais avant cela, examinez les fichiers JSON utilisés pour créer et remplir l'index.

**Schema.json** définit l'index, notamment les profils de score qui sont mis en évidence dans cette démonstration. Notez que le schéma définit tous les champs utilisés dans l'index, y compris les champs non consultables, tels que `margin`, que vous pouvez utiliser dans un profil de score. La syntaxe du profil de score est documentée dans [Ajout d'un profil de score à un index Azure Search](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Data1-3.json** fournit les données, 246 albums sur une poignée de genres. Les données sont une combinaison d'informations sur des albums et des artistes réels, avec des champs fictifs comme `price` et `margin` utilisés pour illustrer les opérations de recherche. Les fichiers de données sont conformes à l'index et sont téléchargés sur votre service Azure Search. Une fois les données téléchargées et indexées, vous pouvez émettre des requêtes.

**Program.cs** effectue les opérations suivantes :

- Ouvre une fenêtre de console.

- Se connecte à Azure Search à l'aide de l'URL du service et d'`api-key`.

- Supprime l’`musicstoreindex` s’il existe.

- Crée un `musicstoreindex` à l'aide du fichier schema.json.

- Remplit l'index en utilisant les fichiers de données.

- Interroge l'index à l'aide de quatre requêtes. Notez que les profils de score sont spécifiés comme un paramètre de requête. Toutes les requêtes recherchent le même terme « best ». La première requête montre le score par défaut. Les trois requêtes restantes utilisent un profil de score.

<a id="sub-5"></a>
## Génération et exécution de l'application

Pour éliminer les problèmes de référence d'assembly ou de connectivité, générez et exécutez l'application pour vous assurer qu'aucun problème ne doit d'abord être résolu. Vous devez voir une application console ouverte en arrière-plan. Les quatre requêtes s'exécutent en séquence sans interruption. Sur de nombreux systèmes, la totalité du programme s'exécute en moins de 15 secondes. Si l'application console contient un message indiquant « Terminé. Appuyez sur ENTRÉE pour continuer », le programme s'est terminé correctement.

Pour comparer les exécutions de la requête, vous pouvez marquer-copier-coller les résultats de la requête à partir de la console et les coller dans un fichier Excel.

L'illustration suivante montre les résultats des trois premières requêtes côte à côte. Toutes les requêtes utilisent le même terme de recherche, « best », qui apparaît dans de nombreux titres d'album.

   ![][10]

La première requête montre le score par défaut. Le terme de recherche s'affichant uniquement dans les titres d'album et aucun critère n'étant spécifié, les éléments contenant « best » dans le titre d'album sont retournés dans l'ordre dans lequel le service de recherche les trouve.

La deuxième requête utilise un profil de score, mais notez que le profil n'a eu aucun effet. Les résultats sont identiques à ceux de la première requête, car le profil de score met en avant un champ (« genre ») qui n'est pas associé à la requête. Le terme « best » n'existe dans aucun champ « genre » d'un document. Quand un profil de score n'a aucun effet, les résultats sont les mêmes que le score par défaut.

La troisième requête est la première preuve de l'impact du profil de score. Le terme recherché est toujours « best », donc nous travaillons avec le même ensemble d'albums, mais comme le profil de score fournit des critères supplémentaires qui renforcent les champs « rating » et « last-updated », certains éléments sont placés plus haut dans la liste.

L'illustration suivante montre la quatrième et dernière requête, renforcée par le champ « margin ». Le champ « margin » est non consultable et ne peut pas être retourné dans les résultats de la recherche. La valeur « margin » a été ajoutée manuellement à la feuille de calcul pour illustrer le fait que les éléments avec des marges supérieures s'affichent plus haut dans la liste des résultats.

   ![][9]

Maintenant que vous avez terminé les exercices de profils de score, essayez de modifier le programme pour utiliser une autre syntaxe de requête, d'autres profils de score ou des données plus complètes. Les liens figurant dans la section suivante fournissent plus d'informations.

<a id="next-steps"></a>
## Étapes suivantes

Découvrez plus en détail les profils de score. Consultez la rubrique [Ajout d'un profil de score à un index Azure Search](http://msdn.microsoft.com/library/azure/dn798928.aspx) pour plus d'informations.

Découvrez plus en détail la syntaxe de recherche et les paramètres de requête. Consultez la rubrique [Recherche de documents (API REST Azure Search)](http://msdn.microsoft.com/library/azure/dn798927.aspx) pour plus d'informations.

Vous avez besoin de revenir en arrière et de vous former davantage à la création d'index ? [Regardez cette vidéo](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) pour comprendre les notions de base.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG

<!---HONumber=July15_HO5-->