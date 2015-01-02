<properties writer="kathydav" editor="tysonn" manager="timlt" /> 

**Important **: si vous voulez que votre machine virtuelle utilise un réseau virtuel, lorsque vous la créez, assurez-vous de bien indiquer le réseau virtuel. Vous pouvez configurer une machine virtuelle pour qu'elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d'informations sur les réseaux virtuels, consultez [Vue d'ensemble du réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).


1. Connectez-vous au [portail de gestion Azure][AzurePreviewPortal] en utilisant votre compte Azure.

2. Dans le portail de gestion, dans le coin inférieur gauche de la page web, cliquez sur **+Nouveau**, sur **Machine virtuelle**, puis sur **À partir de la galerie**.

	![Create a New Virtual Machine][Image1]

3. Sous le groupe **SUSE**, sélectionnez une image de machine virtuelle OpenSUSE, puis cliquez sur la flèche Suivant située en bas à droite de la page.


4. Dans la première page **Configuration de la machine virtuelle**, renseignez ou vérifiez les paramètres :

	- Tapez le **Nom de la machine virtuelle**, comme " testlinuxvm ".
	- Vérifiez le **Niveau** et sélectionnez une **Taille**. Le niveau détermine les tailles que vous pouvez choisir.
	- Tapez un **Nouveau nom d'utilisateur**, comme " newuser ", qui sera ajouté au fichier de liste Sudoers.
	- Déterminez le type d'**authentification** à utiliser. Pour obtenir des instructions générales sur les mots de passe, consultez [Mots de passe forts](http://msdn.microsoft.com/fr-fr/library/ms161962.aspx).


5. Dans la page **Configuration de la machine virtuelle** suivante, renseignez ou vérifiez les paramètres :
	- Utilisez le **nouveau service cloud** par défaut.
	- Dans la zone **Nom DNS**, tapez un nom DNS valide à utiliser dans le cadre de l'adresse, comme " testlinuxvm ".
	- Dans la zone **Région/Groupe d'affinités**, sélectionnez une région dans laquelle cette image virtuelle sera hébergée.

6.	Cliquez sur la flèche Suivant pour terminer, puis patientez pendant qu'Azure prépare votre machine virtuelle, puis la redémarre.

##Connexion à la machine virtuelle
Vous allez utiliser SSH ou PuTTY pour vous connecter à la machine virtuelle, selon le système d'exploitation que vous exécutez sur votre ordinateur :

- Si vous utilisez Linux pour vous connecter à la machine virtuelle, utilisez le protocole SSH. À l'invite de commandes, exécutez : 

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
	
	Type the user's password.

- Si vous utilisez un ordinateur Windows pour vous connecter à la machine virtuelle, utilisez le protocole PuTTY. Vous pouvez télécharger PuTTY depuis la [page de téléchargement PuTTY][PuTTYDownLoad]. 

	Téléchargez et enregistrez **putty.exe** dans un répertoire de votre ordinateur. Ouvrez une invite de commande, accédez à ce dossier, puis exécutez **putty.exe**.

	Tapez le nom d'hôte, comme testlinuxvm.cloudapp.net, puis tapez 22 dans le champ **Port**.

	![PuTTY Screen][Image6]  

##Mise à jour de la machine virtuelle (facultatif)
1. Une fois connecté à la machine virtuelle, vous pouvez éventuellement installer des mises à jour système et des correctifs. Pour exécuter la mise à jour, tapez :

	`$ sudo zypper update`

2. Sélectionnez **Logiciels**, puis **Mise à jour en ligne** pour répertorier les mises à jour disponibles. Sélectionnez **Accepter** pour démarrer l'installation et appliquer tous les nouveaux correctifs disponibles (sauf les facultatifs). 

3. Une fois l'installation terminée, sélectionnez **Terminer**.  Votre système est maintenant à jour.

[PuTTYDownload]: http://www.puttyssh.org/download.html
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!--HONumber=35_1-->
