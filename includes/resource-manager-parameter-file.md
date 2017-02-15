Si vous utilisez un fichier de paramètres pour transmettre des valeurs lors du déploiement, vous devez créer un fichier JSON avec un format similaire à l’exemple suivant :

```json
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
```

La taille du fichier de paramètre ne peut pas être supérieure à 64 Ko.

Pour fournir une valeur sensible pour un paramètre (par exemple, un mot de passe), ajoutez cette valeur à un coffre de clés. Récupérez le coffre de clés pendant le déploiement comme indiqué dans l’exemple précédent. Pour plus d’informations, consultez [Passage de valeurs sécurisés pendant le déploiement](../articles/resource-manager-keyvault-parameter.md). 



<!--HONumber=Nov16_HO3-->


