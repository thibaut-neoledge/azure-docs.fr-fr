## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’instance Cache Redis Azure et toutes les ressources associées dans le groupe de ressources.

```azurecli
az group delete --name contosoGroup
```