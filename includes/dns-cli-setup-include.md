## <a name="set-up-azure-cli-for-azure-dns"></a>Configuration de l’interface de ligne de commande Azure pour Azure DNS

### <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Installez la dernière version de l’interface de ligne de commande Azure, disponible pour Windows, Linux ou MAC. Pour plus d’informations, consultez la page [Installation de l’interface de ligne de commande Azure](../articles/cli-install-nodejs.md).

### <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure

Ouvrez une fenêtre de console et procédez à l’authentification à l’aide de vos informations d’identification. Pour plus d’informations, consultez [Se connecter à Azure avec la CLI Azure](../articles/xplat-cli-connect.md).

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Passage en mode CLI

Azure DNS utilise Azure Resource Manager. Veillez à utiliser le mode d’interface de ligne de commande pour exécuter les commandes Azure Resource Manager.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Sélection de l’abonnement

Vérifiez les abonnements associés au compte.

```azurecli
azure account list
```

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.

Ignorez cette étape si vous utilisez un groupe de ressources existant.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>S’inscrire auprès du fournisseur de ressources

Le service Azure DNS est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être enregistré auprès de ce fournisseur de ressources pour que vous puissiez utiliser Azure DNS. Cette opération n’est à effectuer qu’une fois par abonnement.

```azurecli
azure provider register --namespace Microsoft.Network
```

