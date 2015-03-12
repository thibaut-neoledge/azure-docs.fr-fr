<properties 
	pageTitle="Activation du Bureau à distance pour les services cloud (Node.js)" 
	description="Découvrez comment activer l'accès Bureau à distance pour les machines virtuelles qui hébergent votre application Azure Node.js." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Activation du Bureau à distance dans Azure

Bureau à distance vous permet d'accéder au bureau d'une instance de rôle
en cours d'exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour
configurer l'ordinateur virtuel ou résoudre des problèmes liés à votre
application.

> [AZURE.NOTE] Les étapes figurant dans cet article s'appliquent uniquement aux applications Node hébergées en tant que service cloud Azure.

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : configuration du service pour l'accès au Bureau à distance au moyen d'Azure PowerShell]
-   [Étape 2 : connexion à l'instance de rôle]
-   [Étape 3 : configuration du service pour désactiver l'accès au Bureau à distance au moyen d'Azure PowerShell]

## <a name="step1"> </a>Étape 1 : configuration du service pour l'accès au Bureau à distance au moyen d'Azure PowerShell

Pour utiliser le Bureau à distance, vous devez configurer votre définition de service et
votre configuration du service à l'aide d'un nom d'utilisateur, d'un mot de passe et d'un certificat pour
authentifier avec des instances de rôle dans le cloud. [Azure PowerShell] intègre l'applet de commande **Enable-AzureServiceProjectRemoteDesktop**, qui
effectue cette configuration pour vous.

Effectuez les opérations suivantes à partir de l'ordinateur sur lequel la
définition de service a été créée.

1.  Dans le menu **Démarrer**, sélectionnez **Azure PowerShell**.

	![Azure PowerShell start menu entry][powershell-menu]

2.  Remplacez le répertoire par le répertoire du service, saisissez
    **Enable-AzureServiceProjectRemoteDesktop**, puis entrez un nom d'utilisateur et un
    mot de passe à utiliser lors de l'authentification avec des instances de rôle dans le
    cloud.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Publiez les modifications apportées à la configuration du service dans le cloud. Lors de l'invite
    **Azure PowerShell**, saisissez
    **Publish-AzureServiceProject**.

	![publish-azureserviceproject][publish-project]

Une fois ces étapes terminées, les instances de rôle du service
dans le cloud sont configurées pour l'accès Bureau à distance.

## <a name="step2"> </a>Étape 2 : connexion à l'instance de rôle

Maintenant que votre déploiement est opérationnel dans Azure, vous pouvez vous connecter à
l'instance de rôle.

1.  Dans le [Portail de gestion Azure], sélectionnez **Cloud Services**, puis le service déployé à l'étape 1 ci-dessus.

	![azure management portal][cloud-services]

2.  Cliquez sur **Instances**, puis sur **Production** ou **Intermédiaire** pour afficher les instances de votre service. Sélectionnez une instance, puis cliquez sur **Connexion** en bas de la page.

    ![The instances page][3]

2.  Lorsque vous cliquez sur **Connexion**, le navigateur Web vous invite à enregistrer un
    fichier .rdp. Si vous utilisez Internet Explorer, cliquez sur **Ouvrir**.

    ![prompt to open or save the .rdp file][4]

3.  Une fois le fichier ouvert, l'invite de sécurité suivante s'affiche :

    ![Windows security prompt][5]

4.  Cliquez sur **Connexion**. Une invite de sécurité s'affiche pour vous permettre de saisir
    vos informations d'identification pour accéder à l'instance. Entrez le mot de passe que vous avez créé
    au cours de l'[Étape 1][Étape 1 : configuration du service pour l'accès au Bureau à distance au moyen d'Azure PowerShell], puis cliquez sur **OK**.

    ![username/password prompt][6]

Lorsque la connexion est établie, la connexion Bureau à distance affiche le
bureau de l'instance dans Azure. Vous disposez à présent
d'un accès à distance à votre instance et pouvez effectuer toutes les tâches nécessaires pour
gérer votre application.

![Remote desktop session][7]

## <a name="step3"> </a>Étape 3 : configuration du service pour désactiver l'accès au Bureau à distance au moyen d'Azure PowerShell

Lorsque vous n'avez plus besoin des connexions Bureau à distance aux instances de rôles
dans le cloud, désactivez l'accès Bureau à distance à l'aide d'[Azure PowerShell]

1.  Dans le menu **Démarrer**, sélectionnez **Azure PowerShell**.

2.  Remplacez le répertoire par le répertoire du service, puis saisissez
    **Disable-AzureServiceProjectRemoteDesktop**:

3.  Publiez les modifications apportées à la configuration du service dans le cloud. Lors de l'invite
    **Azure PowerShell**, saisissez
    **Publish-AzureServiceProject**:

## Ressources supplémentaires

- [Accès à distance aux Instances de rôle dans Azure] 
- [Utilisation du Bureau à distance avec des rôles Azure]

  [Étape 1 : configuration du service pour l'accès au Bureau à distance au moyen d'Azure PowerShell]: #step1
  [Étape 2 : connexion à l'instance de rôle]: #step2
  [Étape 3 : configuration du service pour désactiver l'accès au Bureau à distance au moyen d'Azure PowerShell]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Portail de gestion Azure]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Accès à distance aux Instances de rôle dans Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Utilisation du Bureau à distance avec des rôles Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx

<!--HONumber=45--> 
