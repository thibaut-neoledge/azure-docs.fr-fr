## <a name="prepare-your-raspberry-pi"></a>Préparer votre Raspberry Pi

### <a name="install-raspbian"></a>Installer Raspbian

Si c’est la première fois que vous utilisez votre Raspberry Pi, vous devez installer le système d’exploitation Raspbian à l’aide de NOOBS sur la carte SD incluse dans le kit. Le [Raspberry Pi Software Guide][lnk-install-raspbian] (Guide logiciel du Raspberry Pi) indique comment installer un système d’exploitation sur votre Raspberry Pi. Pour effectuer ce didacticiel, vous devez avoir installé le système d’exploitation Raspbian sur votre Raspberry Pi.

> [!NOTE]
> NOOBS est déjà installé sur la carte SD incluse dans le [Microsoft Azure IoT Starter Kit pour Raspberry Pi 3][lnk-starter-kits]. Vous pouvez démarrer le Raspberry Pi à partir de cette carte et choisir d’installer le système d’exploitation Raspbian.

Pour terminer la configuration matérielle, vous devez :

- connecter votre Raspberry Pi à l’alimentation incluse dans le kit.
- connecter votre Raspberry Pi à votre réseau à l’aide du câble Ethernet inclus dans votre kit. Vous pouvez également configurer [Connectivité sans fil][lnk-pi-wireless] pour votre Raspberry Pi.

Vous avez maintenant terminé la configuration matérielle de votre Raspberry Pi.

### <a name="sign-in-and-access-the-terminal"></a>Se connecter et accéder au terminal

Deux méthodes vous permettent d’accéder à un environnement de type terminal sur votre Raspberry Pi :

- Si vous disposez d’un clavier et d’un moniteur connectés à votre Raspberry Pi, vous pouvez utiliser l’interface utilisateur graphique Raspbian pour accéder à une fenêtre de terminal.

- Accédez à la ligne de commande sur votre Raspberry Pi à l’aide de SSH à partir de votre ordinateur de bureau.

#### <a name="use-a-terminal-window-in-the-gui"></a>Utiliser une fenêtre de terminal dans l’interface utilisateur graphique

Les informations d’identification par défaut pour Raspbian sont le nom d’utilisateur **pi** et le mot de passe **raspberry**. Dans la barre des tâches dans l’interface utilisateur graphique, vous pouvez lancer l’utilitaire **Terminal** à l’aide de l’icône qui ressemble à un moniteur.

#### <a name="sign-in-with-ssh"></a>Se connecter avec SSH

Vous pouvez utiliser SSH pour accéder à l’aide de la ligne de commande à votre Raspberry Pi. L’article [SSH (Secure Shell)][lnk-pi-ssh] décrit la configuration de SSH sur votre Raspberry Pi et la connexion à partir des systèmes d’exploitation [Windows][lnk-ssh-windows] ou [Linux et Mac][lnk-ssh-linux].

Connectez-vous avec le nom d’utilisateur **pi** et le mot de passe **raspberry**.

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>Facultatif : partager un dossier sur votre Raspberry Pi

Si vous le souhaitez, vous pouvez partager un dossier sur votre Raspberry Pi avec votre environnement de bureau. Partager un dossier vous permet d’utiliser votre éditeur de texte de bureau favori (tel que [Visual Studio Code](https://code.visualstudio.com/) ou [Sublime Text](http://www.sublimetext.com/)) pour modifier des fichiers sur votre Raspberry Pi au lieu d’utiliser `nano` ou `vi`.

Pour partager un dossier avec Windows, configurez un serveur Samba sur le Raspberry Pi. Vous pouvez également utiliser le serveur [SFTP](https://www.raspberrypi.org/documentation/remote-access/) intégré avec un client SFTP sur votre Bureau.

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/