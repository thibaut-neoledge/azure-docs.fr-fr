Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée Paramètres, qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Lorsque vous définissez les paramètres, utilisez le champ **allowedValues** pour spécifier les valeurs qu'un utilisateur peut fournir au cours du déploiement. Utilisez le champ **defaultValue** pour affecter une valeur au paramètre, si aucune valeur n'est fournie pendant le déploiement.

Nous allons décrire chaque paramètre du modèle.

### logicAppName

Le nom de l'application logique à créer.

    "logicAppName": {
        "type": "string"
    }

### svcPlanName

Le nom du plan App Service à créer pour l'hébergement de l'application logique.
    
    "svcPlanName": {
        "type": "string"
    }

### sku

Le niveau de tarification de l'application logique.

    "sku": {
        "type": "string",
        "defaultValue": "Standard",
        "allowedValues": [
            "Free",
            "Basic",
            "Standard",
            "Premium"
        ]
    }

Le modèle définit les valeurs autorisées pour ce paramètre (Gratuit, De base, Standard ou Premium) et affecte une valeur par défaut (Standard) si aucune valeur n'est spécifiée.

### svcPlanSize

La taille d'instance de l'application.

    "svcPlanSize": {
        "defaultValue": "0",
        "type": "string",
        "allowedValues": [
            "0",
            "1",
            "2"
        ]
    }

Le modèle définit les valeurs autorisées pour ce paramètre (0, 1 ou 2) et affecte une valeur par défaut (0) si aucune valeur n'est spécifiée. Les valeurs correspondent à une taille petite, moyenne et grande.

<!---HONumber=Oct15_HO2-->