<properties linkid="dev-node-remotedesktop" urlDisplayName="Enable Remote Desktop" pageTitle="Enable remote desktop for cloud services (Node.js)" metaKeywords="Azure Node.js remote access, Azure Node.js remote connection, Azure Node.js VM access, Azure Node.js virtual machine access" description="Learn how to enable remote-desktop access for the virtual machines hosting your Azure Node.js application. " metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Enabling Remote Desktop in Azure" authors="" solutions="" manager="" editor="" />

Activation du Bureau à distance dans Azure
==========================================

Le Bureau à distance vous permet d'accéder au bureau d'une instance de rôle en cours d'exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour configurer la machine virtuelle ou résoudre des problèmes avec votre application.

**Remarque**

Les étapes figurant dans cet article s'appliquent uniquement aux applications Node hébergées en tant que service cloud Azure.

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : configuration du service pour l'accès au Bureau à distance au moyen d'Azure PowerShell](#step1)
-   [Étape 2 : connexion à l'instance de rôle](#step2)
-   [Étape 3 : configuration du service pour désactiver l'accès au Bureau à distance au moyen d'Azure PowerShell](#step3)

Étape 1 : configuration du service pour l'accès au Bureau à distance au moyen d'Azure PowerShell
------------------------------------------------------------------------------------------------

Pour utiliser le Bureau à distance, vous devez configurer votre définition de service et votre configuration de service au moyen d'un nom d'utilisateur, d'un mot de passe et d'un certificat afin de vous authentifier auprès des instances de rôle dans le cloud. [Azure PowerShell](http://go.microsoft.com/?linkid=9790229&clcid=0x409) inclut la cmdlet **Enable-AzureServiceProjectRemoteDesktop**, qui effectue cette configuration pour vous.

Effectuez les étapes suivantes sur l'ordinateur sur lequel la définition de service a été créée.

1.  Dans le menu **Démarrer**, sélectionnez **Azure PowerShell**.

    ![Entrée du menu Démarrer Azure PowerShell](./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png)

2.  Accédez au service d'annuaire, tapez **Enable-AzureServiceProjectRemoteDesktop**, puis entrez le nom d'utilisateur et le mot de passe à utiliser lors de l'authentification auprès d'instances de rôle dans le cloud.

    ![enable-azureserviceprojectremotedesktop](./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png)

3.  Publiez les modifications apportées à la configuration du service dans le cloud. À l'invite **Azure PowerShell**, tapez **Publish-AzureServiceProject**.

    ![publish-azureserviceproject](./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png)

Une fois ces étapes effectuées, les instances de rôle du service dans le cloud sont configurées pour l'accès au Bureau à distance.

Étape 2 : connexion à l'instance de rôle
----------------------------------------

Votre déploiement étant opérationnel dans Azure, vous pouvez vous connecter à l'instance de rôle.

1.  Dans le [portail de gestion Azure](http://manage.windowsazure.com), sélectionnez **Cloud Services**, puis le service déployé à l'étape 1 ci-dessus.

    ![portail de gestion azure](./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png)

2.  Cliquez sur **Instances**, puis sur **Production** ou **Intermédiaire** pour afficher les instances de votre service. Sélectionnez une instance, puis cliquez sur **Connexion** en bas de la page.

    ![Page des instances](./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png)

3.  Lorsque vous cliquez sur **Connexion**, le navigateur Web vous invite à enregistrer un fichier .rdp. Si vous utilisez Internet Explorer, cliquez sur **Ouvrir**.

    ![invite à ouvrir ou enregistrer le fichier .rdp](./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png)

4.  Une fois le fichier ouvert, l'invite de sécurité suivante s'affiche :

    ![Invite de sécurité Windows](./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png)

5.  Cliquez sur **Connexion**. Une invite de sécurité vous permet d'entrer des informations d'identification pour accéder à l'instance. Entrez le mot de passe que vous avez créé à l'[étape 1](#step1), puis cliquez sur **OK**.

    ![invite du nom d'utilisateur/mot de passe](./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png)

Une fois la connexion établie, la connexion Bureau à distance affiche le bureau de l'instance dans Azure. Vous disposez à présent d'un accès distant à votre instance et vous pouvez effectuer toutes les tâches nécessaires à la gestion de votre application.

![session Bureau à distance](./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png)

Étape 3 : configuration du service pour désactiver l'accès au Bureau à distance au moyen d'Azure PowerShell
-----------------------------------------------------------------------------------------------------------

Une fois que vous n'avez plus besoin des connexions Bureau à distance aux instances de rôle dans le cloud, désactivez l'accès Bureau à distance au moyen d'[Azure PowerShell](http://go.microsoft.com/?linkid=9790229&clcid=0x409)

1.  Dans le menu **Démarrer**, sélectionnez **Azure PowerShell**.

2.  Accédez au service d'annuaire et tapez **Disable-AzureServiceProjectRemoteDesktop** :

3.  Publiez les modifications apportées à la configuration du service dans le cloud. À l'invite **Azure PowerShell**, tapez **Publish-AzureServiceProject**.

Ressources supplémentaires
--------------------------

-   [Accès à distance aux instances de rôle dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/hh124107.aspx)
-   [Utilisation du Bureau à distance avec des rôles Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg443832.aspx)


