<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Important** : si vous voulez que votre machine virtuelle utilise un réseau virtuel, lorsque vous la créez, assurez-vous de bien indiquer le réseau virtuel. Vous pouvez configurer une machine virtuelle pour qu'elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d'informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure][1].

1.  Connectez-vous au [portail de gestion Azure][2] en utilisant votre compte Azure.

2.  Dans le portail de gestion, dans le coin inférieur gauche de la page Web, cliquez sur **+New**, sur **Virtual Machine**, puis sur **From Gallery**.
    
    ![Créer une machine
    virtuelle](./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png)

3.  Sélectionnez une image de machine virtuelle OpenSUSE depuis **Platform Images**, puis cliquez sur la flèche Suivant dans le coin inférieur droit de la page.

4.  Sur la page **Virtual machine configuration**, fournissez les informations suivantes :
    
    * Entrez le **Virtual Machine Name**, par exemple << testlinuxvm >>.
    * Entrez un **New User Name**, par exemple << newuser >>, qui sera ajouté au fichier de liste Sudoers.
    * Dans la zone **New Password**, entrez un [mot de passe fort][3].
    * Dans la zone **Confirm Password**, entrez de nouveau le mot de passe.
    * Dans la liste déroulante, sélectionnez la **Size** adéquate.
    
    Cliquez sur la flèche Suivant pour continuer.

5.  Sur la page **Virtual machine mode**, entrez les informations suivantes :
    * Sélectionnez **Standalone Virtual Machine**.
    * Dans la zone **DNS Name**, entrez une adresse DNS valide. Par exemple, << testlinuxvm >>.
    * Dans la zone **Region/Affinity Group/Virtual Network**, sélectionnez une région d'hébergement pour cette image virtuelle.
    
    Cliquez sur la flèche Suivant pour continuer.

6.  Sur la page **Virtual machine options**, dans la zone **Availability Set** sélectionnez **(none)**. Cliquez sur la coche pour continuer.

7.  Patientez pendant qu'Azure prépare votre machine virtuelle.

## Configuration des points de terminaison

Une fois la machine virtuelle créée, configurez les points de terminaison pour la connexion à distance.

1.  Dans le portail de gestion, cliquez sur **Virtual Machines**, sur le nom de votre nouvelle machine virtuelle, puis sur **Endpoints**.

2.  Cliquez sur **Edit Endpoint** en bas de la page, puis remplacez le numéro de **Public Port** du point de terminaison SSH par 22.

## Connexion à la machine virtuelle

Une fois la machine virtuelle déployée et les points de terminaison configurés, vous pouvez vous y connecter en utilisant SSH ou PuTTY.

### Connexion avec SSH

Si vous utilisez un ordinateur Linux, connectez-vous en utilisant SSH. À l'invite de commandes, exécutez :

    $ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Entrez le mot de passe de l'utilisateur.

### Connexion avec PuTTY

Si vous utilisez un ordinateur Windows, connectez-vous à la machine virtuelle en utilisant PuTTY, que vous pouvez télécharger sur la [page de téléchargement de PuTTY][4].

1.  Téléchargez et enregistrez **putty.exe** dans un répertoire de votre ordinateur. Ouvrez une invite de commandes, accédez à ce dossier, puis exécutez **putty.exe**.

2.  Entrez le **Host Name** << testlinuxvm.cloudapp.net >> et le numéro de **Port** << 22 >>. 
	![Écran PuTTY](./media/create-and-configure-opensuse-vm-in-portal/putty.png)

## Mise à jour de la machine virtuelle (facultatif)

1.  Une fois connecté à la machine virtuelle, vous pouvez éventuellement installer des mises à jour système et des correctifs. Exécutez :
    
    `$ sudo zypper update`

2.  Sélectionnez **Software**, puis **Online Update**. La liste des mises à jour s'affiche. Sélectionnez **Accept** pour démarrer l'installation et appliquer tous les nouveaux correctifs (sauf ceux qui sont facultatifs) actuellement disponibles pour votre système.

3.  Une fois l'installation terminée, sélectionnez **Finish**. Votre système est maintenant à jour.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://msdn.microsoft.com/fr-fr/library/ms161962.aspx
[4]: http://www.puttyssh.org/download.html