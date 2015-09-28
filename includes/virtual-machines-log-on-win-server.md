<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

4. Un clic sur **Connecter** permet de créer et de télécharger un fichier de protocole Remote Desktop Protocol (fichier .rdp). Cliquez sur **Ouvrir** pour utiliser ce fichier.

5. Dans la fenêtre Bureau à distance, cliquez sur **Connecter** pour continuer.

	![Poursuivre la connexion](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Dans la fenêtre Sécurité de Windows, tapez les informations d’identification d’un compte administratif sur la machine virtuelle, puis cliquez sur **OK**.

 	>[AZURE.TIP]Dans la plupart des cas, vous utiliserez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vérifiez le nom d'utilisateur pour vous assurer qu'il possède les informations de domaine correctes :
	>
	>- Si la machine virtuelle appartient à un domaine de votre organisation, assurez-vous que le nom d’utilisateur inclut le nom de ce domaine.
	>- Si la machine virtuelle n’appartient pas à un domaine, supprimez les informations de domaine en commençant la ligne avec le caractère « ’’ » ou utilisez le nom de la machine virtuelle comme nom de domaine. Par exemple, `\MyUserName` ou `MyTestVM\MyUserName`.
	>- Si la machine virtuelle est un contrôleur de domaine, tapez le nom d’utilisateur et le mot de passe d’un compte d’administrateur de domaine pour ce domaine.

7.	Cliquez sur **Oui** pour vérifier l’identité de la machine virtuelle et terminer la connexion.

	![Vérifier l'identité de la machine](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Sept15_HO3-->