## <a name="prerequisites"></a>Composants requis

Si ce n’est déjà fait, inscrivez-vous à un [essai gratuit de l’abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) et installez [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="create-the-scale-set"></a>Créer le groupe identique

Tout d’abord, créez un groupe de ressources dans lequel vous déploierez le groupe identique :

```azurecli
az group create --location westus --name myResourceGroup
```

À présent, créez maintenant votre groupe de machines virtuelles identiques à l’aide de la commande `az vmss create`. L’exemple suivant crée un groupe identique Linux nommé `myvmss` dans le groupe de ressources nommé `myrg` :

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

L’exemple suivant crée un groupe identique Windows avec la même configuration :

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Si vous souhaitez choisir une autre image de système d’exploitation, vous pouvez afficher les images disponibles avec la commande `az vm image list` ou `az vm image list --all`. Pour afficher les informations de connexion des machines virtuelles dans le groupe identique, utilisez la commande `az vmss list_instance_connection_info`:

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```