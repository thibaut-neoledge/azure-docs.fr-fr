Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Vous créez généralement votre groupe de ressources et les ressources dans une région proche de chez vous. Pour visualiser tous les emplacements pris en charge pour Azure Web Apps, exécutez la commande `az appservice list-locations`. 