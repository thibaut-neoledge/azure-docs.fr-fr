Pour ajouter une balise à un groupe de ressources, utilisez **azure group set**. Si le groupe de ressources ne comporte aucune balise, transmettez la balise.

```azurecli
azure group set -n tag-demo-group -t Dept=Finance
```

Les balises sont mises à jour en tant qu'ensemble. Si vous souhaitez ajouter une balise à un groupe de ressources comportant déjà des balises, transmettez toutes les balises. 

```azurecli
azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
```

Les balises ne sont pas héritées par les ressources d’un groupe de ressources. Pour ajouter une balise à une ressource, utilisez **azure resource set**. Transmettez le numéro de version d’API associé au type de ressource auquel vous ajoutez la balise. Si vous avez besoin de récupérer la version d’API, utilisez la commande ci-après avec le fournisseur de ressources pour le type que vous définissez :

```azurecli
azure provider show -n Microsoft.Storage --json
```

Dans les résultats, recherchez le type de ressource souhaité.

```azurecli
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
```

Ensuite, indiquez cette version d’API, le nom du groupe de ressources, le nom de la ressource, le type de la ressource et la valeur de balise sous forme de paramètres.

```azurecli
azure resource set -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -t Dept=Finance -o 2016-01-01
```

Les balises se trouvent directement dans les ressources et les groupes de ressources. Pour afficher les balises existantes, récupérez un groupe de ressources ainsi que ses ressources avec **azure group show**.

```azurecli
azure group show -n tag-demo-group --json
```

Ce qui renvoie des métadonnées sur le groupe de ressources, y compris toutes les balises appliquées.

```azurecli
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
}
```

Vous affichez les balises d’une ressource spécifique avec **azure resource show**.

```azurecli
azure resource show -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -o 2016-01-01 --json
```

Pour récupérer toutes les ressources avec une valeur de balise, utilisez la commande suivante :

```azurecli
azure resource list -t Dept=Finance --json
```

Pour récupérer tous les groupes de ressources avec une valeur de balise, utilisez la commande suivante :

```azurecli
azure group list -t Dept=Finance
```

Vous pouvez visualiser les balises existantes dans votre abonnement avec la commande suivante :

```azurecli
azure tag list
```


<!--HONumber=Jan17_HO2-->


