## Fichier de paramètres

Si vous utilisez un fichier de paramètres pour passer les valeurs de paramètre à votre modèle au cours du déploiement, vous devez créer un fichier JSON avec un format similaire à l’exemple suivant.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            },
            "adminPassword": {
                "reference": {
                   "keyVault": {
                      "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                   }, 
                   "secretName": "sqlAdminPassword" 
                }   
            }
       }
    }

La taille du fichier de paramètre ne peut pas être supérieure à 64 Ko.

<!---HONumber=AcomDC_0615_2016-->