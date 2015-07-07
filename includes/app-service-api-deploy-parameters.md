Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée Paramètres, qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Nous allons décrire chaque paramètre du modèle.

### gatewayName

Nom de la passerelle que vous souhaitez créer. L’application API est enregistrée pour cette passerelle.

    "gatewayName": {
      "type": "string"
    }

### apiAppName

Nom de l’application API à créer.
    
    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

Secret de l’application API. Cette valeur doit être une chaîne codée en base 64.

    "apiAppSecret": {
      "type": "securestring"
    }

### location

Emplacement de la nouvelle application API.

    "location": {
      "type": "string"
    }

<!---HONumber=62-->