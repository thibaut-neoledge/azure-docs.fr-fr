Utilisez l’interface de ligne de commande Azure afin d’obtenir l’URL de déploiement à distance pour votre application API, puis configurez votre déploiement Git local pour être en mesure d’effectuer une transmission de type push vers le référentiel distant.

```bash
giturl=$(az webapp deployment source config-local-git -n $app_name \ -g myResourceGroup --query [url] -o tsv)

git remote add azure $giturl
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application. Le mot de passe que vous avez utilisé précédemment dans le cadre de la création de l’utilisateur du déploiement vous sera demandé. Veillez à entrer le mot de passe que vous avez créé précédemment dans le cadre du démarrage rapide, et non celui que vous utilisez pour vous connecter au Portail Azure.

```bash
git push azure master
```
