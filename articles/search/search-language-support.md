<properties
   pageTitle="Création d'un index de documents dans plusieurs langues dans Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
   description="Azure Search prend en charge 56 langages, tirant parti des analyseurs de langue de la technologie Lucene et Natural Language Processing de Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="10/19/2015"
   ms.author="jlembicz"/>

# Création d'un index de documents dans plusieurs langues dans Azure Search
> [AZURE.SELECTOR]
- [Portal](search-language-support.md)
- [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Décupler les performances des analyseurs de langue est aussi facile que de définir une propriété sur un champ de recherche dans la définition d'index. Maintenant, vous pouvez effectuer cette étape dans le portail.

Voici les captures d'écran des panneaux du portail Azure pour Azure Search qui permettent aux utilisateurs de définir un schéma d'index. À partir de ce panneau, les utilisateurs peuvent créer tous les champs et définir la propriété de l'analyseur pour chacun d'eux.

> [AZURE.NOTE]Vous pouvez uniquement définir un analyseur de langage lors de la définition du champ, comme lors de la création d'un nouvel index ou lorsque vous ajoutez un nouveau champ à un index existant. Veillez à spécifier entièrement tous les attributs, y compris l'analyseur, lors de la création du champ. Vous ne pourrez pas modifier les attributs ou modifier le type d'analyseur une fois le champ défini.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez le panneau de votre service de recherche.
2. Cliquez sur **Ajouter un index** en haut du tableau de bord de service pour démarrer un nouvel index ou ouvrez un index existant pour définir un analyseur sur des nouveaux champs que vous ajoutez à un index existant.
3. Le panneau Champs s'affiche. Il vous propose des options pour définir le schéma de l'index, y compris l'onglet Analyseur utilisé pour le choix d'un analyseur de langage.
4. Dans le panneau Champs, démarrez une définition de champ en fournissant un nom, le choix du type de données et la définition des attributs pour marquer le champ en tant que texte intégral consultable, récupérables dans les résultats de recherche, utilisable dans des structures de navigation de facette, pouvant être trié, et ainsi de suite. 
5. Avant de passer au champ suivant, ouvrez l'onglet **Analyseur**. 
6. Faites défiler pour rechercher le champ que vous définissez. 
7. Si vous n'avez pas activé le champ de recherche, cliquez sur la case à cocher pour indiquer qu'il peut faire l'objet d'une recherche.
8. Cliquez sur la zone Analyseur pour afficher la liste des analyseurs disponibles.
9. Cliquez sur l'analyseur à utiliser.

![][1] *Pour sélectionner un analyseur, cliquez sur l'onglet Analyseur du panneau Champs*

![][2] *Sélectionnez un des analyseurs pris en charge pour chaque champ*

Par défaut, tous les champs pouvant faire l'objet d'une recherche utilisent l'[analyseur Lucene Standard](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) qui est indépendant du langage. Pour afficher la liste complète des analyseurs pris en charge, consultez [Prise en charge linguistique dans Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Une fois que l'analyseur de langage est sélectionné pour un champ, il sera utilisé à chaque demande de recherche et d'indexation pour ce champ. Lorsqu'une requête est émise sur plusieurs champs à l'aide de différents analyseurs, elle sera traitée indépendamment par les analyseurs corrects pour chaque champ.

Plusieurs applications web et mobiles servent les utilisateurs dans le monde entier à l'aide de différentes langues. Il est possible de définir un index pour un scénario comme celui-ci en créant un champ pour chaque langue prise en charge.

![][3] *Définition d'index avec un champ de description pour chaque langue prise en charge*

Si la langue de l'agent d'émission d'une requête est connue, une demande de recherche peut être étendue à un champ spécifique à l'aide du paramètre de requête **searchFields**. La requête suivante sera émise uniquement sur la description en polonais :

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

Parfois, la langue de l'agent d'émission d'une requête n'est pas connue, auquel cas la requête peut être exécutée simultanément sur tous les champs. Si nécessaire, la préférence de résultats dans une langue donnée peut être définie à l'aide des [profils de score](https://msdn.microsoft.com/library/azure/dn798928.aspx). Dans l'exemple ci-dessous, les correspondances trouvées dans la description en anglais auront un score supérieur par rapport aux correspondances en polonais et en français :

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Si vous êtes un développeur .NET, notez que vous pouvez configurer les analyseurs de langage à l'aide du [Kit de développement logiciel (SDK) Azure Search .NET](http://www.nuget.org/packages/Microsoft.Azure.Search/0.13.0-preview). La dernière version prend également en charge les analyseurs de langage Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png

<!---HONumber=Nov15_HO3-->