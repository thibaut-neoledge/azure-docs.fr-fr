<properties 
pageTitle="Configurer une connexion Bureau à distance pour un rôle dans Azure Cloud Services" 
description="Configuration de l’application de service cloud Azure pour autoriser les connexions Bureau à distance" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="07/06/2015" 
ms.author="adegeo"/>

# Configurer une connexion Bureau à distance pour un rôle dans Azure
Après avoir créé un service cloud qui exécute votre application, vous pouvez accéder à distance à une instance de rôle dans cette application pour configurer les paramètres ou résoudre les problèmes. Le service cloud doit être configuré pour un Bureau à distance.

* Ai-je besoin d’un certificat pour activer l’authentification du Bureau à distance ? [Créez-en](cloud-services-certs-create.md) un et configurez-le ci-dessous.
* Vous avez déjà configuré un Bureau à distance pour votre service cloud ? [Connectez-vous](#remote-into-role-instances) à votre service cloud.

## Configurer une connexion Bureau à distance pour des rôles web ou des rôles de travail
Pour activer une connexion Bureau à distance pour un rôle web ou un rôle de travail, vous pouvez configurer la connexion dans le modèle de service de l’application. Vous pouvez également utiliser le portail de gestion Azure pour configurer la connexion une fois que les instances s’exécutent.

### Configurer la connexion dans le modèle de service
L’élément **Imports** doit être ajouté au fichier de définition de service qui importe les modules **RemoteAccess** et **RemoteForwarder** dans le modèle de service. Lorsque vous créez un projet Azure à l’aide de Visual Studio, les fichiers du modèle de service sont créés pour vous.

Le modèle de service se compose d’un fichier [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) et d’un fichier [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg). Un package du fichier de définition avec les fichiers binaires du rôle est créé lorsque l’application correspondant au service cloud est préparée pour le déploiement. Le fichier ServiceConfiguration.cscfg est déployé avec le package d’application et est utilisé par Azure pour déterminer comment l’application doit s’exécuter.

Après avoir créé votre projet, vous pouvez utiliser Visual Studio pour [activer une connexion Bureau à distance](https://msdn.microsoft.com/library/gg443832.aspx).

Après avoir configuré la connexion, le fichier de définition de service doit être similaire à l’exemple suivant avec l’élément `<Imports>` ajouté.

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

Le fichier de configuration de service doit être similaire à l’exemple suivant avec les valeurs que vous avez fournies lorsque vous avez configuré la connexion. Notez les éléments `<ConfigurationSettings>` et `<Certificates>` :

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

Lorsque vous [créez un package](cloud-services-model-and-package.md#cspkg) de l’application et que vous le [publiez](cloud-services-how-to-create-deploy-portal.md), vous devez vous assurer que la case **Activer le Bureau à distance pour tous les rôles** est cochée. [Cet](https://msdn.microsoft.com/library/ff683672.aspx) article fournit les listes des tâches courantes liées à l’utilisation de Visual Studio et de Cloud Services.

### Configurer la connexion sur les instances en cours d’exécution
Dans la page Configurer du service cloud, vous pouvez activer ou modifier les paramètres de la connexion Bureau à distance. Pour plus d’informations, consultez [Configurer l’accès à distance aux instances de rôle](cloud-services-how-to-configure.md).




## À distance dans les instances de rôle
Pour accéder aux instances des rôles web ou des rôles de travail, vous devez utiliser un fichier RDP (Remote Desktop Protocol). Vous pouvez le télécharger à partir du portail de gestion ou le récupérer via un programme.

### Télécharger le fichier RDP via le portail de gestion

Vous pouvez effectuer les étapes suivantes pour récupérer le fichier RDP à partir du portail de gestion, puis utiliser Connexion Bureau à distance pour vous connecter à l’instance en utilisant le fichier :

1.  Dans la page Instances, sélectionnez l’instance, puis cliquez sur **Connexion** dans la barre de commandes.
2.  Cliquez sur **Enregistrer** pour enregistrer le fichier RDP sur votre ordinateur local.
3.  Ouvrez **Connexion Bureau à distance**, cliquez sur **Afficher les options**, puis sur **Ouvrir**.
4.  Accédez à l’emplacement où vous avez enregistré le fichier RDP, sélectionnez-le, puis cliquez sur **Ouvrir** et sur **Se connecter**. Pour terminer la connexion, procédez comme suit.

### Utiliser PowerShell pour récupérer le fichier RDP
Vous pouvez utiliser l’applet de commande [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) pour récupérer le fichier RDP.

### Utiliser Visual Studio pour télécharger le fichier RDP
Dans Visual Studio, vous pouvez utiliser l’Explorateur de serveurs pour créer une connexion Bureau à distance.

1.  Dans l’Explorateur de serveurs, développez le nœud **Azure\Cloud Services\[nom du service cloud]**.
2.  Développez **Intermédiaire** ou **Production**.
3.  Développez le rôle individuel.
4.  Cliquez avec le bouton droit sur l’une des instances de rôle, cliquez sur **Connexion à l’aide de Bureau à distance…**, puis entrez le nom d’utilisateur et le mot de passe.

### Télécharger par programme le fichier RDP via l’API REST de gestion des services
Vous pouvez utiliser l’opération REST [Télécharger le fichier RDP](https://msdn.microsoft.com/library/jj157183.aspx) pour télécharger le fichier RDP. Vous pouvez ensuite utiliser le fichier RDP avec Connexion Bureau à distance pour accéder au service cloud.

## Étapes suivantes
Vous devrez peut-être [créer un package](cloud-services-model-and-package.md) de l’application ou la [charger (déployer)](cloud-services-how-to-create-deploy-portal.md).

<!---HONumber=July15_HO4-->