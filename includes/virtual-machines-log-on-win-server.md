<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. Un clic sur **Connecter** permet de créer et de télécharger un fichier de protocole Remote Desktop Protocol (fichier .rdp). Cliquez sur **Ouvrir** pour utiliser ce fichier.

	![Capture d’écran du fichier .rdp téléchargé.](./media/virtual-machines-log-on-win-server/open-rdp.png)

5. Un message vous avertit que le fichier .rdp provient d’un éditeur inconnu. C’est normal. Dans la fenêtre Bureau à distance, cliquez sur **Connecter** pour continuer.

	![Capture d’écran d’avertissement relatif à un éditeur inconnu.](./media/virtual-machines-log-on-win-server/rdp-warn.png)

6. Dans la fenêtre Sécurité de Windows, tapez les informations d’identification d’un compte sur la machine virtuelle, puis cliquez sur **OK**.

 	Généralement, les informations d’identification correspondent au nom d’utilisateur et au mot de passe du compte local que vous avez spécifiés quand vous avez créé la machine virtuelle. Le domaine correspond alors au nom de la machine virtuelle et vous devez l’entrer sous la forme *nom\_machine\_virtuelle*&#92;*nom\_utilisateur*.
	
	Si la machine virtuelle appartient à un domaine de votre organisation, assurez-vous que le nom d’utilisateur inclut le nom du domaine sous la forme *domaine*&#92;*nom\_utilisateur*. Le compte doit également être membre du groupe Administrateurs ou bénéficier de privilèges d’accès à distance à la machine virtuelle.
	
	Si la machine virtuelle est un contrôleur de domaine, tapez le nom d’utilisateur et le mot de passe d’un compte d’administrateur de domaine pour ce domaine.

7.	Cliquez sur **Oui** pour vérifier l’identité de la machine virtuelle et terminer la connexion.

	![Capture d'écran montrant un message relatif à la vérification de l'identité de la machine virtuelle.](./media/virtual-machines-log-on-win-server/cert-warning.png)

<!---HONumber=AcomDC_0504_2016-->