Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, vous pouvez les supprimer en supprimant le groupe de ressources.
 
1. Exécutez la commande suivante pour vous assurer que le groupe de ressources ne contient aucune des ressources que vous souhaitez enregistrer :

  ```azurecli
  az group show --name myResourceGroup
  ```

2. Si les ressources affichées sont bien celles que vous souhaitez supprimer, exécutez la commande suivante :
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
