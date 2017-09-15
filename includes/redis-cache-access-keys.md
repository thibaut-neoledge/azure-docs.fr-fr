Pour vous connecter à une instance Cache Redis Azure, vos clients de cache ont besoin du nom d’hôte, des ports et des clés du cache. Certains clients peuvent référencer ces éléments par des noms légèrement différents. Vous pouvez récupérer ces informations dans le portail Azure ou à l’aide des outils de ligne de commande tels qu’Azure CLI.

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Récupération du nom d’hôte, des ports et des clés d’accès à l’aide du portail Azure
Pour récupérer le nom d’hôte, les ports et les clés d’accès à l’aide du portail Azure, [accédez](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings) à votre cache dans le [portail Azure](https://portal.azure.com) et cliquez sur **Clés d’accès** et **Propriétés** dans le **menu Ressource**. 

![Paramètres du cache Redis](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Récupération du nom d’hôte, des ports et des clés d’accès à l’aide de l’interface de ligne de commande Azure
Pour récupérer le nom d’hôte et les ports à l’aide d’Azure CLI 2.0, vous pouvez appeler [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show), et pour récupérer les clés, vous pouvez appeler [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). Le script suivant appelle ces deux commandes et renvoie le nom d’hôte, les ports et les clés à la console.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Pour plus d’informations sur ce script, consultez [Obtenir le nom d’hôte, les ports et les clés pour Cache Redis Azure](../articles/redis-cache/scripts/cache-keys-ports.md). Pour plus d’informations sur Azure CLI 2.0, consultez [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) et [Prise en main d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
