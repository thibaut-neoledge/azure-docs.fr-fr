
### redisCacheName

Nom du Cache Redis Azure à créer.

    "redisCacheName": {
      "type": "string"
    }

### redisCacheSKU

Niveau de tarification du nouveau Cache Redis Azure.

    "redisCacheSKU": {
      "type": "string",
      "allowedValues": [ "Basic", "Standard" ],
      "defaultValue": "Standard"
    }

Le modèle définit les valeurs autorisées pour ce paramètre (De base ou Standard) et affecte une valeur par défaut (Standard) si aucune valeur n’est spécifiée. Le niveau De base fournit un nœud unique disponible en plusieurs tailles, jusqu’à 53 Go. Le niveau Standard fournit deux nœuds primaire/réplica disponibles en plusieurs tailles jusqu’à 53 Go et un contrat SLA de 99,9 %.

### redisCacheFamily

Famille de la référence (SKU).

    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C"
    }

### redisCacheCapacity

Taille de la nouvelle instance de Cache Redis Azure.

    "redisCacheCapacity": {
      "type": "int",
      "allowedValues": [ 0, 1, 2, 3, 4, 5, 6 ],
      "defaultValue": 1
    }

Le modèle définit les valeurs autorisées pour ce paramètre (0, 1, 2, 3, 4, 5 ou 6) et affecte une valeur par défaut (1) si aucune valeur n’est spécifiée. Ces chiffres correspondent aux tailles de cache suivantes : 0 = 250 Mo, 1 = 1 Go, 2 = 2,5 Go, 3 = 6 Go, 4 = 13 Go, 5 = 26 Go, 6 = 53 Go

### redisCacheVersion

Version du serveur Redis du nouveau cache.

    "redisCacheVersion": {
      "type": "string",
      "defaultValue": "2.8"
    }

<!---HONumber=Oct15_HO3-->