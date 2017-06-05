## <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement  

Pour FTP et les Git locaux, il est nécessaire de disposer d’un utilisateur de déploiement configuré sur le serveur pour authentifier votre déploiement.

> [!NOTE]
> Un utilisateur de déploiement est requis pour les déploiements FTP et Git en local sur une application web.
> Les éléments `username` et `password` sont liés au compte. Ils sont différents des informations d’identification de votre abonnement Azure.
>

Exécutez la commande [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) pour créer vos informations d’identification de déploiement.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

Le nom d’utilisateur doit être unique et le mot de passe doit être fort. Si vous obtenez une erreur ` 'Conflict'. Details: 409`, modifiez le nom d’utilisateur. Si vous obtenez une erreur ` 'Bad Request'. Details: 400`, utilisez un mot de passe plus fort.

Vous ne devez créer cet utilisateur de déploiement qu’une fois. Vous pouvez l’utiliser pour tous vos déploiements Azure.

Enregistrez le nom d’utilisateur et le mot de passe, car vous les utiliserez lors d’une étape ultérieure pour le déploiement de l’application.