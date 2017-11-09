---
title: "Configurer des connexions sécurisées au cluster Azure Service Fabric | Microsoft Docs"
description: "Découvrez comment utiliser Visual Studio pour configurer des connexions sécurisées prises en charge par le cluster Azure Service Fabric."
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 80501867-dd7a-4648-8bd6-d4f26b68402d
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: dc07b2f38d6fd2de941ebbe99303f6e63cbf122d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Configurer des connexions sécurisées à un cluster Service Fabric à partir de Visual Studio
Découvrez comment utiliser Visual Studio pour accéder en toute sécurité à un cluster Azure Service Fabric avec des stratégies de contrôle d’accès configurées.

## <a name="cluster-connection-types"></a>Types de connexion au cluster
Les connexions prises en charge par le cluster Azure Service Fabric sont de deux types : les connexions **non sécurisées** et les connexions sécurisées **basées sur des certificats x509**. (Pour les clusters Service Fabric hébergés localement, les authentifications **Windows** et **dSTS** sont également prises en charge.) Vous devez configurer le type de connexion au cluster lorsque le cluster est en cours de création. Une fois la connexion créée, son type ne peut plus être modifié.

Les outils Visual Studio Service Fabric prennent en charge tous les types d’authentification permettant de se connecter à un cluster pour assurer la publication. Pour obtenir des instructions sur la configuration d’un cluster Service Fabric sécurisé, consultez [Configuration d’un cluster Service Fabric à partir du portail Azure](service-fabric-cluster-creation-via-portal.md) .

## <a name="configure-cluster-connections-in-publish-profiles"></a>Configurer des connexions au cluster dans les profils de publication
Si vous publiez un projet Service Fabric à partir de Visual Studio, la boîte de dialogue **Publier une application Service Fabric** vous permet de choisir un cluster Azure Service Fabric. Sous **Point de terminaison de connexion**, sélectionnez un cluster existant sous votre abonnement.

![La boîte de dialogue **Publier une application de Service Fabric** permet de configurer une connexion Service Fabric.][publishdialog]

La boîte de dialogue **Publier une application Service Fabric** valide automatiquement la connexion au cluster. Si vous y êtes invité, connectez-vous à votre compte Azure. Si la validation s’effectue correctement, cela signifie que votre système dispose des certificats appropriés pour se connecter au cluster en toute sécurité ou que votre cluster n’est pas sécurisé. La validation peut échouer en raison de problèmes réseau ou si votre système n’est pas correctement configuré pour se connecter à un cluster sécurisé.

![La boîte de dialogue **Publier une application Service Fabric** valide une connexion au cluster Service Fabric existant et correctement configuré.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Pour se connecter à un cluster sécurisé
1. Vérifiez que vous pouvez accéder à l’un des certificats clients approuvés par le cluster de destination. Le certificat est généralement partagé au format Personal Information Exchange (.pfx). Pour savoir comment configurer le serveur afin d’accorder l’accès à un client, consultez [Configuration d’un cluster Service Fabric à partir du portail Azure](service-fabric-cluster-creation-via-portal.md) .
2. Installez le certificat approuvé. Pour ce faire, double-cliquez sur le fichier .pfx ou utilisez le script PowerShell Import-PfxCertificate pour importer les certificats. Installez le certificat à l’emplacement **Cert:\LocalMachine\My**. Il vous est possible d'accepter tous les paramètres par défaut lors de l'importation du certificat.
3. Choisissez la commande **Publier...** du menu contextuel du projet pour ouvrir la boîte de dialogue **Publier une application Azure**, puis sélectionnez le cluster cible. L'outil résout automatiquement la connexion et enregistre les paramètres de connexion sécurisée dans le profil de publication.
4. Facultatif : vous pouvez modifier le profil de publication pour spécifier une connexion sécurisée de cluster.
   
   Puisque vous modifiez manuellement le fichier XML du profil de publication pour spécifier les informations de certificat, prenez soin de noter le nom du magasin de certificats, l'emplacement du magasin et l’empreinte numérique du certificat. Vous devrez fournir ces valeurs pour le nom du magasin de certificats et l’emplacement du magasin. Pour plus d’informations, consultez l’article [Comment : récupérer l’empreinte numérique d’un certificat](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx).
   
   Vous pouvez utiliser les paramètres *ClusterConnectionParameters* pour spécifier les paramètres PowerShell à utiliser lors de la connexion au cluster Service Fabric. Les paramètres valides sont ceux qui sont acceptés par l'applet de commande Connect-ServiceFabricCluster. Consultez la rubrique [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) pour obtenir la liste des paramètres disponibles.
   
   Si vous publiez sur un cluster à distance, vous devez spécifier les paramètres appropriés pour ce cluster spécifique. Voici un exemple de connexion à un cluster non sécurisé :
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Voici un exemple de connexion à un cluster sécurisé basé sur un certificat x509 :
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Modifiez les autres paramètres nécessaires, tels que les paramètres de mise à niveau et l’emplacement du fichier de paramètres d’application, puis publiez votre application à partir de la boîte de dialogue **Publier une application Service Fabric** dans Visual Studio.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’accès aux clusters Service Fabric, consultez [Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
