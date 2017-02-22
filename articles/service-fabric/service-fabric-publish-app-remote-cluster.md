---
title: Publier une application sur un cluster distant avec Visual Studio | Microsoft Docs
description: "Découvrez comment publier une application sur un cluster Service Fabric distant à l’aide de Visual Studio."
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: faecd892-eb54-4d9c-8023-c67442afb8e8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/29/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e7bd4398503566684187831bde1e82f334a59f32


---
# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>Publier une application sur un cluster distant à l’aide de Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

L’extension Azure Service Fabric pour Visual Studio offre un moyen facile et réutilisable de publier une application sur un cluster Service Fabric. Il est aussi possible de créer des scripts pour cette publication.

## <a name="the-artifacts-required-for-publishing"></a>Artefacts exigés pour la publication
### <a name="deploy-fabricapplicationps1"></a>Deploy-FabricApplication.ps1
Il s’agit d’un script PowerShell qui utilise un chemin d’accès au profil de publication comme paramètre pour publier des applications Service Fabric. Étant donné que ce script fait partie de votre application, vous avez la possibilité de le modifier en fonction des besoins de votre application.

### <a name="publish-profiles"></a>Profils de publication
Un dossier dans le projet d’application Service Fabric appelé **PublishProfiles** contient les fichiers XML qui stockent les informations essentielles à la publication d’une application, par exemple :

* les paramètres de connexion au cluster Service Fabric ;
* le chemin au fichier de paramètres de l’application ;
* les paramètres de mise à niveau.

Par défaut, votre application inclut deux profils de publication : Local.xml et Cloud.xml. Vous pouvez ajouter d’autres profils en copiant et en collant l’un des fichiers par défaut.

### <a name="application-parameter-files"></a>Fichiers de paramètre d’application
Un dossier du projet d’application Service Fabric appelé **ApplicationParameters** contient les fichiers XML pour les valeurs de paramètre du manifeste d’application spécifié par l’utilisateur. Les fichiers manifeste de l’application peuvent être paramétrés de sorte que vous pouvez utiliser des valeurs différentes pour les paramètres de déploiement. Pour plus d’informations sur le paramétrage de votre application, consultez [Gérer plusieurs environnements dans Service Fabric](service-fabric-manage-multiple-environment-app-configuration.md).

> [!NOTE]
> Pour les services d’acteur, vous devez créer le projet avant d’essayer de modifier le fichier dans un éditeur ou par le biais de la boîte de dialogue Publier. Cela est dû au fait que les fichiers de manifeste sont en partie générés pendant la génération.
> 
> 

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>Pour publier une application à l’aide de la boîte de dialogue Publier une application Service Fabric
Les étapes suivantes montrent comment publier une application à l’aide de la boîte de dialogue **Publier une application Service Fabric** fournie par les outils Service Fabric de Visual Studio.

1. Dans le menu contextuel du projet d’application Service Fabric, sélectionnez **Publier...** pour afficher la boîte de dialogue **Publier une application Service Fabric**.
   
    ![Boîte de dialogue **Publier une application Service Fabric**][0]
   
    Le fichier sélectionné dans la liste déroulante **Profil cible** est l’emplacement où tous les paramètres sont enregistrés, à l’exception des **versions du manifeste**. Vous pouvez soit réutiliser un profil existant, soit en créer un en choisissant **<Gérer les profils...>** dans la zone de liste déroulante **Profil cible**. Quand vous choisissez un profil de publication, son contenu s’affiche dans les champs correspondants de la boîte de dialogue. Pour enregistrer vos modifications à tout moment, cliquez sur le lien **Enregistrer le profil** .    
2. Dans la section **Point de terminaison de la connexion** , spécifiez un point de terminaison de publication local ou distant sur le cluster Service Fabric. Pour ajouter ou modifier le point de terminaison de connexion, cliquez sur la liste déroulante **Point de terminaison de connexion** . Cette liste affiche les points de terminaison de connexion disponibles sur le cluster Service Fabric et sur lesquels vous pouvez publier selon vos abonnements Azure. Notez que si vous n’êtes pas déjà connecté à Visual Studio, vous serez invité à le faire.
   
    Utilisez la boîte de dialogue de sélection de cluster pour faire votre choix parmi l’ensemble des abonnements et clusters disponibles.
   
    ![Boîte de dialogue **Sélectionner un cluster Service Fabric**][1]
   
   > [!NOTE]
   > Si vous souhaitez publier sur un point de terminaison arbitraire (par exemple, un cluster Party), consultez la section **Publication sur un point de terminaison de cluster arbitraire** ci-dessous.
   > 
   > 
   
    Une fois que vous avez choisi un point de terminaison, Visual Studio valide la connexion au cluster Service Fabric sélectionné. Si le cluster n’est pas sécurisé, Visual Studio peut vous y connecter immédiatement. Toutefois, si le cluster est sécurisé, vous devez installer un certificat sur votre ordinateur local avant de continuer. Consultez [Configuration de connexions sécurisées](service-fabric-visualstudio-configure-secure-connections.md) pour plus d’informations. Lorsque vous avez terminé, choisissez le bouton **OK** . Le cluster sélectionné s’affiche dans la boîte de dialogue **Publier une application Service Fabric** .
3. Dans la zone de liste déroulante **Fichier de paramètres d’application** , accédez à un fichier de paramètres d’application. Ce dernier contient les valeurs spécifiées par l’utilisateur pour les paramètres du fichier manifeste de l’application. Pour ajouter ou modifier un paramètre, cliquez sur le bouton **Modifier** . Entrez ou modifiez la valeur du paramètre dans la grille **Paramètres** . Lorsque vous avez terminé, cliquez sur le bouton **Enregistrer** .
   
    ![Boîte de dialogue **Modifier les paramètres**][2]
4. Utilisez la case à cocher **Mettre à niveau l’application** pour spécifier si cette action de publication est une mise à niveau. Les actions de mise à niveau sont différentes des actions de publication normales. Consultez la page [Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md) pour obtenir la liste des différences. Pour configurer les paramètres de mise à niveau, cliquez sur le lien **Configurer les paramètres de mise à niveau** . L’éditeur de paramètres de mise à niveau s’affiche. Consultez [Configuration de la mise à niveau d’une application Service Fabric](service-fabric-visualstudio-configure-upgrade.md) pour en savoir plus sur les paramètres de mise à niveau.
5. Cliquez sur le bouton **Versions du manifeste...** pour afficher la boîte de dialogue **Modifier les versions**. Vous devez mettre à jour les applications et les versions de service pour que la mise à niveau puisse se dérouler. Pour savoir comment les versions de manifeste de l’application et du service affectent la mise à niveau, consultez [Didacticiel sur la mise à niveau d’une application Service Fabric](service-fabric-application-upgrade-tutorial.md) .
   
    ![Boîte de dialogue **Modifier les versions**][3]
   
    Si la version de l’application et la version du service utilisent le contrôle de version sémantique comme 1.0.0 ou des valeurs numériques au format 1.0.0.0, sélectionnez l’option **Mettre à jour automatiquement la version de l’application et les versions de service** . Lorsque vous choisissez cette option, les numéros de version du service et de l’application sont automatiquement mis à jour chaque fois que la version du code, de la configuration ou du package de données est mise à jour. Si vous préférez modifier les versions manuellement, désactivez la case à cocher pour désactiver cette fonctionnalité.
   
   > [!NOTE]
   > Pour que toutes les entrées de package s’affichent pour un projet d’acteur, créez d’abord le projet pour générer les entrées dans les fichiers de manifeste de service.
   > 
   > 
6. Lorsque vous avez fini de spécifier tous les paramètres nécessaires, cliquez sur le bouton **Publier** pour publier votre application sur le cluster Service Fabric sélectionné. Les paramètres que vous avez spécifiés sont appliqués au processus de publication.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publier sur un point de terminaison de cluster arbitraire (clusters Party inclus)
L’expérience de la publication de Visual Studio est optimisée pour la publication sur les clusters distants associés à l’un de vos abonnements Azure. Toutefois, il est possible de publier sur des points de terminaison arbitraires (notamment des clusters Party Service Fabric) en modifiant directement le profil de publication XML. Comme décrit ci-dessus, deux profils de publication sont fournis par défaut (**Local.xml** et **Cloud.xml**), mais vous pouvez créer des profils supplémentaires pour différents environnements. Par exemple, vous souhaiterez peut-être créer un profil pour la publication sur les clusters Party, éventuellement nommés **Party.xml**.

Si vous vous connectez à un cluster non sécurisé, tout ce dont vous avez besoin est le point de terminaison de connexion du cluster, par exemple `partycluster1.eastus.cloudapp.azure.com:19000`. Dans ce cas, le point de terminaison de connexion dans le profil de publication doit ressembler à ceci :

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Si vous vous connectez à un cluster sécurisé, vous devrez également fournir les informations du certificat client à partir du magasin local à utiliser pour l’authentification. Pour plus d’informations, consultez [Configuration de connexions sécurisées à un cluster Service Fabric](service-fabric-visualstudio-configure-secure-connections.md).

  Une fois votre profil de publication configuré, vous pouvez le référencer dans la boîte de dialogue de publication comme indiqué ci-dessous.

  ![Nouveau profil de publication dans la boîte de dialogue Publier][4]

  Notez que dans ce cas, le nouveau profil de publication pointe vers l’un des fichiers de paramètre d’application par défaut. Cette opération est appropriée si vous souhaitez publier la même configuration d’application sur plusieurs environnements. En revanche, dans les cas où vous préférez avoir différentes configurations pour chaque environnement sur lequel vous souhaitez publier, il serait judicieux de créer un fichier de paramètre d’application correspondant.

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment automatiser le processus de publication dans un environnement d’intégration continue, consultez [Configurer l’intégration continue Service Fabric](service-fabric-set-up-continuous-integration.md).

[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png



<!--HONumber=Dec16_HO2-->


