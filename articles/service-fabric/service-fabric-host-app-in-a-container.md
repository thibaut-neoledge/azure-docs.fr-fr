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
ms.date: 05/19/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 56788914452c0f3a7072d6b2805866072b9b7fea
ms.contentlocale: fr-fr
ms.lasthandoff: 05/23/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>Déployer une application .NET dans un conteneur vers Azure Service Fabric

Ce didacticiel vous montre comment déployer une application ASP.NET existante dans un conteneur Windows à l’aide de la mise à jour 3 de Visual Studio 2017 en version préliminaire. Vous apprendrez ensuite à déployer le conteneur dans Azure à l’aide de Visual Studio Team Services et à héberger le conteneur dans un cluster Service Fabric.

## <a name="prerequisites"></a>Composants requis

1. Installez [Docker CE pour Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) afin que vous puissiez exécuter des conteneurs sur Windows 10.
2. Familiarisez-vous avec [l’utilisation de Windows 10 Containers][link-container-quickstart].
3. Pour ce didacticiel, nous allons utiliser **Fabrikam Fiber CallCenter**, un exemple d’application que vous pouvez télécharger [ici][link-fabrikam-github].
4. [Azure PowerShell][link-azure-powershell-install]
5. [Extension Continuous Delivery Tools pour Visual Studio 2017][link-visualstudio-cd-extension]
6. Un [abonnement Azure][link-azure-subscription] et un [compte Visual Studio Team Services][link-vsts-account]. Vous pouvez parcourir ce didacticiel en utilisant des niveaux gratuits de tous les services.

>[!NOTE]
>Si vous utilisez pour la première fois des images de conteneurs Windows sur votre ordinateur, Docker CE doit extraire les images de base de vos conteneurs. Les images utilisées dans ce didacticiel font une taille de 14 Go. Poursuivez et exécutez la commande suivante dans PowerShell pour extraire les images de base :
>1. docker pull microsoft/mssql-server-windows-developer
>2. docker pull microsoft/aspnet:4.6.2

## <a name="containerize-the-application"></a>Conteneuriser l’application

Pour commencer à exécuter notre application dans un conteneur, nous devons ajouter la **prise en charge Docker** au projet dans Visual Studio. Lorsque vous ajoutez la **prise en charge Docker** à l’application, deux événements se produisent. Un fichier _docker_ est d’abord ajouté au projet. Ce nouveau fichier décrit la manière dont doit être générée l’image de conteneur. Dans un deuxième temps, un nouveau projet _docker-compose_ est ajouté à la solution. Le nouveau projet contient quelques fichiers docker-compose. Les fichiers docker-compose peuvent être utilisés pour décrire la manière dont le conteneur est exécuté.

En savoir plus sur l’utilisation de [Visual Studio Container Tools][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Ajouter la prise en charge Docker

1. Ouvrez le fichier **FabrikamFiber.CallCenter.sln** dans Visual Studio.

2. Cliquez avec le bouton droit sur le projet **FabrikamFiber.Web** > **Ajouter** > **Prise en charge de Docker**.

### <a name="add-support-for-sql"></a>Ajouter la prise en charge de SQL

Puisque cette application utilise SQL en tant que fournisseur de données, vous avez besoin d’un SQL Server pour exécuter l’application. Dans ce didacticiel, nous utilisons une instance de SQL Server exécutée dans un conteneur pour le débogage local.
Pour exécuter une instance de SQL Server dans un conteneur, au moment du débogage, nous pouvons référencer une image conteneur SQL Server dans notre fichier docker-compose.override.yml. 

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

   >[!WARNING]
   >L’exécution d’une instance de SQL Server dans un conteneur ne prend pas en charge les données persistantes quand le conteneur s’arrête. N’utilisez pas cette configuration pour la production.

4. Modifiez le nœud `fabrikamfiber.web` et ajoutez un nouveau nœud enfant nommé `depends_on:`. Le service `db` (le conteneur SQL Server) peut ainsi démarrer avant notre application web (fabrikamfiber.web).

   ```yml
     fabrikamfiber.web:
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

Dans la suite de ce didacticiel, vous allez utiliser Visual Studio Team Services pour déployer le conteneur sur un cluster Service Fabric exécuté dans Azure.

## <a name="create-a-service-fabric-cluster"></a>Créer un cluster Service Fabric

Si vous possédez déjà un cluster Service Fabric vers lequel déployer votre application, vous pouvez ignorer cette étape. Sinon, lisez la suite pour créer un cluster Service Fabric.

>[!NOTE]
>La procédure suivante crée un cluster Service Fabric en préversion à nœud unique (machine virtuelle unique) sécurisé par un certificat auto-signé, qui est placé dans un coffre de clés.
>Les clusters à nœud unique ne peuvent pas être mis à l’échelle au-delà d’une machine virtuelle, et les clusters en préversion ne peuvent pas être mis à niveau vers une version plus récente.
>Pour calculer le coût lié à l’exécution d’un cluster Service Fabric dans Azure, utilisez la [calculatrice de prix Azure][link-azure-pricing-calculator].
>Pour plus d’informations sur la création de clusters Service Fabric, consultez l’article [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager][link-servicefabric-create-secure-clusters].

1. Téléchargez une copie locale du modèle Azure Resource Manager et le fichier de paramètres à partir de ce dépôt GitHub :
    * [Modèle Azure Resource Manager pour Service Fabric][link-sf-clustertemplate]
       - **azuredeploy.json** : modèle Azure Resource Manager qui définit un cluster Service Fabric.
       - **azuredeploy.parameters.json** : fichier de paramètres permettant de personnaliser le déploiement du cluster.
2. Personnalisez les paramètres suivants dans le fichier de paramètres :
  
   | Paramètre       | Description | Valeur suggérée |
   | --------------- | ----------- | --------------- |
   | clusterLocation | Région Azure dans laquelle déployer le cluster. | *par exemple, westeurope, eastasia, eastus* |
   | clusterName     | Nom de votre cluster. | *par exemple, bobs-sfpreviewcluster* |
   | adminUsername   | Compte d’administrateur local sur les machines virtuelles du cluster. | *N’importe quel nom d’utilisateur Windows Server valide* |
   | adminPassword   | Mot de passe du compte d’administrateur local sur les machines virtuelles du cluster. | *N’importe quel mot de passe Windows Server valide* |
   | clusterCodeVersion | Version de Service Fabric à exécuter. (255.255.X.255 sont des préversions). | **255.255.5713.255** |
   | vmInstanceCount | Nombre de machines virtuelles dans votre cluster (1 ou de 3 à 99). | **1** |

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
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >Le paramètre `-CertificateSubjectName` doit s’aligner sur le paramètre clusterName spécifié dans le fichier de paramètres, ainsi que sur le domaine lié à la région Azure que vous avez sélectionnée, par exemple : `clustername.eastus.cloudapp.azure.com`.
   
    Une fois la configuration terminée, cette commande génère des informations sur le cluster créé dans Azure, et copie le certificat dans le répertoire -CertificateOutputFolder.

8. **Double-cliquez** sur le certificat pour ouvrir l’Assistant Importation de certificat.

9. Utilisez les paramètres par défaut, mais veillez à cocher la case **Marquer cette clé comme exportable** à l’étape **Protection de clé privée**. Visual Studio doit exporter le certificat pendant la configuration d’Azure Container Registry pour l’authentification de cluster Service Fabric plus loin dans ce didacticiel.

10. Vous pouvez maintenant ouvrir Service Fabric Explorer dans un navigateur. L’URL est le point de terminaison de gestion (**ManagementEndpoint**) dans le résultat de la cmdlet PowerShell, par exemple, *https://mycluster.westeurope.cloudapp.azure.com:19080* 

>[!NOTE]
>Quand vous ouvrez Service Fabric Explorer, vous voyez une erreur de certificat, car vous utilisez un certificat auto-signé. Dans Edge, vous devez cliquer sur *Détails*, puis sur le lien *Atteindre la page web*. Dans Chrome, vous devez cliquer sur *Avancé*, puis sur le lien *proceed* (Continuer).

>[!NOTE]
>Si la création du cluster échoue, vous pouvez toujours réexécuter la commande et mettre ainsi à jour les ressources déjà déployées. Si un certificat a été créé dans le cadre du déploiement ayant échoué, un nouveau est généré. Pour résoudre les problèmes liés à la création du cluster consultez l’article [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager][link-servicefabric-create-secure-clusters].

## <a name="getting-the-application-ready-for-the-cloud"></a>Préparation de l’application pour le cloud

Pour préparer l’application en vue de son exécution dans Service Fabric dans Azure, nous devons effectuer deux étapes :

1. Exposer le port sur lequel nous souhaitons pouvoir atteindre notre application web dans le cluster Service Fabric
2. Fournir une base de données SQL prête pour la production pour notre application

### <a name="1-exposing-the-web-application-in-service-fabric"></a>1. Exposition de l’application web dans Service Fabric
Le cluster Service Fabric que nous avons configuré a son port 80 ouvert par défaut dans Azure Load Balancer, qui équilibre le trafic entrant vers le cluster. Nous pouvons exposer notre conteneur sur ce port par le biais de notre fichier docker-compose.yml.

1. Ouvrez l’ **Explorateur de solutions**.

2. Ouvrez **docker-compose** > **docker-compose.yml**.

3. Modifiez le nœud `fabrikamfiber.web` et ajoutez un nouveau nœud enfant nommé `ports:` et la chaîne `- "80:80"`. Le fichier complet docker-compose.yml doit ressembler à ceci :

   ```yml
      version: '3'

      services:
        fabrikamfiber.web:
          image: fabrikamfiber.web
          build:
            context: .\FabrikamFiber.Web
            dockerfile: Dockerfile
          ports:
            - "80:80"
   ```

### <a name="2-provide-a-production-ready-sql-database-for-our-application"></a>2. Fournir une base de données SQL prête pour la production pour notre application
Quand nous avons placé cette application dans un conteneur et activé le débogage local, nous l’avons paramétrée pour exécuter SQL Server dans un conteneur. Cette approche est une bonne solution quand vous déboguez votre application localement, dans la mesure où il n’est pas nécessaire que les données de la base de données soient rendues persistantes. Cependant, dans un environnement de production, les données doivent être rendues persistantes dans la base de données. Comme il n’existe aucun moyen de garantir la persistance des données dans un conteneur, vous ne pouvez pas stocker les données de production dans SQL Server dans un conteneur.

Ainsi, si votre service requiert une base de données SQL persistante, nous vous recommandons d’utiliser une base de données SQL Azure. Pour configurer et exécuter un serveur SQL managé dans Azure, consultez l’article [Azure SQL Database Quickstarts][[link-azure-sql]] (Démarrages rapides pour une base de données SQL Azure).

>[!NOTE]
>N’oubliez pas de modifier les chaînes de connexion au serveur SQL dans le fichier web.release.config dans le projet FabrikamFiber.Web.
>Cette application échoue normalement si aucune base de données SQL n’est accessible. Vous pouvez choisir de poursuivre et de déployer l’application sans serveur SQL.

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
   
   Une fois la configuration terminée, votre conteneur est déployé sur Service Fabric. Chaque fois que vous placez des mises à jour dans le dépôt, de nouvelles build et version Release sont exécutées.
   
   >[!NOTE]
   >La génération des images du conteneur prend environ 15 minutes.
   >Le premier déploiement sur le cluster Service Fabric entraîne le téléchargement des images du conteneur Windows Server Core de base. Le téléchargement prend 5 à 10 minutes supplémentaires.

7. Accédez à l’application Fabrikam Call Center à l’aide de l’url de votre cluster : par exemple, *http://mycluster.westeurope.cloudapp.azure.com*

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
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /sql-database

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
