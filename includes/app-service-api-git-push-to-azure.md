L’interface CLI Azure permet d’obtenir l’URL de déploiement à distance pour votre application API. Dans la commande suivante, remplacez *\<app_name>* par le nom de votre application web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Configurez votre déploiement Git local pour effectuer des transmissions de type push vers le serveur distant.

```bash
git remote add azure <URI from previous step>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application. Le mot de passe que vous avez utilisé précédemment dans le cadre de la création de l’utilisateur du déploiement vous sera demandé. Veillez à entrer le mot de passe que vous avez créé précédemment dans le cadre du démarrage rapide, et non celui que vous utilisez pour vous connecter au Portail Azure.

```bash
git push azure master
```
