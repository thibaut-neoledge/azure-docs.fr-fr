Configurez un déploiement Git local vers l’application web avec la commande [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git).

App Service prend en charge plusieurs façons de déployer du contenu vers une application web, telle que FTP, Git local, GitHub, Visual Studio Team Services et Bitbucket. Pour ce démarrage rapide, vous effectuez le déploiement à l’aide de Git local. Cela signifie que vous déployez à l’aide d’une commande Git pour transmettre un référentiel local à un référentiel dans Azure. 

Dans la commande suivante, remplacez *\<app_name>* par le nom de votre application web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

La sortie a le format suivant :

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

Le `<username>` représente [l’utilisateur du déploiement](#configure-a-deployment-user) que vous avez créé lors d’une précédente étape.

Copiez l’URI affichée, vous allez l’utiliser à l’étape suivante.
