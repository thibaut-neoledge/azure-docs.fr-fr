| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Ressources par [groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (par type de ressource) |800 |Varie selon le type de ressource |
| Déploiements par groupe de ressources dans l’historique des déploiements |800 |800 |
| Ressources par déploiement |800 |800 |
| Verrous de gestion (par étendue unique) |20 |20 |
| Nombre de balises (par ressource ou groupe de ressources) |15 |15 |
| Longueur de clé de la balise |512 |512 |
| Longueur de valeur de la balise |256 |256 |


#### <a name="template-limits"></a>Limites de modèle

| Valeur | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Paramètres |256 |256 |
| Variables |256 |256 |
| Ressources (incluant le nombre de copies) |800 |800 |
| Outputs |64 |64 |
| Expression de modèle |24 576 caractères |24 576 caractères |
| Ressources dans les modèles exportés |200 |200 | 
| Taille du modèle |1 Mo |1 Mo |
| Taille du fichier de paramètres |64 Ko |64 Ko |

Vous pouvez dépasser certaines limites de modèle en utilisant un modèle imbriqué. Pour plus d’informations, consultez l’article [Utilisation de modèles liés lors du déploiement des ressources Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Pour réduire le nombre de paramètres, de variables ou de sorties, vous pouvez combiner plusieurs valeurs dans un même objet. Pour plus d’informations, consultez l’article [Objects as parameters](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md) (Utiliser un objet en tant que paramètre).

Si vous atteignez la limite des 800 déploiements par groupe de ressources, supprimez les déploiements inutiles dans l’historique. Vous pouvez supprimer des entrées de l’historique avec la commande [az group deployment delete](/cli/azure/group/deployment#az_group_deployment_delete) dans Azure CLI ou la commande [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) dans PowerShell. La suppression d’une entrée à partir de l’historique des déploiements n’affecte pas les ressources de déploiement. 