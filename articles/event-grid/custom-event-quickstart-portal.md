---
title: "Événements personnalisés pour Azure Event Grid avec le portail Azure| Microsoft Docs"
description: "Utilisez Azure Event Grid et PowerShell pour publier une rubrique et pour vous abonner à cet événement."
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 10/11/2017
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 0fe498b7b6dcf59bc5caef8ff5a40053e0498f85
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2017
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Créer et acheminer des événements personnalisés avec le portail Azure et Event Grid

Azure Event Grid est un service de gestion d’événements pour le cloud. Dans cet article, vous utilisez le portail Azure pour créer une rubrique personnalisée, vous abonner à cette rubrique et déclencher l’événement pour afficher le résultat. En règle générale, vous envoyez des événements à un point de terminaison qui répond à l’événement, comme un webhook ou une fonction Azure. Toutefois, pour simplifier cet article, vous envoyez les événements à une URL qui collecte seulement les messages. Vous créez cette URL à l’aide d’un outil tiers en open-source nommé [RequestBin](https://requestb.in/).

>[!NOTE]
>**RequestBin** est un outil open source qui n’est pas destiné à une utilisation à haut débit. L’utilisation de l’outil ici est uniquement à but démonstratif. Si vous envoyez plusieurs événements par push en simultané, vous pouvez ne pas voir tous les événements dans l’outil.

Une fois terminé, vous voyez que les données d’événement ont été envoyées à un point de terminaison.

![Données d’événement](./media/custom-event-quickstart-portal/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les rubriques Event Grid sont des ressources Azure et doivent être placées dans un groupe de ressources Azure. Un groupe de ressources est une collection logique dans laquelle des ressources Azure sont déployées et gérées.

1. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**. Sélectionnez ensuite **Ajouter**.

   ![Créer un groupe de ressources](./media/custom-event-quickstart-portal/create-resource-group.png)

1. Définir le nom du groupe de ressources par *gridResourceGroup* et l’emplacement par *westus2*. Sélectionnez **Créer**.

   ![Fournir des valeurs au groupe de ressources](./media/custom-event-quickstart-portal/provide-resource-group-values.png)

## <a name="create-a-custom-topic"></a>Créer une rubrique personnalisée

Une rubrique fournit un point de terminaison défini par l’utilisateur vers lequel vous envoyez vos événements. 

1. Pour créer une rubrique dans votre groupe de ressources, sélectionnez **More services** et recherchez *Event Grid*. Sélectionnez **Rubriques Event Grid** dans les options disponibles.

   ![Créer une rubrique Event Grid](./media/custom-event-quickstart-portal/create-event-grid-topic.png)

1. Sélectionnez **Ajouter**.

   ![Ajouter une rubrique Event Grid](./media/custom-event-quickstart-portal/add-topic.png)

1. Donnez un nom à la rubrique. Le nom de la rubrique doit être unique, car elle est représentée par une entrée DNS. Dans sa version préliminaire, Event Grid prend en charge les emplacements **westus2** et **westcentralus**. Sélectionnez le groupe de ressources créé précédemment. Sélectionnez **Créer**.

   ![Donner des valeurs à la rubrique Event Grid](./media/custom-event-quickstart-portal/provide-topic-values.png)

1. Une fois la rubrique créée, sélectionnez **Actualiser** pour qu’elle apparaisse.

   ![Consulter la rubrique Event Grid](./media/custom-event-quickstart-portal/see-topic.png)

## <a name="create-a-message-endpoint"></a>Créer un point de terminaison de message

Avant de nous abonner à la rubrique, nous allons créer le point de terminaison pour le message de l’événement. Au lieu d’écrire du code qui réponde à l’événement, nous allons créer un point de terminaison qui collecte les messages, afin que vous puissiez les consulter. RequestBin est un outil tiers en open-source qui vous permet de créer un point de terminaison et d’afficher les requêtes qui lui sont envoyées. Accédez à [RequestBin](https://requestb.in/), puis cliquez sur **Créer un RequestBin**.  Copiez l’URL du fichier bin, dont vous avez besoin pour vous abonner à la rubrique.

## <a name="subscribe-to-a-topic"></a>S’abonner à une rubrique

Vous vous abonnez à une rubrique pour communiquer à Event Grid les événements qui vous intéressent. 

1. Pour créer un abonnement Event Grid, sélectionnez de nouveau **More Services** puis recherchez *Event Grid*. Sélectionnez **Abonnements Event Grid** dans les options disponibles.

   ![Créer un abonnement Event Grid](./media/custom-event-quickstart-portal/create-subscription.png)

1. Sélectionnez **+ Abonnement aux événements**.

   ![Ajouter un abonnement Event Grid](./media/custom-event-quickstart-portal/add-subscription.png)

1. Indiquez un nom unique pour votre abonnement aux événements. Pour le type de rubrique, sélectionnez **Rubriques Event Grid**. Pour l’instance, sélectionnez la rubrique personnalisée que vous avez créée. Indiquez l’URL depuis RequestBin en tant que point de terminaison de la notification d’événement. Lorsque vous avez fourni les valeurs, sélectionnez **Créer**.

   ![Donner une valeur à l’abonnement Event Grid](./media/custom-event-quickstart-portal/provide-subscription-values.png)

Nous allons maintenant déclencher un événement pour voir comment Event Grid distribue le message à votre point de terminaison. Pour simplifier cet article, utilisez Cloud Shell pour envoyer des exemples de données d’événements à la rubrique. En règle générale, une application ou un service Azure envoie les données d’événements.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>Envoyer un événement à votre rubrique

Tout d’abord, il nous faut l’URL et la clé de la rubrique. Utilisez le nom de votre rubrique pour `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

L’exemple suivant obtient les exemples de données d’événements :

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Si vous affichez `echo "$body"`, vous pouvez voir l’événement complet. L’élément `data` du fichier JSON est la charge utile de l’événement. N’importe quel fichier JSON bien construit peut être placé dans ce champ. Vous pouvez aussi utiliser le champ objet pour un routage et un filtrage avancés.

CURL est un utilitaire qui effectue des requêtes HTTP. Dans cet article, nous utilisons CURL pour envoyer l’événement à notre rubrique. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Vous avez déclenché l’événement, et Event Grid a envoyé le message au point de terminaison configuré lors de l’abonnement. Accédez à l’URL de RequestBin que vous avez créée précédemment. Ou cliquez sur Actualiser dans le navigateur RequestBin ouvert. L’événement que vous venez d’envoyer apparaît.

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer à utiliser cet événement, ne supprimez pas les ressources créées dans cet article. Sinon, supprimez les ressources créées avec cet article.

Sélectionnez le groupe de ressources, puis **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez créer des rubriques et des abonnements d’événements, vous pouvez en apprendre davantage sur Event Grid et ce qu’il peut vous offrir :

- [Event Grid](overview.md)
- [Acheminer des événements de stockage Blob Azure vers un point de terminaison Web personnalisé ](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Diffuser en continu des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md)
