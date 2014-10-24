<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn how to log on to a virtual machine running Windows Server 2008 R2 by using the Azure Management Portal." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

> [WACOM.NOTE] Pour connaître les conditions requises et obtenir des astuces concernant le dépannage, consultez la page [Se connecter à une machine virtuelle Azure avec RDP ou SSH][Se connecter à une machine virtuelle Azure avec RDP ou SSH].

1.  Si ce n’est pas déjà fait, connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

3.  Dans la barre de commandes, cliquez sur **Connecter**.

    ![Se connecter à la machine virtuelle][Se connecter à la machine virtuelle]

4.  Cliquez sur **Ouvrir** pour utiliser le fichier de protocole RDP (Remote Desktop Protocol) qui a été créé automatiquement pour la machine virtuelle.

5.  Cliquez sur **Connecter** pour continuer le processus de connexion.

    ![Poursuivre la connexion][Poursuivre la connexion]

6.  Tapez le nom d'utilisateur et le mot de passe du compte d'administration sur la machine virtuelle, puis cliquez sur **OK**.

7.  Cliquez sur **Oui** pour vérifier l'identité de la machine virtuelle.

    ![Vérifier l'identité de la machine][Vérifier l'identité de la machine]

    Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

  [Se connecter à une machine virtuelle Azure avec RDP ou SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Se connecter à la machine virtuelle]: ./media/virtual-machines-log-on-win-server/connectwindows.png
  [Poursuivre la connexion]: ./media/virtual-machines-log-on-win-server/connectpublisher.png
  [Vérifier l'identité de la machine]: ./media/virtual-machines-log-on-win-server/connectverify.png
