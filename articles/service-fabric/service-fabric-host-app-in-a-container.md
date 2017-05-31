---
title: "Déployer une application .NET dans un conteneur vers Azure Service Fabric | Microsoft Docs"
description: "Explique comment empaqueter une application .NET dans Visual Studio dans un conteneur Docker. Cette nouvelle application « conteneur » est ensuite déployée sur un cluster Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/10/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: a965fcb9dd5caf9656af5ae381b25baaaa01ec6d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>Déployer une application .NET dans un conteneur vers Azure Service Fabric

Ce didacticiel vous montre comment déployer une application ASP.NET existante dans un conteneur Windows à l’aide de la mise à jour 3 de Visual Studio 2017 en version préliminaire. Vous apprendrez ensuite à déployer le conteneur dans Azure à l’aide de Visual Studio Team Services et à héberger le conteneur dans un cluster Service Fabric.

## <a name="prerequisites"></a>Composants requis

1. Installez [Docker CE pour Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) afin que vous puissiez exécuter des conteneurs sur Windows 10.
2. Familiarisez-vous avec [l’utilisation de Windows 10 Containers][link-container-quickstart].
3. Dans cet article, nous allons utiliser **Fabrikam Fiber**, un exemple d’application que vous pouvez télécharger [ici][link-fabrikam-github].
4. [Azure PowerShell][link-azure-powershell-install]
5. [Extension Continuous Delivery Tools pour Visual Studio 2017][link-visualstudio-cd-extension]

>[!NOTE]
>Si vous utilisez pour la première fois des images de conteneurs Windows sur votre ordinateur, Docker CE doit extraire les images de base de vos conteneurs. Les images utilisées dans ce didacticiel font une taille de 14 Go. Poursuivez et exécutez la commande suivante dans PowerShell pour extraire les images de base :
>
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

## <a name="containerize-the-application"></a>Conteneuriser l’application

Pour commencer à exécuter notre application dans un conteneur, nous devons ajouter la **prise en charge Docker** au projet dans Visual Studio. Lorsque vous ajoutez la **prise en charge Docker** à l’application, deux événements se produisent. Un fichier _docker_ est d’abord ajouté au projet. Ce nouveau fichier décrit la manière dont doit être générée l’image de conteneur. Dans un deuxième temps, un nouveau projet _docker-compose_ est ajouté à la solution. Ce nouveau projet contient quelques fichiers docker-compose qui peuvent être utilisés pour décrire la manière dont le conteneur est exécuté.

En savoir plus sur l’utilisation de [Visual Studio Container Tools][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Ajouter la prise en charge Docker

1. Ouvrez le fichier **FabrikamFiber.CallCenter.sln** dans Visual Studio.

2. Cliquez avec le bouton droit sur le projet **FabrikamFiber.Web** > **Ajouter** > **Prise en charge de Docker**.

### <a name="add-support-for-sql"></a>Ajouter la prise en charge de SQL

Puisque cette application utilise SQL en tant que fournisseur de données, vous avez besoin d’un SQL Server pour exécuter l’application. Dans ce didacticiel, nous utilisons une instance de SQL Server exécutée dans un conteneur.
Pour indiquer à Docker que nous souhaitons exécuter un SQL Server dans un conteneur, nous pouvons référencer une image de conteneur SQL Server dans notre fichier docker-compose.override.yml contenu dans le projet docker-compose. De cette façon, le serveur SQL Server en cours d’exécution dans le conteneur est utilisé lors du débogage de l’application dans Visual Studio.

1. Ouvrez l’ **Explorateur de solutions**.

2. Ouvrez **docker-compose** > **docker-compose.yml** > **docker-compose.override.yml**.

3. Sous le nœud `services:`, ajoutez un nouveau nœud nommé `db:`. Ce nœud déclare exécuter un serveur SQL Server dans un conteneur.

   ```yml
     db:
       image: microsoft/mssql-server-windows-developer
       environment:
         sa_password: "Password1"
         ACCEPT_EULA: "Y"
       ports:
         - "1433"
       healthcheck:
         test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
         interval: 1s
         retries: 20
   ```

   >[!NOTE]
   >Vous pouvez utiliser l’instance SQL Server de votre choix pour le débogage local, tant qu’elle est accessible à partir de votre hôte. **localdb** ne prend cependant pas en charge la communication `container -> host`.

   >[!NOTE]
   >Si vous voulez exécuter votre serveur SQL Server dans un conteneur, vous pouvez choisir d’ajouter le code précédent dans le fichier docker-compose.yml au lieu du fichier docker-compose.override.yml.


4. Modifiez le nœud `fabrikamfiber.web` et ajoutez un nouveau nœud enfant nommé `depends_on:`. Le service `db` (le conteneur SQL Server) peut ainsi démarrer avant notre application web (fabrikamfiber.web).

   ```yml
     fabrikamfiber.web:
       ports:
         - "80"
       depends_on:
         - db
   ```

5. Dans le projet **FabrikamFiber.Web**, mettez à jour la chaîne de connexion dans le fichier **web.config** pour pointer vers le serveur SQL Server du conteneur.

   ```xml
   <add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   
   <add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   ```

   >[!NOTE]
   >Si vous souhaitez utiliser un autre SQL Server lors de la création d’une version build de votre application web, ajoutez une autre chaîne de connexion à votre fichier web.release.config.

6. Appuyez sur **F5** pour exécuter et déboguer l’application dans votre conteneur.

   Edge ouvre la page de démarrage définie de votre application en utilisant l’adresse IP du conteneur sur le réseau NAT interne (généralement 172.x.x.x). Pour en savoir plus sur le débogage des applications dans des conteneurs à l’aide de Visual Studio 2017, consultez [cet article][link-debug-container].

   ![exemple de fabrikam dans un conteneur][image-web-preview]

L’application est maintenant prête à être générée et empaquetée dans un conteneur. Une fois l’image de conteneur intégrée sur votre ordinateur, vous pouvez la placer dans n’importe quel registre de conteneur et la transmettre à n’importe quel hôte pour l’exécuter.

Dans la suite de ce didacticiel, vous allez utiliser Visual Studio Team Services pour générer et déployer le conteneur, le placer dans un registre Azure Container Registry et le déployer sur un cluster Service Fabric exécuté dans Azure.

## <a name="create-a-service-fabric-cluster"></a>Créer un cluster Service Fabric

Si vous possédez déjà un cluster Service Fabric vers lequel déployer votre application, vous pouvez ignorer cette étape. Sinon, lisez la suite pour créer un cluster Service Fabric.

>[!NOTE]
>La procédure suivante crée un cluster Service Fabric sécurisé par un certificat auto-signé, qui est placé dans un coffre de clés créé dans le cadre du déploiement. Pour plus d’informations sur l’utilisation de l’authentification Azure Active Directory, consultez l’article [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager][link-servicefabric-create-secure-clusters].

1. Téléchargez une copie locale des fichiers de modèle et de paramètres Azure référencés dans le code suivant.
    * [Modèle Azure Resource Manager pour Service Fabric][link-sf-clustertemplate] : modèle Resource Manager qui définit un cluster Service Fabric.
    * [Fichier de paramètres de modèle][link-sf-clustertemplate-parameters] : fichier de paramètres permettant de personnaliser le déploiement du cluster.
2. Personnalisez les paramètres suivants dans le fichier de paramètres :
  
   | Paramètre       | Description |
   | --------------- | ----------- |
   | clusterName     | Nom de votre cluster. |
   | adminUsername   | Compte d’administrateur local sur les machines virtuelles du cluster. |
   | adminPassword   | Mot de passe du compte d’administrateur local sur les machines virtuelles du cluster. |
   | clusterLocation | Région Azure dans laquelle déployer le cluster. |
   | vmInstanceCount | Nombre de nœuds de votre cluster (peut être 1). |

3. Ouvrez **PowerShell**.
4. **Connectez-vous** à Azure.

   ```powershell
   Login-AzureRmAccount
   ```

5. Sélectionnez **l’abonnement** dans lequel vous souhaitez déployer le cluster.

   ```powershell
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

6. Créez et **chiffrez un mot de passe** pour le certificat utilisé par Service Fabric.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```

7. **Créez le cluster** en exécutant la commande suivante :

   ```powershell
   New-AzureRmServiceFabricCluster 
       -TemplateFile C:\users\me\downloads\PreviewSecureClusters.json `
       -ParameterFile C:\users\me\downloads\myCluster.parameters.json `
       -CertificateOutputFolder C:\users\me\desktop\ `
       -CertificatePassword $pwd `
       -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
       -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >Le paramètre `-CertificateSubjectName` doit s’aligner sur le paramètre clusterName spécifié dans le fichier de paramètres, ainsi que sur le domaine lié à la région Azure que vous avez sélectionnée, par exemple : `clustername.eastus.cloudapp.azure.com`.
   
    Une fois la configuration terminée, cette commande génère des informations sur le cluster créé dans Azure, et copie le certificat dans le répertoire -CertificateOutputFolder.

8. **Double-cliquez** sur le certificat pour l’installer sur votre ordinateur local.

## <a name="deploy-with-visual-studio"></a>Déployer avec Visual Studio

Pour configurer le déploiement à l’aide de Visual Studio Team Services, vous devez installer [l’extension Continuous Delivery Tools pour Visual Studio 2017][link-visualstudio-cd-extension]. Cette extension facilite le déploiement dans Azure en configurant Visual Studio Team Services et vous permet de déployer votre application sur votre cluster Service Fabric.

Pour commencer, votre code doit être hébergé dans le contrôle de code source. Le reste de cette section utilise **git**.

1. En bas à droite de Visual Studio, cliquez sur **Ajouter au contrôle de code Source** > **Git** (ou l’option de votre choix).

   ![appuyez sur le bouton de contrôle de code source][image-source-control]

2. Dans le volet _Team Explorer_, appuyez sur **Publier le dépôt Git**.

3. Sélectionnez le nom de votre dépôt VSTS et appuyez sur **Dépôt**.

   ![publier un dépôt dans VSTS][image-publish-repo]

Maintenant que votre code est synchronisé avec un dépôt de code source VSTS, vous pouvez configurer l’intégration continue et la livraison continue.

1. Dans _l’Explorateur de solutions_, cliquez avec le bouton droit sur la **solution** > **Configurer la livraison continue**.

2. Sélectionnez l’abonnement Azure.

3. Définissez **Type d’hôte** sur **Cluster Service Fabric**.

   >[!NOTE]
   >Selon les types de conteneurs que vous créez, nous allons ajouter pour vous d’autres options permettant d’héberger votre application dans des conteneurs sur Azure. 

4. Définissez **Hôte cible** sur le cluster Service Fabric que vous avez créé dans la section précédente.

5. Choisissez un **Registre de conteneurs** dans lequel publier votre conteneur.

   >[!TIP]
   >Cliquez sur le bouton **Modifier** pour créer un registre de conteneurs.
    
6. Appuyez sur **OK**.

   ![configurer l’intégration continue pour service fabric][image-setup-ci]
   
   Une fois la configuration terminée, votre conteneur est déployé dans le cluster Service Fabric chaque fois que vous appliquez des mises à jour dans votre dépôt.

7. **Démarrez une build** à l’aide de **Team Explorer** et observez l’exécution de votre application de conteneur dans Service Fabric.

Maintenant que vous avez mis en conteneur et déployé la solution de centre d’appareils Fabrikam, vous pouvez ouvrir le [portail Azure][link-azure-portal] et voir l’application en cours d’exécution dans Service Fabric. Pour tester l’application, ouvrez un navigateur web et accédez à l’URL de votre cluster Service Fabric.

## <a name="next-steps"></a>Étapes suivantes

- [Outils de conteneurs dans Visual Studio][link-visualstudio-container-tools]
- [Prise en main des conteneurs dans Service Fabric][link-servicefabric-containers]
- [Créer des applications Service Fabric][link-servicefabric-createapp]

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-sf-clustertemplate-parameters]: https://aka.ms/securepreviewonelineclusterparameters

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

