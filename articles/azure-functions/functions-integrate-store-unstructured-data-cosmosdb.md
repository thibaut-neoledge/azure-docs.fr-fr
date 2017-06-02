---
title: "Stocker des données non structurées à l’aide d’Azure Functions et de Cosmos DB"
description: "Stocker des données non structurées à l’aide d’Azure Functions et de Cosmos DB"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, Cosmos DB, calcul dynamique, architecture sans serveur"
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: ms-hero
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/08/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 46022530c69b292878a4500c0e325bda878e6188
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017

---
# <a name="store-unstructured-data-using-azure-functions-and-cosmos-db"></a>Stocker des données non structurées à l’aide d’Azure Functions et de Cosmos DB

Azure Cosmos DB est un excellent moyen de stocker des données JSON et non structurées. Combiné à Azure Functions, Cosmos DB rend le stockage de données simple et rapide avec beaucoup moins de code que pour stocker des données dans une base de données relationnelle.

Ce didacticiel vous guide dans l’utilisation du portail Azure pour créer une fonction d’Azure qui stocke les données non structurées dans un document Cosmos DB. 

## <a name="prerequisites"></a>Composants requis

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-a-function"></a>Créer une fonction

Créez un nouveau WebHook générique C# nommé `MyTaskList`.

1. Développez votre liste de fonctions existantes, puis cliquez sur le signe + pour créer une nouvelle fonction
1. Sélectionnez GenericWebHook-CSharp et nommez-le `MyTaskList`

![Ajouter une nouvelle Function App WebHook générique C#](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-new-functionapp.png)

## <a name="add-an-output-binding"></a>Ajouter une liaison de sortie

Une fonction Azure peut avoir un déclencheur et un nombre quelconque de liaisons d’entrée ou de sortie. Pour cet exemple, nous allons utiliser un déclencheur de requête HTTP et le document Cosmos DB comme liaison de sortie.

1. Cliquez sur l’onglet *Integrate* de la fonction pour afficher ou modifier le déclencheur de la fonction et les liaisons.
1. Choisissez le lien *Nouvelle sortie* situé dans le coin supérieur droit de la page.

Remarque : Le déclencheur de requête HTTP est déjà configuré. Toutefois, vous devez ajouter la liaison de document Cosmos DB.

![Ajouter une nouvelle liaison de sortie Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

1. Entrez les informations requises pour créer la liaison. Utilisez le tableau ci-dessous pour déterminer les valeurs.

![Configurer la liaison de sortie Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

|  Champ | Valeur  |
|---|---|
| Nom du paramètre de document | Nom qui fait référence à l’objet Cosmos DB dans le code |
| Nom de la base de données | Nom de la base de données pour l’enregistrement des documents |
| Nom de la collection | Nom du regroupement des bases de données Cosmos DB |
| Souhaitez-vous que le document Cosmos DB et la collection soient créés pour vous ? | Oui ou Non |
| Connexion au compte Cosmos DB | Chaîne de connexion pointant vers Cosmos DB |

Vous devez également configurer la connexion à la base de données Cosmos DB.

1. Cliquez sur le lien « Nouveau » en regard du libellé « Connexion au compte Cosmos DB ».
1. Renseignez les champs et sélectionnez les options appropriées nécessaires pour créer le document Cosmos DB.

![Configurer la connexion au document Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

|  Champ | Valeur  |
|---|---|
| ID | ID unique de la base de données Cosmos  |
| API NoSQL | Cosmos DB ou MongoDB  |
| Abonnement | Abonnement MSDN  |
| Groupe de ressources  | Créez un nouveau groupe ou sélectionnez-en un.  |
| Emplacement  | WestEurope  |

1. Cliquez sur le bouton *OK* . Vous devrez peut-être patienter quelques minutes pendant qu’Azure crée les ressources.
1. Cliquez sur le bouton *Enregistrer* .

## <a name="update-the-function-code"></a>Mettre à jour le code de fonction

Remplacez le code du modèle de fonction par le suivant :

Notez que le code de cet exemple est en C# uniquement.

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```

Cet exemple de code lit les chaînes de requête HTTP et les affecte en tant que membres d’un objet `taskDocument`. L’objet `taskDocument` enregistre automatiquement les données dans la base de données Cosmos DB et crée même la base de données à la première utilisation.

## <a name="test-the-function-and-database"></a>Tester la fonction et la base de données

1. Dans l’onglet Function, cliquez sur le lien *Test* sur la droite du portail et entrez les chaînes de requête HTTP suivantes :

| Chaîne de requête | Valeur |
|---|---|
| name | Chris P. Bacon |
| task | Créer un sandwich BLT |
| duedate | 05/12/2017 |

1. Cliquez sur le lien *Exécuter*.
1. Vérifiez que la fonction a renvoyé un code de réponse *HTTP 200 OK*.

![Configurer la liaison de sortie Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

Vérifiez qu’une entrée a été effectuée dans la base de données Cosmos DB.

1. Recherchez votre base de données dans le portail Azure et sélectionnez-la.
1. Sélectionnez l’option *Explorateur de données*.
1. Développez les nœuds jusqu'à ce que vous atteigniez les entrées du document.
1. Vérifiez l’entrée de la base de données. Il y aura des métadonnées supplémentaires dans la base de données en même temps que vos données.

![Vérifiez l’entrée Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

Si les données figurent dans le document, alors vous avez bien créé une fonction Azure qui stocke les données non structurées dans une base de données Cosmos DB.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Functions, consultez les rubriques suivantes :

[!INCLUDE [Getting help note](../../includes/functions-get-help.md)]

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]
