### Plan App Service

Crée le plan de service pour l'hébergement de l'application web. Vous fournissez le nom du plan à l'aide du paramètre **hostingPlanName**. L'emplacement du plan est identique à celui utilisé pour l'application web. Le niveau de tarification et la taille du travail sont spécifiés dans les paramètres **sku** et **workerSize**.

    {
       "apiVersion":"2015-04-01",
       "name":"[parameters('hostingPlanName')]",
       "type":"Microsoft.Web/serverfarms",
       "location":"[parameters('siteLocation')]",
       "properties":{
         "name":"[parameters('hostingPlanName')]",
         "sku":"[parameters('sku')]",
         "workerSize":"[parameters('workerSize')]",
         "numberOfWorkers":1
       }
    }

<!---HONumber=Oct15_HO3-->