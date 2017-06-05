Utilisez la commande [az appservice web create](/cli/azure/appservice/web#create) pour créer l’application web. Dans la commande suivante, indiquez un nom d’application unique dans l’espace réservé `<app_name>`. `<app_name>` est utilisé dans le site DNS par défaut de l’application web. Si `<app_name>` n’est pas une valeur unique, le message d’erreur « Un site Web nommé <app_name> existe déjà. » s’affiche.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Une fois l’application web créée, Azure CLI affiche des informations similaires à celles de l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

Accédez au site (`http://<app_name>.azurewebsites.net`) pour voir votre nouvelle application web.

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>Configurer le déploiement Git local

App Service prend en charge plusieurs façons de déployer du contenu vers une application web, telle que FTP, Git local, GitHub, Visual Studio Team Services et Bitbucket. 

Pour ce démarrage rapide, vous effectuez le déploiement à l’aide de Git local. Cela signifie que vous déployez à l’aide d’une commande Git pour transmettre un référentiel local à un référentiel dans Azure. 

Utilisez la commande [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) pour configurer l’accès Git local à l’application web.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

La sortie a le format suivant :

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

Enregistrez l’URI indiqué. Vous l’utiliserez à l’étape suivante. Le `<username>` représente [l’utilisateur du déploiement](#configure-a-deployment-user) que vous avez créé lors d’une précédente étape.

## <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

Ajoutez un référentiel distant Azure dans votre référentiel Git local.

```bash
git remote add azure <URI from previous step>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application. Le mot de passe que vous avez utilisé précédemment dans le cadre de la création de l’utilisateur du déploiement vous sera demandé. Veillez à saisir le mot de passe que vous avez indiqué dans [Configurer un utilisateur de déploiement](#configure-a-deployment-user), et non pas celui que vous utilisez pour vous connecter au portail Azure.

```azurecli
git push azure master
```

La commande précédente affiche des informations semblables à ce qui suit :

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>Accéder à l’application


Accédez à l’application déployée :

```
http://<app_name>.azurewebsites.net
```

