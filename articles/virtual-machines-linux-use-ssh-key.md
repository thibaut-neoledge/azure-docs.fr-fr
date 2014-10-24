<properties linkid="article" urlDisplayName="Use SSH" pageTitle="Use SSH to connect to Linux virtual machines in Azure" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Learn how to generate and use SSH keys with a Linux virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use SSH with Linux on Azure" authors="" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Utilisation de SSH avec Linux sur Azure

Cette rubrique décrit la procédure à suivre pour générer des clés SSH compatibles avec Azure.

La version actuelle du portail de gestion Azure accepte uniquement les clés publiques SSH encapsulées dans un certificat X509. Pour générer et utiliser des clés SSH avec Azure, procédez comme suit :

## Génération de clés compatibles avec Azure sous Linux

1.  Installez l'utilitaire `openssl` si nécessaire :

    **CentOS/Oracle Linux**

        `sudo yum install openssl`

    **Ubuntu**

        `sudo apt-get install openssl`

    **SLES et openSUSE**

        `sudo zypper install openssl`

2.  Utilisez `openssl` pour générer un certificat X509 avec une paire de clés RSA 2048 bits. Veuillez répondre aux quelques questions posées par `openssl` (ou vous pouvez les laisser vides). Le contenu de ces champs n'est pas utilisé par la plateforme :

            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Modifiez les autorisations sur la clé privée pour la sécuriser.

            chmod 600 myPrivateKey.key

4.  Téléchargez `myCert.pem` lors de la création de la machine virtuelle Linux. Le processus d'approvisionnement installe automatiquement la clé publique de ce certificat dans le fichier `authorized_keys` pour l'utilisateur spécifié dans la machine virtuelle.

5.  Si vous utilisez l'API directement, et non le portail de gestion, convertissez `myCert.pem` en `myCert.cer` (certificat codé DER X509) à l'aide de la commande suivante :

            openssl  x509 -outform der -in myCert.pem -out myCert.cer

## Génération d'une clé à partir d'une clé existante compatible OpenSSH

L'exemple précédent décrit la création d'une clé à utiliser avec Azure. Dans certains cas, il se peut que les utilisateurs possèdent déjà une paire de clés publique et privée compatible OpenSSH et qu'ils souhaitent utiliser les mêmes clés avec Azure.

Les clés privées OpenSSH sont directement accessibles en lecture à partir de l'utilitaire `openssl`. La commande suivante prend une clé privée SSH existante (id\_rsa dans l'exemple ci-dessous) et crée la clé publique `.pem` requise par Microsoft Azure :

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Le fichier **myCert.pem** est la clé publique à utiliser pour déployer une machine virtuelle Linux sur Microsoft Azure. Lors de l'approvisionnement, le fichier `.pem` est converti en clé publique compatible `openssh` et placé dans `~/.ssh/authorized_keys`.

## Connexion à une machine virtuelle Azure à partir de Linux

Chaque machine virtuelle Linux est déployée avec SSH sur un port particulier pouvant être différent du port standard utilisé.

1.  Trouvez le port à utiliser pour vous connecter à la machine virtuelle Linux à partir du portail de gestion.
2.  Connectez-vous à la machine virtuelle Linux à l'aide de `ssh`. Lors de la première connexion, vous êtes invité à accepter l'empreinte digitale de la clé publique de l'hôte.

        ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.  (Facultatif) Vous pouvez copier `myPrivateKey.key` vers `~/.ssh/id_rsa` pour que votre client openssh puisse sélectionner la clé automatiquement sans utiliser l'option `-i`.

## Obtention d'OpenSSL sur Windows

### Utilisation de msysgit

1.  Téléchargez et installez msysgit à partir de l'emplacement suivant : [][]<http://msysgit.github.com/></a>
2.  Exécutez `msys` à partir du répertoire installé (par exemple : c:\\msysgit\\msys.exe)
3.  Accédez au répertoire `bin` en tapant `cd bin`

### Utilisation de GitHub pour Windows

1.  Téléchargez et installez GitHub pour Windows à partir de l'emplacement suivant : [][1]<http://windows.github.com/></a>
2.  Exécutez Git Shell à partir du menu Démarrer \> Tous les programmes \> GitHub, Inc

### Utilisation de cygwin

1.  Téléchargez et installez Cygwin à partir de l'emplacement suivant : [][2]<http://cygwin.com/></a>
2.  Vérifiez que le package OpenSSL et toutes ses dépendances sont installés.
3.  Exécutez `cygwin`.

## Création d'une clé privée sur Windows

1.  Suivez une des procédures ci-dessus pour pouvoir exécuter `openssl.exe`.
2.  Tapez la commande suivante :

        openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Votre écran doit ressembler à ce qui suit :

    ![linuxwelcomegit][linuxwelcomegit]

4.  Répondez aux questions posées.
5.  Deux fichiers doivent être créés : `myPrivateKey.key` et `myCert.pem`.
6.  Si vous utilisez l'API directement, et non le portail de gestion, convertissez `myCert.pem` en `myCert.cer` (certificat codé DER X509) à l'aide de la commande suivante :

        openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Création d'une clé privée pour Putty

1.  Téléchargez et installez puttygen à partir de l'emplacement suivant : [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  Exécutez `puttygen.exe`.
3.  Cliquez sur le menu : Fichier \> Charger une clé privée
4.  Trouvez votre clé privée, que nous avons nommée `myPrivateKey.key`. Vous devez modifier le filtre de fichiers pour afficher **Tous les fichiers (\*.*)**
5.  Cliquez sur **Ouvrir**. Vous devez voir apparaître une boîte de dialogue ressemblant à ceci :

    ![linuxgoodforeignkey][linuxgoodforeignkey]

6.  Cliquez sur **OK**.
7.  Cliquez sur **Enregistrer la clé privée**, qui est mis en surbrillance dans la capture d'écran ci-dessous :

    ![linuxputtyprivatekey][linuxputtyprivatekey]

8.  Enregistrez le fichier au format PPK.

## Utilisation de Putty pour se connecter à une machine Linux

1.  Téléchargez et installez putty à partir de l'emplacement suivant : [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  Exécutez putty.exe
3.  Remplissez le nom d'hôte à l'aide de l'IP du portail de gestion.

    ![linuxputtyconfig][linuxputtyconfig]

4.  Avant de sélectionner **Ouvrir**, cliquez sur l'onglet Connexion \> SSH \> Auth pour choisir votre clé. Consultez la capture d'écran ci-dessous pour le champ à remplir.

    ![linuxputtyprivatekey][4]

5.  Cliquez sur **Ouvrir** pour vous connecter à la machine virtuelle.

  []: http://msysgit.github.com/
  [1]: http://windows.github.com/
  [2]: http://cygwin.com/
  [linuxwelcomegit]: ./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png
  [3]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [linuxgoodforeignkey]: ./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png
  [linuxputtyprivatekey]: ./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png
  [linuxputtyconfig]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png
  [4]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png
