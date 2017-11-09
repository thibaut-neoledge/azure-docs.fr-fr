Dans la fenêtre du terminal local, ajoutez un référentiel distant Azure dans votre référentiel Git local. Cette instance Azure distante a été créée pour vous dans [Créer une application web](#create-a-web-app).

```bash
git remote add azure <URI from previous step>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Lorsque vous êtes invité à saisir un mot de passe, veillez à utiliser celui que vous avez créé dans [Configurer un utilisateur de déploiement](#configure-a-deployment-user), et non pas celui vous permettant de vous connecter au portail Azure.

```bash
git push azure master
```

La commande précédente affiche des informations semblables à ce qui suit :
