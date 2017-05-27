1. Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran. Pour plus d’informations sur la connexion, consultez [Prise en main d’Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

  ```azurecli
  az login
  ```
2. Si vous disposez de plusieurs abonnements Azure, répertoriez les abonnements associés au compte.

  ```azurecli
  az account list --all
  ```
3. Spécifiez l’abonnement que vous souhaitez utiliser.

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```