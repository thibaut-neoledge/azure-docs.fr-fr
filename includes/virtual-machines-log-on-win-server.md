<properties services="virtual-machines" title="Connexion à une machine virtuelle exécutant Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

>[AZURE.NOTE] Vous pouvez utiliser l'extension [VMAccess](http://go.microsoft.com/fwlink/p/?LinkId=396856) pour réinitialiser le nom d'utilisateur ou le mot de passe, ou encore pour activer le protocole RDP dans la machine virtuelle. Pour vérifier la configuration requise ou résoudre les problèmes d'ouverture de session, consultez la page [Connexion d'une machine virtuelle Azure à l'aide de RDP or SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Si ce n'est pas déjà fait, connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

3. Dans la barre de commandes, cliquez sur **Connecter**.

	![Log on to the virtual machine](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. Cliquez sur **Ouvrir** pour utiliser le fichier de protocole RDP (Remote Desktop Protocol) qui a été créé automatiquement pour la machine virtuelle.
	
5. Cliquez sur **Connecter** pour continuer.

	![Continue with connecting](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Tapez les informations d'identification du compte d'administration sur l'ordinateur virtuel, puis cliquez sur **OK**. 

 >[AZURE.TIP] Dans la plupart des cas, vous utiliserez le nom d'utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vérifiez le nom d'utilisateur pour vous assurer qu'il possède les informations de domaine correctes :

>- Si la machine virtuelle appartient à un domaine de votre organisation, assurez-vous que le nom d'utilisateur inclut le nom de ce domaine.
- Si la machine virtuelle n'appartient pas à un domaine, supprimez les informations de domaine en commençant la ligne par '\' ou utilisez le nom de la machine virtuelle comme nom de domaine. Par exemple, `\MyUserName` ou `MyTestVM\MyUserName`. 
- Si la machine virtuelle est un contrôleur de domaine, tapez le nom d'utilisateur et le mot de passe d'un compte d'administrateur de domaine pour ce domaine.

Cliquez sur **Oui** pour vérifier l'identité de la machine virtuelle.

![Verify the identity of the machine](./media/virtual-machines-log-on-win-server/connectverify.png)

Vous pouvez désormais travailler à distance avec l'ordinateur virtuel.


<!--HONumber=42-->
