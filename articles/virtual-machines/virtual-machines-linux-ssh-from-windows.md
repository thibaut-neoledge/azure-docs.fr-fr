<properties 
	pageTitle="Utilisation de SSH sous Windows pour se connecter à des machines virtuelles Linux | Microsoft Azure" 
description="Apprenez à créer et à utiliser des clés SSH sur un ordinateur Windows pour vous connecter à une machine virtuelle Linux dans Azure." 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2016" 
	ms.author="rasquill"/>

#Utilisation de SSH avec Windows sur Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)


Cette rubrique décrit comment créer et utiliser **ssh rsa** et le format **.pem** pour des fichiers de clé publics et privés sur Windows, que vous pouvez utiliser pour vous connecter à vos machines virtuelles Linux sur Azure avec la commande **ssh**. Si vous avez déjà créé des fichiers **.pem**, vous pouvez les utiliser pour créer des machines virtuelles Linux auxquelles vous pouvez vous connecter à l'aide de **ssh**. Plusieurs autres commandes utilisent le protocole **SSH** et des fichiers de clés pour travailler en toute sécurité, notamment **scp** ou [Secure Copy](https://en.wikipedia.org/wiki/Secure_copy), qui peuvent copier des fichiers en toute sécurité vers et depuis des ordinateurs prenant en charge des connexions **SSH**.

> [AZURE.NOTE] Si vous avez un moment, aidez-nous à améliorer la documentation relative aux machines virtuelles Azure Linux en répondant à cette [enquête rapide](https://aka.ms/linuxdocsurvey) concernant vos expériences. Chaque réponse nous aide à vous faciliter la tâche.

## De quels programmes SSH et de création de programmes avez-vous besoin ?

**SSH** &#8212; ou [secure shell](https://en.wikipedia.org/wiki/Secure_Shell) &#8212; est un protocole de connexion chiffré qui permet d'ouvrir des sessions en toute sécurité à travers des connexions non sécurisées. Il s'agit du protocole de connexion par défaut pour les machines virtuelles Linux hébergées dans Azure, sauf si vous configurez vos machines virtuelles Linux de manière à activer d'autres mécanismes de connexion. Les utilisateurs Windows peuvent également se connecter à des machines virtuelles Linux et les gérer dans Azure à l'aide une mise en œuvre du client **ssh**, mais les ordinateurs Windows ne sont en général pas dotés d'un client **ssh**. Il vous faudra donc en choisir un.

Vous pouvez installer entre autres les clients courants suivants :

- [puTTY et puTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)
- [Git pour Windows](https://git-for-windows.github.io/), qui est fourni avec l'environnement et les outils

Si vous êtes particulièrement expérimenté, vous pouvez également essayer le [nouveau port de l'ensemble d'outils **OpenSSH** pour Windows](http://blogs.msdn.com/b/powershell/archive/2015/10/19/openssh-for-windows-update.aspx). Sachez, toutefois, qu'il s'agit de code qui est en cours de développement et que vous devez examiner la base de code avant de l'utiliser avec des systèmes de production.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Quels fichiers de clés devez-vous créer ?

La configuration SSH de base pour Azure inclut une paire de clés publiques et privées **ssh-rsa** de 2 048 bits (par défaut, **ssh-keygen** stocke ces fichiers sous la forme **~/.ssh/id\_rsa** et **~/.ssh/id-rsa.pub**, sauf si vous modifiez les valeurs par défaut) ainsi qu'un fichier `.pem` généré à partir du fichier de clé privée **id\_rsa** pour une utilisation avec le modèle de déploiement classique du portail classique.

Voici les scénarios de déploiement et les types de fichiers que vous utilisez dans chacun d'eux

1. Les clés **ssh rsa** sont nécessaires pour tout déploiement à l’aide du [portail Azure](https://portal.azure.com), quel que soit le modèle de déploiement.
2. Les fichiers .pem sont nécessaires pour créer des machines virtuelles à l'aide du [portail classique](https://manage.windowsazure.com). Les fichiers .pem sont également pris en charge dans les déploiements classiques qui utilisent l'[interface de ligne de commande CLI](../xplat-cli-install.md).

> [AZURE.NOTE] Si vous prévoyez de gérer des services déployés avec le modèle de déploiement classique, vous pouvez également créer un fichier au format **.cer** à télécharger sur le portail, bien que cela n'implique pas **ssh** ni la connexion à des machines virtuelles Linux (l'objet de cet article). Pour créer ces fichiers sous Windows, tapez : <br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer

## Obtenez ssh keygen et openssl sur Windows ##

[La section](#What-SSH-and-key-creation-programs-do-you-need) ci-dessus répertorie plusieurs utilitaires comprenant `ssh-keygen` et `openssl` pour Windows. Voici quelques exemples :

###Utiliser Git pour Windows###

1.	Téléchargez et installez Git pour Windows à partir de l’emplacement suivant : [https://git-for-windows.github.io/](https://git-for-windows.github.io/)
2.	Exécutez Git Bash à partir du menu Démarrer > Toutes les applications > Git Shell

> [AZURE.NOTE] L'erreur suivante peut être générée lors de l'exécution des commandes `openssl` ci-dessus :

        Unable to load config info from /usr/local/ssl/openssl.cnf

Le moyen le plus simple de résoudre le problème est de définir la variable d’environnement `OPENSSL_CONF`. Le processus de définition de cette variable varie en fonction de l’interpréteur de commande configuré dans Gitbub :

**PowerShell :**

        $Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

**CMD :**

        set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

**Git Bash :**

        export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf
	

###Utilisation de Cygwin###

1.	Téléchargez et installez Cygwin à partir de l’emplacement suivant : [http://cygwin.com/](http://cygwin.com/).
2.	Vérifiez que le package OpenSSL et toutes ses dépendances sont installés.
3.	Exécutez `cygwin`.

## Créez une clé privée##

1.	Suivez l’une des procédures ci-dessus pour pouvoir exécuter `openssl.exe`.
2.	Tapez la commande suivante :

  ```
  openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  ```
3.	Votre écran doit ressembler à ce qui suit :

  ```
  $ openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  Generating a 2048 bit RSA private key
  .......................................+++
  .......................+++
  writing new private key to 'myPrivateKey.key'
  -----
  You are about to be asked to enter information that will be incorporated
  into your certificate request.
  What you are about to enter is what is called a Distinguished Name or a DN.
  There are quite a few fields but you can leave some blank
  For some fields there will be a default value,
  If you enter '.', the field will be left blank.
  -----
  Country Name (2 letter code) [AU]:
  ```

4.	Répondez aux questions posées.
5.	Deux fichiers doivent être créés : `myPrivateKey.key` et `myCert.pem`.
6.	Si vous utilisez l’API directement, et non le portail de gestion, convertissez `myCert.pem` en `myCert.cer` (certificat codé DER X509) à l’aide de la commande suivante :

  ```
  openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
  ```

## Création d'une clé privée pour Putty ##

1. Téléchargez et installez Puttygen à partir de l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Puttygen ne pourra peut-être pas lire la clé privée créée précédemment (`myPrivateKey.key`). Exécutez la commande suivante pour la transformer en clé privée RSA interprétable par Puttygen :

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	La commande ci-dessus doit produire une nouvelle clé privée myPrivateKey\_rsa.

3. Exécutez `puttygen.exe`.

4. Cliquez sur le menu : Fichier > Charger une clé privée.

5. Recherchez votre clé privée, que nous avons nommée `myPrivateKey_rsa` plus haut. Vous devez modifier le filtre de fichiers pour afficher **Tous les fichiers (*.*)**

6. Cliquez sur **Ouvrir**. Vous devez voir apparaître une boîte de dialogue ressemblant à ceci :

	![linuxgoodforeignkey](./media/virtual-machines-linux-ssh-from-windows/linuxgoodforeignkey.png)

7. Cliquez sur **OK**

8. Cliquez sur **Enregistrer la clé privée**, qui est mis en surbrillance dans la capture d'écran ci-dessous :

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-windows/linuxputtygenprivatekey.png)

9. Enregistrez le fichier au format PPK.


## Utilisation de Putty pour se connecter à une machine Linux ##

1.	Téléchargez et installez putty à partir de l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
2.	Exécutez putty.exe
3.	Remplissez le nom d'hôte à l'aide de l'adresse IP du portail de gestion :

	![linuxputtyconfig](./media/virtual-machines-linux-ssh-from-windows/linuxputtyconfig.png)

4.	Avant de sélectionner **Ouvrir**, cliquez sur l'onglet Connexion > SSH > Auth pour choisir votre clé. Examinez la capture d'écran ci-dessous pour le champ à remplir :

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-windows/linuxputtyprivatekey.png)

5.	Cliquez sur **Ouvrir** pour vous connecter à la machine virtuelle.
 

<!---HONumber=AcomDC_0824_2016-->