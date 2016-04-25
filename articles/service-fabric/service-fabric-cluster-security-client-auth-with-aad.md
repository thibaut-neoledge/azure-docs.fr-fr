
<properties
   pageTitle="Sécurité du cluster Service Fabric : authentification client avec Azure Active Directory | Microsoft Azure"
   description="Cet article décrit comment créer un cluster Service Fabric à l’aide d’Azure Active Directory (AAD) pour l’authentification client"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/11/2016"
   ms.author="seanmck"/>

# VERSION PRÉLIMINAIRE : Créer un cluster Service Fabric à l’aide d’Azure Active Directory pour l’authentification client

Vous pouvez sécuriser l’accès aux points de terminaison de gestion d’un cluster Service Fabric à l’aide d’Azure Active Directory (AAD). Cet article décrit comment créer les artefacts AAD nécessaires, les remplir pendant la création des clusters et se connecter à ces clusters par la suite.

>[AZURE.IMPORTANT] L’intégration d’AAD aux clusters Service Fabric est actuellement en version préliminaire. Tous les éléments décrits dans cet article sont disponibles dans le runtime Service Fabric 5.0, mais nous vous recommandons de ne pas l’utiliser pour les clusters de production pour l’instant.

## Modéliser un cluster Service Fabric dans AAD

AAD permet aux organisations (appelées locataires) de gérer l’accès utilisateur aux applications qui sont réparties entre les applications avec une interface utilisateur de connexion web et les applications avec une expérience client natif. Dans ce document, nous partons du principe que vous avez déjà créé un locataire. Si ce n’est pas le cas, commencez par lire [Obtention d’un client Azure Active Directory](../active-directory/active-directory-howto-tenant.md).

Les clusters Service Fabric offrent différents points d’entrée à leurs fonctionnalités de gestion, y compris l’outil web [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) et [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Par conséquent, vous allez créer deux applications AAD pour contrôler l’accès au cluster, une application web et une application native.

Pour simplifier certaines des étapes impliquées dans la configuration d’AAD avec un cluster Service Fabric, nous avons créé un ensemble de scripts Windows PowerShell.

>[AZURE.NOTE] Vous devez effectuer ces étapes *avant* de créer le cluster ; ainsi, dans les situations où les scripts attendent des noms de cluster et des points de terminaison, ces valeurs doivent être les valeurs planifiées et non celles que vous avez déjà créées.

1. [Téléchargez les scripts][sf-aad-ps-script-download] et extrayez-les avant de continuer.

2. Exécutez `SetupApplications.ps1`, en indiquant TenantId, ClusterName et WebApplicationReplyUrl en tant que paramètres. Par exemple :

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Vous pouvez trouver votre **TenantId** en examinant l’URL pour le locataire dans le portail Azure Classic. Le GUID incorporé dans cette URL est le TenantId. Par exemple :

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    Le paramètre **ClusterName** sera utilisé pour préfixer les applications AAD créées par le script. Il n’a pas à correspondre exactement au nom de cluster proprement dit, car il est uniquement destiné à faciliter le mappage des artefacts AAD au cluster Service Fabric avec lequel ils sont utilisés.

    Le paramètre **WebApplicationReplyUrl** est le point de terminaison par défaut retourné par AAD aux utilisateurs à la fin du processus de connexion. La valeur de ce paramètre doit être le point de terminaison Service Fabric Explorer pour votre cluster, qui est par défaut :

    https://&lt;cluster_domain&gt;:19080/Explorer

    Vous serez invité à vous connecter à un compte qui dispose de privilèges d’administration pour le locataire AAD. Une fois cette opération effectuée, le script continuera de créer les applications web et native pour représenter votre cluster Service Fabric. Si vous examinez les applications du locataire dans le [portail Azure Classic][azure-classic-portal], vous devez voir deux nouvelles entrées :

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    Comme le script imprimera le Json requis par le modèle ARM (Azure Resource Manager) quand vous allez créer le cluster dans la section suivante, gardez la fenêtre PowerShell ouverte.

    ![La sortie du script SetupApplication inclut le Json requis par le modèle ARM][setupapp-script-output]

## Création du cluster

Maintenant que vous avez créé les applications AAD, vous pouvez créer le cluster Service Fabric. À ce stade, le portail Azure ne prenant pas en charge la configuration de l’authentification AAD pour les clusters Service Fabric, vous devez le faire avec un modèle ARM dans PowerShell ou Visual Studio.

Notez qu’AAD est uniquement utilisé pour l’authentification client auprès du cluster. Pour créer un cluster sécurisé, vous devez également fournir un certificat qui sera utilisé pour sécuriser la communication entre les nœuds du cluster et pour fournir une authentification serveur pour les points de terminaison de gestion du cluster. Vous trouverez un [modèle ARM pour un cluster sécurisé dans la galerie de démarrage rapide Azure][secure-cluster-arm-template] ou vous pouvez suivre les instructions fournies dans le fichier Lisez-moi du [projet de groupe de ressources Service Fabric dans Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Ajoutez la sortie d’extrait de code du modèle ARM du script `SetupApplication` en tant qu’homologue de fabricSettings, managementEndpoint, etc. Si vous avez fermé la fenêtre, l’extrait de code est également indiqué ci-dessous :

```json
  "azureActiveDirectory": {
    "tenantId": "<your_tenant_id>",
    "clusterApplication": "<your_cluster_application_client_id>",
    "clientApplication": "<your_native_application_client_id>"
  }
```

clusterApplication fait référence à l’application web créée dans la section précédente. Vous trouverez son ID dans la sortie du script SetupApplication sous le nom `WebAppId`. clientApplication fait référence à l’application native et son ID client est disponible dans la sortie du script SetupApplication sous le nom NativeClientAppId.

## Affecter des utilisateurs aux rôles

Une fois que vous avez créé les applications pour représenter votre cluster, vous devez affecter les utilisateurs aux rôles pris en charge par Service Fabric : en lecture seule et administrateur. Pour ce faire, utilisez le [portail Azure Classic][azure-classic-portal].

1. Accédez à votre locataire et choisissez Applications.
2. Choisissez l’application web, qui portera un nom comme `myTestCluster_Cluster`.
3. Cliquez sur l’onglet Utilisateurs.
4. Choisissez un utilisateur à affecter, puis cliquez sur le bouton **Affecter** situé en bas de l’écran.

    ![Bouton Assign users to roles (Affecter des utilisateurs aux rôles)][assign-users-to-roles-button]

5. Sélectionnez le rôle à affecter à l’utilisateur.

    ![Affecter des utilisateurs aux rôles][assign-users-to-roles-dialog]

>[AZURE.NOTE] Pour plus d’informations sur les rôles dans Service Fabric, consultez [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric](service-fabric-cluster-security-roles.md).

## Connexion au cluster

Quand vous accédez à Service Fabric Explorer sur un cluster compatible AAD, vous êtes automatiquement redirigé vers une page de connexion sécurisée.

Pour vous connecter à partir d’un client natif comme Windows PowerShell ou Visual Studio, vous devez indiquer AAD comme mécanisme de connexion, puis fournir une empreinte numérique de certificat serveur, qui sert à valider l’identité du point de terminaison. Les détails de ces deux points d’entrée sont présentés ci-dessous.

### Connexion à partir de Visual Studio

Dans Visual Studio, vous pouvez modifier le profil de publication pour ajouter les attributs nécessaires, comme indiqué ci-dessous :

```xml
<ClusterConnectionParameters     
    ConnectionEndpoint="<your_cluster_endpoint>:19000"  
    AzureActiveDirectory="true"
    ServerCertThumbprint="<your_cert_thumbprint>"
    />
```

Quand vous publiez sur le cluster, Visual Studio affiche une fenêtre de connexion où vous pouvez vous authentifier auprès du cluster.

![Fenêtre de connexion AAD lors de la publication Visual Studio][vs-publish-aad-login]

### Connexion à partir de Windows PowerShell

Dans PowerShell, vous pouvez fournir les paramètres nécessaires à l’applet de commande Connect-ServiceFabricCluster comme indiqué ci-dessous :

```PowerShell
Connect-ServiceFabricCluster -AzureActiveDirectory -ConnectionEndpoint <cluster_endpoint>:19000 -ServerCertThumbprint <server_cert_thumbprint>
```

Comme dans Visual Studio, PowerShell présente une fenêtre de connexion sécurisée pour l’authentification.

>[AZURE.NOTE] Par défaut, la passerelle TCP de Service Fabric utilisée par PowerShell et Visual Studio écoute sur le port 19000. Si vous avez configuré un port différent, vous devez utiliser celui-là à la place quand vous spécifiez le point de terminaison de connexion.

## Problèmes connus

### Erreur d’authentification de client natif en raison d’une adresse de réponse non correspondante

Lors de l’authentification à partir d’un client natif, tel que Visual Studio ou PowerShell, un message d’erreur semblable au suivant peut s’afficher :

*L’adresse de réponse http://localhost/ ne correspond pas à l’adresse de réponse configurée pour l’application &lt;GUID\_application\_cliente\_cluster&gt;*

Pour contourner ce problème, ajoutez **http://<i></i>localhost** comme URI de redirection à la définition de l’application cliente de cluster dans AAD, en plus de l’adresse urn:ietf:wg:oauth:2.0:oob qui existe déjà.

## Étapes suivantes

- En savoir plus sur la [sécurité du cluster Service Fabric](service-fabric-cluster-security.md)
- Découvrez comment [publier une application sur un cluster distant à l’aide de Visual Studio](service-fabric-publish-app-remote-cluster.md)

<!-- Links -->
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[secure-cluster-arm-template]: https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad
[aad-graph-api-docs]: https://msdn.microsoft.com/fr-FR/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com

<!-- Images -->
[assign-users-to-roles-button]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles.png
[setupapp-script-output]: ./media/service-fabric-cluster-security-client-auth-with-aad/setupapp-script-arm-json-output.png
[vs-publish-aad-login]: ./media/service-fabric-cluster-security-client-auth-with-aad/vs-login-prompt.png

<!---HONumber=AcomDC_0413_2016-->