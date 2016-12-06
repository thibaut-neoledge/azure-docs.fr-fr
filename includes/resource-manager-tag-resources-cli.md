Pour ajouter une balise à un groupe de ressources, utilisez **azure group set**. Si le groupe de ressources ne comporte aucune balise, transmettez la balise.

    azure group set -n tag-demo-group -t Dept=Finance

Les balises sont mises à jour en tant qu'ensemble. Si vous souhaitez ajouter une balise à un groupe de ressources comportant déjà des balises, transmettez toutes les balises. 

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade

Les balises ne sont pas héritées par les ressources d’un groupe de ressources. Pour ajouter une balise à une ressource, utilisez **azure resource set**. Vous devez transmettre le numéro de version d’API associé au type de ressource auquel vous ajoutez la balise. Si vous avez besoin de récupérer la version d’API, utilisez la commande ci-après avec le fournisseur de ressources pour le type que vous définissez :

    azure provider show -n Microsoft.Storage --json

Dans les résultats, recherchez le type de ressource souhaité.

    "resourceTypes": [
    {
      "resourceType": "storageAccounts",
      ...
      "apiVersions": [
        "2016-01-01",
        "2015-06-15",
        "2015-05-01-preview"
      ]
    }
    ...

Ensuite, indiquez cette version d’API, le nom du groupe de ressources, le nom de la ressource, le type de la ressource et la valeur de balise sous forme de paramètres.

    azure resource set -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -t Dept=Finance -o 2016-01-01

Les balises se trouvent directement dans les ressources et les groupes de ressources. Pour afficher les balises existantes, il suffit de récupérer un groupe de ressources et ses ressources avec **azure group show**.

    azure group show -n tag-demo-group --json

Ce qui renvoie des métadonnées sur le groupe de ressources, y compris toutes les balises appliquées.

    {
      "id": "/subscriptions/4705409c-9372-42f0-914c-64a504530837/resourceGroups/tag-demo-group",
      "name": "tag-demo-group",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "location": "southcentralus",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production",
        "Project": "Upgrade"
      },
      ...

Vous affichez les balises d’une ressource spécifique avec **azure resource show**.

    azure resource show -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -o 2016-01-01 --json

Pour récupérer toutes les ressources avec une valeur de balise, utilisez la commande suivante :

    azure resource list -t Dept=Finance --json

Pour récupérer tous les groupes de ressources avec une valeur de balise, utilisez la commande suivante :

    azure group list -t Dept=Finance

Vous pouvez visualiser les balises existantes dans votre abonnement avec la commande suivante :

    azure tag list



<!--HONumber=Nov16_HO3-->


