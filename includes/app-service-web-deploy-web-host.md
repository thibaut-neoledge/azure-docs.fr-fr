### Plan App Service

Crée le plan de service pour l'hébergement de l'application web. Vous fournissez le nom du plan à l'aide du paramètre **hostingPlanName**. L’emplacement du plan est identique à celui utilisé pour le groupe de ressources. Le niveau de tarification et la taille du travail sont spécifiés dans les paramètres **sku** et **workerSize**.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

