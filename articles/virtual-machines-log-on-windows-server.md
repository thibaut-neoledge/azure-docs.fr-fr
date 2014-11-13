<properties urlDisplayName="Log on to a VM" pageTitle="Connexion &agrave; une machine virtuelle ex&eacute;cutant&nbsp;Windows&nbsp;Server" metaKeywords="Azure logging on vm, vm portal" description="Apprenez &agrave; utiliser le portail de gestion&nbsp;Azure pour vous connecter &agrave; une machine virtuelle ex&eacute;cutant&nbsp;Windows&nbsp;Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Connexion &agrave; une machine virtuelle ex&eacute;cutant Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="08/05/2014" ms.author="kathydav" />

# Connexion à une machine virtuelle exécutant Windows Server

Pour une machine virtuelle exécutant le système d'exploitation Windows Server, utilisez le bouton Connecter du portail de gestion pour démarrer une connexion Bureau à distance.

> [WACOM.NOTE] Vous pouvez utiliser l'extension [VMAccess][VMAccess] pour réinitialiser le nom d'utilisateur ou le mot de passe, ou encore pour activer le protocole RDP dans la machine virtuelle. Pour des conseils concernant les exigences et le dépannage, consultez [Connexion d'une machine virtuelle Azure à l'aide de RDP or SSH][Connexion d'une machine virtuelle Azure à l'aide de RDP or SSH].

1.  Si ce n’est pas déjà fait, connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

3.  Dans la barre de commandes, cliquez sur **Connecter**.

    ![Se connecter à la machine virtuelle][Se connecter à la machine virtuelle]

4.  Cliquez sur **Ouvrir** pour utiliser le fichier de protocole RDP (Remote Desktop Protocol) qui a été créé automatiquement pour la machine virtuelle.

5.  Cliquez sur **Connect** pour continuer.

    ![Poursuivre la connexion][Poursuivre la connexion]

6.  Tapez le nom d'utilisateur et le mot de passe du compte d'administration sur la machine virtuelle, puis cliquez sur **OK**. Ce sont le nom d'utilisateur et le mot de passe que vous avez indiqués lors de la création de la machine virtuelle, sauf si la machine virtuelle est maintenant un contrôleur de domaine. Dans ce cas, saisissez le nom d'utilisateur et le mot de passe d'un compte d'administrateur de domaine.

7.  Cliquez sur **Yes** pour vérifier l'identité de la machine virtuelle.

    ![Vérifier l'identité de la machine][Vérifier l'identité de la machine]

    Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

  [VMAccess]: http://go.microsoft.com/fwlink/p/?LinkId=396856
  [Connexion d'une machine virtuelle Azure à l'aide de RDP or SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Se connecter à la machine virtuelle]: ./media/virtual-machines-log-on-windows-server/connectwindows.png
  [Poursuivre la connexion]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Vérifier l'identité de la machine]: ./media/virtual-machines-log-on-windows-server/connectverify.png
