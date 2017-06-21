## <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

Ajoutez un référentiel distant Azure dans votre référentiel Git local.

```bash
git remote add azure <URI from previous step>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application. Le mot de passe que vous avez utilisé précédemment dans le cadre de la création de l’utilisateur du déploiement vous sera demandé. Veillez à saisir le mot de passe que vous avez indiqué dans [Configurer un utilisateur de déploiement](#configure-a-deployment-user), et non pas celui que vous utilisez pour vous connecter au portail Azure.

```bash
git push azure master
```

La commande précédente affiche des informations semblables à ce qui suit :
