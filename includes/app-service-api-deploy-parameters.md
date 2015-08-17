Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée Paramètres, qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Nous allons décrire chaque paramètre du modèle.

### gatewayName

Le nom de la passerelle. L'application API est enregistrée pour cette passerelle.

    "gatewayName": {
      "type": "string"
    }

### apiAppName

Nom de l'application API à créer. Le nom doit contenir entre 8 et 50 caractères.
    
    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

Secret de l'application API. Cette valeur doit être une chaîne codée en base 64. Elle doit correspondre à une chaîne aléatoire de 64 caractères et comprendre uniquement des entiers et des caractères minuscules.

    "apiAppSecret": {
      "type": "securestring"
    }

### location

Emplacement de la nouvelle application API. Vous pouvez obtenir les emplacements valides en exécutant la commande PowerShell `Get-AzureLocation` ou la commande `azure location list` de la CLI Azure.

    "location": {
      "type": "string"
    }

<!---HONumber=August15_HO6-->