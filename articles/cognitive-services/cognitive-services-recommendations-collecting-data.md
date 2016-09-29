<properties
	pageTitle="Collecte de données pour la formation d’un modèle : API Machine Learning Recommendations | Microsoft Azure"
	description="Azure Machine Learning Recommendations - Collecte de données pour la formation d’un modèle"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="luisca"/>

#  Collecte de données pour la formation d’un modèle #

L’API Recommendations utilise vos transactions passées pour identifier les articles qu’il convient de recommander à un utilisateur particulier.

Après avoir créé un modèle, vous devez fournir deux éléments d’information pour pouvoir former votre modèle : un fichier de catalogue et les données d’utilisation.

>   Si ce n’est déjà fait, nous vous encourageons à suivre le [guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).


## Données de catalogue ##

### Format de fichier de catalogue ###

Le fichier de catalogue contient des informations sur les articles que vous proposez à votre client. Les données de catalogue doivent être au format suivant :

- Sans caractéristiques : `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Avec caractéristiques : `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### Exemples de lignes d’un fichier de catalogue

Sans caractéristiques :

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Avec caractéristiques :

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### Détails du format

| Name | Obligatoire | Type | Description |
|:---|:---|:---|:---|
| Item Id |Oui | [A-z], [a-z], [0-9], [\_] &#40;trait de soulignement&#41;, [-] &#40;tiret&#41;<br> Longueur maximale : 50 | Identificateur unique d'un élément. |
| Item Name | Oui | Caractères alphanumériques<br> Longueur maximale : 255 | Nom de l'élément. |
| Item Category | Oui | Caractères alphanumériques <br> Longueur maximale : 255 | Catégorie à laquelle cet élément appartient (par exemple, livres de cuisine, pièces de théâtre, etc.) ; peut être vide. |
| Description | Non, sauf si des caractéristiques sont présentes (mais peut être vide) | Caractères alphanumériques <br> Longueur maximale : 4 000 | Description de cet élément. |
| Features list | Non | Caractères alphanumériques <br> Longueur maximale : 4 000 ; nombre maximal de caractéristiques : 20 | Liste de noms de caractéristiques séparés par des virgules = valeur qui peut être utilisée pour améliorer la recommandation de modèle.|

#### Chargement d’un fichier de catalogue

Consultez la [référence d’API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) pour savoir comment charger un fichier de catalogue.

Notez que le contenu du fichier de catalogue doit être passé en tant que corps de la demande.

Si vous téléchargez plusieurs fichiers de catalogue dans le même modèle avec plusieurs appels, seuls les nouveaux éléments de catalogue sont insérés. Les éléments existants conservent leurs valeurs d'origine. Vous ne pouvez pas mettre à jour des données de catalogue à l'aide de cette méthode.

>   Remarque : la taille de fichier maximale est de 200 Mo. Le nombre d’articles du catalogue pris en charge est limité à 100 000.


## Pourquoi ajouter des caractéristiques au catalogue ?

Le moteur de recommandations crée un modèle statistique qui vous indique les articles que le client est susceptible d’apprécier ou d’acheter. Dans le cas d’un nouveau produit qui n’a jamais été proposé au client, vous ne pouvez pas créer de modèle basé uniquement sur les co-occurrences. Supposons que vous ajoutiez un nouveau « violon pour enfant » dans votre boutique. Puisque vous n’avez jamais vendu ce violon auparavant, vous n’avez aucune idée des autres articles qu’il serait intéressant de recommander avec ce violon.

Ceci étant dit, si le moteur possède des informations sur ce violon (c.-à-d. s’il sait qu’il s’agit d’un instrument de musique, qu’il est destiné à des enfants âgés de 7 à 10 ans, qu’il n’est pas cher, etc.), il peut obtenir des informations auprès d’autres produits présentant des fonctionnalités similaires. Par exemple, vous avez déjà vendu des violon auparavant et vous savez qu’en règle générale les personnes qui achètent des violons ont tendance à acheter des CD de musique classique et des supports de partitions. Le système peut rechercher des liens entre les caractéristiques et fournir des recommandations compte tenu de ces caractéristiques, ce alors même que votre nouveau violon n’a que peu été utilisé.

Les caractéristiques sont importées dans le cadre des données de catalogue, puis leur classement (autrement dit l’importance de la caractéristique dans le modèle) est associé au terme de l’exécution d’une build de classement. Le classement des caractéristiques peut varier selon le modèle des données d'utilisation et le type d'éléments. Toutefois, pour des données d'utilisation ou des éléments cohérents, les fluctuations de classement sont généralement minimes. Le classement des caractéristiques est représenté par une valeur non négative. La valeur 0 signifie que la caractéristique n'a pas été classée (ce qui se produit si vous appelez cette API avant la fin de la première build de classement). On parle d'actualisation du score pour désigner la date à laquelle le classement a été attribué.


###Les caractéristiques sont catégorielles

Cela signifie que vous devez créer des caractéristiques ressemblant à une catégorie. Par exemple, prix = 9,34 n’est pas une caractéristique catégorielle. En revanche, GammeDePrix = MoinsDe5Dollars est une caractéristique catégorielle. Une autre erreur fréquente consiste à utiliser le nom de l’élément en tant que caractéristique. Ce nom serait unique et ne décrirait donc pas une catégorie. Assurez-vous que les caractéristiques représentent des catégories d’éléments.


###Combien de caractéristiques dois-je utiliser, et lesquelles ?


En fin de compte, la build Recommendations prend en charge la création d’un modèle comprenant jusqu’à 20 caractéristiques. Vous pouvez attribuer plus de 20 caractéristiques aux éléments de votre catalogue, mais vous devez effectuer une build de classement et sélectionner uniquement les caractéristiques qui ont un rang élevé. (Une caractéristique avec un classement de 2,0 ou plus est très intéressante à utiliser !).


###Quand les caractéristiques sont-elles réellement utilisées ?

Le modèle utilise les caractéristiques lorsqu’il ne dispose pas de suffisamment de données de transaction pour fournir des recommandations uniquement à partir des informations de transaction. Les caractéristiques ont donc un plus grand impact sur les « éléments à froid » – éléments avec peu de transactions. Si tous vos éléments disposent de suffisamment d’informations de transaction, vous n’aurez peut-être pas besoin d’enrichir votre modèle avec des caractéristiques.


###Utiliser les caractéristiques de produit

Pour utiliser des caractéristiques dans le cadre de votre build, vous devez :

1. veiller à ce que votre catalogue dispose de ces caractéristiques lors de son téléchargement ;

2. déclencher une build de classement. Ceci permettra d’analyser l’importance/le classement des caractéristiques ;

3. déclencher une build de recommandations, en définissant les paramètres de build suivants : définir useFeaturesInModel sur true, définir allowColdItemPlacement sur true et définir modelingFeatureList sur la liste séparée par des virgules des caractéristiques que vous souhaitez utiliser pour améliorer votre modèle. Pour plus d’informations, consultez la page [Recommendations build type parameters](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) (Paramètres de build de type recommandation).





## Données d'utilisation ##
Un fichier d’utilisation contient des informations sur l’utilisation des articles ou sur les transactions réalisées auprès de votre entreprise.

#### Détails du format du fichier d’utilisation
Un fichier d’utilisation est un fichier CSV (valeurs séparées par des virgules) dans lequel chaque ligne représente une interaction entre un utilisateur et un article. Chaque ligne est mise en forme de la manière suivante : <br> `<User Id>,<Item Id>,<Time>,[<Event>]`



| Name | Obligatoire | Type | Description
|-------|------------|------|---------------
|User Id| Oui|[A-z], [a-z], [0-9], [\_] &#40;trait de soulignement&#41;, [-] &#40;tiret&#41;<br> Longueur maximale : 255 |Identificateur unique d’un utilisateur.
|Item Id|Oui|[A-z], [a-z], [0-9], [&#95;] &#40;trait de soulignement&#41;, [-] &#40;tiret&#41;<br> Longueur maximale : 50|Identificateur unique d'un élément.
|Time|Oui|Date au format suivant : AAAA/MM/JJTHH:MM:SS (par exemple, 2013/06/20T10:00:00)|Indication de temps des données.
|Événement|Non | Une des valeurs suivantes :<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase| Le type de transaction. |

#### Exemples de lignes dans un fichier d’utilisation

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### Chargement d’un fichier d’utilisation

Consultez la [référence d’API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) pour savoir comment charger des fichiers d’utilisation. Notez que vous devez transmettre le contenu du fichier d’utilisation dans le corps de l’appel HTTP.

>  Remarque :

>  Taille maximale du fichier : 200 Mo. Vous pouvez télécharger plusieurs fichiers d’utilisation.

>  Vous devez télécharger un fichier de catalogue avant de commencer à ajouter des données d’utilisation à votre modèle. Seuls les articles contenus dans le fichier de catalogue seront utilisés pendant la phase de formation. Tous les autres articles seront ignorés.

## De quelle quantité de données avez-vous besoin ?

La qualité de votre modèle dépend fortement de la qualité et de la quantité de vos données. Le système apprend lorsque les utilisateurs achètent différents éléments (ce que nous appelons les « co-occurrences »). Pour les versions FBT, il est également important de savoir quels éléments sont achetés dans une même transaction.

Une bonne règle empirique est de faire en sorte que la plupart des éléments soient présents dans au minimum 20 transactions. Si vous avez 10 000 éléments dans votre catalogue, nous recommandons donc de disposer d'au moins 20 fois ce nombre de transactions, soit environ 200 000 transactions. Une fois encore, il s'agit d'une règle empirique. Vous devez faire des essais avec vos données.

Une fois que vous avez créé un modèle, vous pouvez effectuer une [évaluation hors connexion](cognitive-services-recommendations-buildtypes.md) pour vérifier le comportement probable de votre modèle.

<!---HONumber=AcomDC_0914_2016-->