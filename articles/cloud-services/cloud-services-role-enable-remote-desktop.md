---
title: "Activation du Bureau à distance dans Service cloud Azure | Microsoft Docs"
description: "Configuration de l’application de service cloud Azure pour autoriser les connexions Bureau à distance"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: d3110ee8-6526-4585-aba5-d0bc9a713e9b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: f64c41733f8fa7e34a0b0dfbbff2b565af7cf7db
ms.lasthandoff: 03/25/2017

---

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Activer une connexion Bureau à distance pour un rôle dans Azure Cloud Services

> [!div class="op_single_selector"]
> * [Portail Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Portail Azure Classic](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Vous pouvez activer une connexion Bureau à distance dans votre rôle pendant le développement en incluant les modules Bureau à distance dans votre définition de service. Vous pouvez aussi activer le Bureau à distance via l’extension Bureau à distance. Cette deuxième approche est recommandée, car elle vous permet d’activer le Bureau à distance sans avoir à redéployer votre application.

## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Configurer le Bureau à distance à partir du portail Azure Classic
Le portail Azure Classic utilise l’approche basée sur l’extension Bureau à distance, ce qui vous permet d’activer le Bureau à distance même après déployé l’application. La page **Configurer** de votre service cloud vous permet d’activer le Bureau à distance, de changer le compte Administrateur local utilisé pour la connexion aux machines virtuelles ou le certificat employé dans l’authentification, et de définir la date d’expiration.

1. Cliquez sur **Cloud Services**, cliquez sur le nom du service cloud, puis cliquez sur **Configurer**.
2. Cliquez sur le bouton **Distant** en bas de la page.

    ![Services cloud à distance](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)

   > [!WARNING]
   > toutes les instances de rôle sont redémarrées lorsque vous activez pour la première fois le Bureau à distance et cliquez sur OK (coche). Pour éviter un redémarrage, le certificat utilisé pour chiffrer le mot de passe doit être installé sur le rôle. Pour éviter un redémarrage, [téléchargez un certificat pour le service cloud](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) , puis revenez à cette boîte de dialogue.

3. Dans **Roles**, sélectionnez le rôle que vous voulez mettre à jour ou sélectionnez **Tous** pour tous les rôles.
4. Effectuez les modifications suivantes :

   * Pour activer le Bureau à distance, activez la case à cocher **Enable Remote Desktop** . Pour désactiver le Bureau à distance, désactivez la case à cocher.
   * Créez un compte pour utiliser les connexions Bureau à distance aux instances de rôle.
   * Mettez à jour le mot de passe du compte existant.
   * Sélectionnez le certificat téléchargé à utiliser pour l’authentification (téléchargez le certificat en utilisant la commande **Télécharger** sur la page **Certificats**) ou créez un certificat.
   * Changez la date d'expiration de la configuration du Bureau à distance.

5. Lorsque les mises à jour de la configuration sont terminées, cliquez sur **OK** (coche).

## <a name="remote-into-role-instances"></a>À distance dans les instances de rôle
Une fois le Bureau à distance activé sur les rôles, vous pouvez vous connecter à distance à une instance de rôle via divers outils.

Pour vous connecter à une instance de rôle à partir du portail Azure Classic :

1. Cliquez sur **Instances** pour ouvrir la page **Instances**.
2. Sélectionnez une instance de rôle pour laquelle la fonctionnalité Bureau à distance est configurée.
3. Cliquez sur **Connecter**et suivez les instructions pour ouvrir le Bureau.
4. Cliquez sur **Ouvrir**, puis sur **Connecter** pour démarrer la connexion Bureau à distance.

### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Utiliser Visual Studio pour se connecter à distance à une instance de rôle
Dans l’Explorateur de serveurs Visual Studio :

1. Développez le nœud **Azure** > **Cloud Services** > **[nom du service cloud]**.
2. Développez **Intermédiaire** ou **Production**.
3. Développez le rôle individuel.
4. Cliquez avec le bouton droit sur l’une des instances de rôle, cliquez sur **Connexion à l’aide de Bureau à distance**, puis entrez le nom d’utilisateur et le mot de passe.

![Bureau à distance de l’Explorateur de serveurs](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)

### <a name="use-powershell-to-get-the-rdp-file"></a>Utiliser PowerShell pour récupérer le fichier RDP
Vous pouvez utiliser l’applet de commande [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) pour récupérer le fichier RDP. Vous pouvez ensuite utiliser le fichier RDP avec Connexion Bureau à distance pour accéder au service cloud.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Télécharger par programme le fichier RDP via l’API REST de gestion des services
Vous pouvez utiliser l’opération REST [Télécharger le fichier RDP](https://msdn.microsoft.com/library/jj157183.aspx) pour télécharger le fichier RDP.

## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Pour configurer le Bureau à distance dans le fichier de définition de service
Cette méthode vous permet d’activer le Bureau à distance pour l’application pendant le développement. Cette approche exige que les mots de passe chiffrés soient stockés dans le fichier de configuration de service. Par ailleurs, toute mise à jour apportée à la configuration du Bureau à distance nécessite un redéploiement de l’application. Pour éviter ces inconvénients, suivez l’approche basée sur l’extension Bureau à distance décrite précédemment.  

Vous pouvez utiliser Visual Studio pour [activer une connexion Bureau à distance](../vs-azure-tools-remote-desktop-roles.md) à l’aide de l’approche basée sur le fichier de définition de service.  
La procédure ci-dessous décrit les modifications qui doivent être apportées aux fichiers de modèle de service pour activer le Bureau à distance. Visual Studio effectue automatiquement ces modifications lors de la publication.

### <a name="set-up-the-connection-in-the-service-model"></a>Configurer la connexion dans le modèle de service
Utilisez l’élément **Importations** pour importer les modules **RemoteAccess** et **RemoteForwarder** dans le fichier [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef).

Ce fichier de définition de service doit être similaire à l’exemple suivant avec l’élément `<Imports>` ajouté.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
Le fichier [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) doit être semblable à l’exemple suivant. Notez les éléments `<ConfigurationSettings>` et `<Certificates>`. Le certificat spécifié doit être [téléchargé dans le service cloud](cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Ressources supplémentaires
[Configuration des services cloud](cloud-services-how-to-configure.md)
[FAQ relative aux services cloud : Bureau à distance](cloud-services-faq.md#remote-desktop)

