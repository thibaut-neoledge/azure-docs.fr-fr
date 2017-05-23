## <a name="prepare-your-intel-nuc"></a>Préparer votre Intel NUC

Pour terminer la configuration matérielle, vous devez :

- connecter votre Intel NUC à l’alimentation incluse dans le kit ;
- connecter votre Intel NUC à votre réseau avec un câble Ethernet.

Vous avez maintenant terminé la configuration matérielle de votre appareil de passerelle Intel NUC.

### <a name="sign-in-and-access-the-terminal"></a>Se connecter et accéder au terminal

Deux méthodes vous permettent d’accéder à un environnement de type terminal sur votre Intel NUC :

- Si vous avez un clavier et un moniteur connectés à votre Intel NUC, vous pouvez accéder directement à l’interpréteur de commandes. Les informations d’identification par défaut sont le nom d’utilisateur **root** et le mot de passe **root**.

- Accédez à l’interpréteur de commandes sur votre Intel NUC à l’aide de SSH à partir de votre ordinateur de bureau.

#### <a name="sign-in-with-ssh"></a>Se connecter avec SSH

Pour vous connecter avec SSH, vous avez besoin de l’adresse IP de votre Intel NUC. Si vous avez un clavier et un moniteur connectés à votre Intel NUC, utilisez la commande `ifconfig` pour rechercher l’adresse IP. Autrement, vous pouvez vous connecter à votre routeur pour répertorier les adresses de périphériques sur votre réseau.

Connectez-vous avec le nom d’utilisateur **root** et le mot de passe **root**.

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>Facultatif : partager un dossier sur votre Intel NUC

Si vous le souhaitez, vous pouvez partager un dossier sur votre Intel NUC avec votre environnement de bureau. Partager un dossier vous permet d’utiliser votre éditeur de texte de bureau favori (tel que [Visual Studio Code](https://code.visualstudio.com/) ou [Sublime Text](http://www.sublimetext.com/)) pour modifier des fichiers sur votre Intel NUC au lieu d’utiliser `nano` ou `vi`.

Pour partager un dossier avec Windows, configurez un serveur Samba sur Intel NUC. Vous pouvez également utiliser le serveur SFTP sur Intel NUC avec un client SFTP sur votre ordinateur de bureau.
