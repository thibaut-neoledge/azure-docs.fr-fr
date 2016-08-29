<properties 
	pageTitle="Activation du Bureau à distance pour les services cloud (Node.js)" 
	description="Découvrez comment activer l'accès Bureau à distance pour les machines virtuelles qui hébergent votre application Azure Node.js." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="08/11/2016" 
	ms.author="robmcm"/>

# Activation du Bureau à distance dans Azure

Le Bureau à distance vous permet d'accéder au bureau d'une instance de rôle en cours d'exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour configurer la machine virtuelle ou résoudre des problèmes avec votre application.

> [AZURE.NOTE] Cet article concerne les applications Node.js hébergées en tant que service cloud Azure.


## Composants requis

- Installez et configurez [Azure PowerShell](../powershell-install-configure.md).
- Déploiement d'une application Node.js dans les services cloud Azure Pour plus d'informations, consultez [Création et déploiement d'une application Node.js dans un service cloud Azure](cloud-services-nodejs-develop-deploy-app.md).


## Étape 1 : configuration du service pour l’accès au Bureau à distance au moyen d’Azure PowerShell

Pour utiliser le Bureau à distance, vous devez mettre à jour la définition et la configuration du service Azure avec un nom d'utilisateur, un mot de passe et un certificat.

Effectuez les opérations suivantes à partir d'un ordinateur qui contient les fichiers source de votre application.

1. Exécutez **Windows PowerShell** en tant qu’administrateur (à partir du **menu Démarrer** ou de l’**écran d’accueil**, recherchez **Windows PowerShell**).

2.  Accédez au répertoire qui contient les fichiers de définition de service (.csdef) et de configuration de service (.cscfg).

3. Entrez la cmdlet PowerShell suivante :

		Enable-AzureServiceProjectRemoteDesktop

4. Lorsque vous y êtes invité, entrez un nom d'utilisateur et un mot de passe.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Entrez la cmdlet PowerShell suivante pour publier les modifications :

    	Publish-AzureServiceProject

	![publish-azureserviceproject][publish-project]

## Étape 2 : connexion à l’instance de rôle

Une fois que vous avez publié la définition de service de mise à jour, vous pouvez vous connecter à l'instance de rôle.

1.  Dans le [Portail Azure Classic], sélectionnez **Cloud Services**, puis sélectionnez votre service.

	![Portail Azure Classic][cloud-services]

2.  Cliquez sur **Instances**, puis sur **Production** ou **Intermédiaire** pour afficher les instances de votre service. Sélectionnez une instance, puis cliquez sur **Connexion** en bas de la page.

    ![Page des instances][3]

2.  Lorsque vous cliquez sur **Connexion**, le navigateur Web vous invite à enregistrer un fichier .rdp. Ouvrez ce fichier. (Par exemple, si vous utilisez Internet Explorer, cliquez sur **Ouvrir**.)

    ![invite à ouvrir ou enregistrer le fichier .rdp][4]

3.  Une fois le fichier ouvert, l'invite de sécurité suivante s'affiche :

    ![Invite de sécurité Windows][5]

4.  Cliquez sur **Connexion**. Une invite de sécurité vous permet d'entrer des informations d'identification pour accéder à l'instance. Entrez le mot de passe créé à l’[étape 1][Étape 1 : configuration du service pour l'accès au Bureau à distance à l’aide d'Azure PowerShell], puis cliquez sur **OK**.

    ![invite du nom d'utilisateur/mot de passe][6]

Une fois la connexion établie, la connexion Bureau à distance affiche le bureau de l'instance dans Azure.

![session Bureau à distance][7]

## Étape 3 : configuration du service pour désactiver l’accès au Bureau à distance 

Une fois que vous n'avez plus besoin des connexions Bureau à distance aux instances de rôle dans le cloud, désactivez l'accès Bureau à distance via [Azure PowerShell].

1.  Entrez la cmdlet PowerShell suivante :

    	Disable-AzureServiceProjectRemoteDesktop

2.  Entrez la cmdlet PowerShell suivante pour publier les modifications :

    	Publish-AzureServiceProject

## Ressources supplémentaires

- [Accès à distance aux instances de rôle dans Azure]
- [Utilisation du Bureau à distance avec des rôles Azure]
- [Centre pour développeurs Node.js](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Portail Azure Classic]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
[Accès à distance aux instances de rôle dans Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[Utilisation du Bureau à distance avec des rôles Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 

<!---HONumber=AcomDC_0817_2016-->