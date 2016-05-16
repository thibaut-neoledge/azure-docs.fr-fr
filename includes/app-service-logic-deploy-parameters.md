Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée Paramètres, qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Lorsque vous définissez les paramètres, utilisez le champ **allowedValues** pour spécifier les valeurs qu'un utilisateur peut fournir au cours du déploiement. Utilisez le champ **defaultValue** pour affecter une valeur au paramètre, si aucune valeur n'est fournie pendant le déploiement.

Nous allons décrire chaque paramètre du modèle.

### logicAppName

Le nom de l'application logique à créer.

    "logicAppName": {
        "type": "string"
    }

### hostingPlanName

Le nom du plan App Service à créer pour l'hébergement de l'application logique.
    
    "hostingPlanName": {
      "type": "string",
      "metadata": {
        "description": "The name of the App Service plan to create for hosting the logic app."
      }
    }

### flowSkuName

Le niveau de tarification de l'application logique.

    "flowSkuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Free",
        "Basic",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "The pricing tier for the logic app."
      }
    },


Le modèle définit les valeurs autorisées pour ce paramètre (Gratuit, De base, Standard ou Premium) et affecte une valeur par défaut (Standard) si aucune valeur n'est spécifiée.

### hostingSkuName

Le niveau de tarification du plan App Service.

    "hostingSkuName": {
      "type": "string",
      "defaultValue": "S1",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "metadata": {
        "description": "Describes plan's pricing tier and instance size."
      }
    },


### hostingSkuCapacity

La taille d'instance de l'application.

    "hostingSkuCapacity": {
      "type": "int",
      "defaultValue": 1,
      "minValue": 1,
      "metadata": {
        "description": "Describes plan's instance count"
      }
    },


Le modèle définit les valeurs autorisées pour ce paramètre (0, 1 ou 2) et affecte une valeur par défaut (0) si aucune valeur n'est spécifiée. Les valeurs correspondent à une taille petite, moyenne et grande.

