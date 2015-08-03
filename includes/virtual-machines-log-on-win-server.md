
1. Si ce n’est pas déjà fait, connectez-vous au [portail de gestion Microsoft Azure](http://manage.windowsazure.com).

2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

3. Dans la barre de commandes, cliquez sur **Connecter**.

	![Connexion à la machine virtuelle](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. Cliquez sur **Ouvrir** pour utiliser le fichier de protocole RDP (Remote Desktop Protocol) automatiquement créé pour la machine virtuelle.
	
5. Cliquez sur **Connect** pour continuer.

	![Poursuivre la connexion](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Tapez les informations d’identification du compte administratif sur la machine virtuelle, puis cliquez sur **OK**.

 >[AZURE.TIP]Dans la plupart des cas, vous utiliserez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vérifiez le nom d'utilisateur pour vous assurer qu'il possède les informations de domaine correctes :

>- Si la machine virtuelle appartient à un domaine de votre organisation, assurez-vous que le nom d'utilisateur inclut le nom de ce domaine.
- Si la machine virtuelle n’appartient pas à un domaine, supprimez les informations de domaine en faisant précéder la ligne du caractère « \ » ou utilisez le nom de la machine virtuelle comme nom de domaine. Par exemple, `\MyUserName` ou `MyTestVM\MyUserName`. 
- Si la machine virtuelle est un contrôleur de domaine, tapez le nom d'utilisateur et le mot de passe d'un compte d'administrateur de domaine pour ce domaine.

Cliquez sur **Oui** pour vérifier l'identité de la machine virtuelle.

![Vérifier l'identité de la machine](./media/virtual-machines-log-on-win-server/connectverify.png)

Vous pouvez désormais travailler à distance avec l'ordinateur virtuel.

<!---HONumber=July15_HO4-->