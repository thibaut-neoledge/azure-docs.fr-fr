Configurez un déploiement Git local vers l’application d’API avec la commande [az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git).   

App Service prend en charge plusieurs façons de déployer du contenu vers une application web, telle que FTP, Git local, GitHub, Visual Studio Team Services et Bitbucket. Pour ce démarrage rapide, vous effectuez le déploiement à l’aide de Git local. Cela signifie que vous déployez à l’aide d’une commande Git pour transmettre un référentiel local à un référentiel dans Azure.  

Utilisez le script ci-dessous pour définir les informations d’identification de déploiement au niveau du compte que vous allez utiliser lors de la transmission (push) du code, en veillant à inclure vos propres valeurs pour le nom d’utilisateur et le mot de passe.   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
