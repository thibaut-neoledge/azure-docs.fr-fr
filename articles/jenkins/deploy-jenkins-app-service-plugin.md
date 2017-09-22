---
title: "Déployer sur Azure App Service avec le plug-in Jenkins | Microsoft Docs"
description: "Découvrez comment utiliser le plug-in Azure App Service Jenkins pour déployer une application web Java sur Azure dans Jenkins."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: bd4e629e522fb9acea5601be8eac7c70ae61d042
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-plugin"></a>Déployer sur Azure App Service avec le plug-in Jenkins 
Pour déployer une application web Java sur Azure, vous pouvez utiliser Azure CLI dans [Jenkins Pipeline](/azure/jenkins/execute-cli-jenkins-pipeline) ou vous pouvez utiliser le [plug-in Azure App Service Jenkins](https://plugins.jenkins.io/azure-app-service). Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer Jenkins pour déployer sur Azure App Service via FTP 
> * Configurer Jenkins pour déployer sur Azure App Service sur Linux par le biais de Docker 

## <a name="create-and-configure-jenkins-instance"></a>Créer et configurer l’instance Jenkins
Si vous ne disposez pas encore d’un serveur maître Jenkins, commencez par utiliser le [modèle de solution](install-jenkins-solution-template.md), qui inclut JDK8 et les plug-ins requis suivants :

* [Plug-in du client Git Jenkins](https://plugins.jenkins.io/git-client) v.2.4.6 
* [Plug-in Docker Commons](https://plugins.jenkins.io/docker-commons) v.1.4.0
* [Informations d’identification Azure](https://plugins.jenkins.io/azure-credentials) v.1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-server) v.0.1

Vous pouvez utiliser le plug-in App Service pour déployer l’application web dans tous les langages (par exemple, C#, PHP, Java et node.js, etc.) pris en charge par Azure App Service. Dans ce didacticiel, nous utilisons l’exemple d’application Java, [Application web Java simple pour Azure](https://github.com/azure-devops/javawebappsample). Pour répliquer le dépôt sur votre propre compte GitHub, cliquez sur le bouton **Bifurcation** en haut à droite.  

Java JDK et Maven sont obligatoires pour la génération du projet Java. Veillez à installer les composants sur le serveur maître Jenkins ou l’agent de la machine virtuelle si vous en utilisez un à des fins d’intégration continue. 

Pour les installer, connectez-vous à l’instance Jenkins à l’aide du protocole SSH et exécutez les commandes suivantes :

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Pour le déploiement sur App Service sur Linux, vous devez également installer Docker sur le serveur maître Jenkins ou l’agent de la machine virtuelle que vous utilisez pour la build. Reportez-vous à cet article pour installer Docker : https://docs.docker.com/engine/installation/linux/ubuntu/.

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Ajout du principal de service Azure aux informations d’identification Jenkins

Un principal de service Azure est nécessaire pour les déploiements sur Azure. 

<ol>
<li>Utiliser [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) ou le [portail Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal) pour créer un principal de service Azure</li>
<li>Dans le tableau de bord Jenkins, cliquez sur **Informations d’identification > Système**. Cliquez sur **Informations d’identification globales (sans restriction)**.</li>
<li>Cliquez sur **Ajouter des informations d’identification** pour ajouter un principal de service Microsoft Azure en renseignant les valeurs suivantes : ID d’abonnement, ID de client, secret client et point de terminaison de jeton OAuth 2.0. Fournissez l’ID, **mySp**, à utiliser dans les étapes ultérieures.</li>
</ol>

## <a name="azure-app-service-plugin"></a>Plug-in Azure App Service

Le plug-in Azure App Service v1.0 prend en charge le déploiement continu sur Azure Web App via :

* Git et FTP
* Docker pour application web sur Linux

## <a name="configure-jenkins-to-deploy-web-app-through-ftp-using-the-jenkins-dashboard"></a>Configurer Jenkins pour déployer une application web via FTP à l’aide du tableau de bord Jenkins

Pour déployer votre projet sur Azure Web App, vous pouvez charger vos artefacts de build (par exemple, un fichier .war en Java) à l’aide de Git ou FTP.

Avant de configurer le travail dans Jenkins, vous avez besoin d’un plan Azure App Service et d’une application web pour l’exécution de l’application Java.


1. Créez un plan Azure App Service avec le niveau tarifaire **Gratuit** à l’aide de la commande d’interface de ligne de commande [az appservice plan create](/cli/azure/appservice/plan#create). Le plan App Service définit les ressources physiques utilisées pour héberger vos applications. Toutes les applications affectées à un plan App Service partagent ces ressources, ce qui vous permet de réduire les coûts lors de l’hébergement de plusieurs applications.
2. Créez une application web. Vous pouvez utiliser le [portail Azure](/azure/app-service/web-sites-configure) ou la commande CLI Az suivante :
```azurecli-interactive 
az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
```

3. Vérifiez que vous définissez la configuration du runtime Java dont votre application a besoin. La commande Azure CLI suivante configure l’application web de manière à ce qu’elle s’exécute sur un JDK Java 8 récent et sur [Apache Tomcat](http://tomcat.apache.org/) 8.0.
```azurecli-interactive
az webapp config set \
--name <myAppName> \
--resource-group <myResourceGroup> \
--java-version 1.8 \
--java-container Tomcat \
--java-container-version 8.0
```

### <a name="set-up-the-jenkins-job"></a>Configurer la tâche Jenkins


1. Créer un nouveau projet **freestyle** dans le tableau de bord Jenkins
2. Configurez la **gestion du code source** de sorte à utiliser votre duplication locale de l’[Application web Java simple pour Azure](https://github.com/azure-devops/javawebappsample) en fournissant l’**URL du dépôt**. Par exemple : http://github.com/&lt;votreID>/javawebappsample.
3. Ajoutez une étape de génération pour générer le projet à l’aide de Maven. Pour ce faire, ajoutez une étape **Exécuter un interpréteur de commandes**. Pour cet exemple, nous avons besoin d’une étape supplémentaire pour renommer le fichier *.war dans le dossier cible et lui attribuer le nom ROOT.war.   
```bash
mvn clean package
mv target/*.war target/ROOT.war
```

4. Ajoutez une action post-build en sélectionnant **Publier une application web Azure**.
5. Indiquez, « mySp », le principal de service Azure stocké à l’étape précédente.
6. Dans **Configuration de l’application**, choisissez le groupe de ressources et l’application web compris dans votre abonnement. Le plug-in détecte automatiquement si l’application web est une application Windows ou Linux. Pour une application web basée sur Windows, l’option « Publier les fichiers » est présentée.
7. Renseignez les fichiers à déployer (par exemple, un package war si vous utilisez Java). Les répertoires source et cible sont facultatifs. Les paramètres vous permettent de spécifier les dossiers source et cible lors du chargement des fichiers. L’application web Java sur Azure est exécutée sur un serveur Tomcat. Ainsi, vous chargez votre package war dans le dossier webapps. Pour cet exemple, définissez le **répertoire source** sur « target » et indiquez « webapps » pour le **répertoire cible**.
8. Pour déployer sur un emplacement autre qu’un emplacement de production, vous pouvez également définir le nom **Slot**.
9. Enregistrez le projet et générez-le. Votre application web est déployée sur Azure quand la build est terminée.

### <a name="deploy-web-app-through-ftp-using-jenkins-pipeline"></a>Déployer l’application Web via FTP à l’aide du pipeline Jenkins

Le plug-in est compatible avec le pipeline. Vous pouvez vous référer à un exemple dans le dépôt GitHub.

1. Dans l’interface utilisateur web de GitHub, ouvrez le fichier **Jenkinsfile_ftp_plugin**. Cliquez sur l’icône du crayon pour modifier ce fichier et mettre à jour le groupe de ressources, ainsi que le nom de votre application web sur les lignes 11 et 12, respectivement.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Modifiez la ligne 14 pour mettre à jour l’ID des informations d’identification dans votre instance Jenkins.    
```java
withCredentials([azureServicePrincipal('<mySp>')]) {
```

### <a name="create-a-jenkins-pipeline"></a>Créer un pipeline Jenkins

1. Ouvrez Jenkins dans un navigateur web, cliquez sur **Nouvel élément**.
2. Fournissez un nom pour le travail et sélectionnez **Pipeline**. Cliquez sur **OK**.
3. Ensuite, cliquez sur l’onglet **Pipeline**.
4. Pour **Définition**, sélectionnez **Script de pipeline à partir de SCM**.
5. Pour **SCM**, sélectionnez **Git**. Entrez l’URL GitHub de votre dépôt dupliqué : https:&lt;votre dépôt dupliqué>.git
6. Remplacez le **chemin du script** par « Jenkinsfile_ftp_plugin ».
7. Cliquez sur **Enregistrer** et exécutez la tâche.

## <a name="configure-jenkins-to-deploy-web-app-on-linux-through-docker"></a>Configurer Jenkins pour déployer l’application web sur Linux via Docker

Outre Git/FTP, l’application web sur Linux prend en charge le déploiement à l’aide de Docker. Pour déployer à l’aide de Docker, vous devez fournir un fichier Docker qui intègre votre application web et le runtime du service dans une image Docker. Le plug-in crée ensuite l’image, l’insère dans un registre Docker et déploie l’image sur votre application web.

L’application web sur Linux prend également en charge les méthodes traditionnelles telles que Git et FTP, mais uniquement pour les langages intégrés (.NET Core, Node.js, PHP et Ruby). Pour les autres langages, vous devez empaqueter le code de votre application et le runtime du service dans une image Docker et utiliser Docker pour le déploiement.

Avant de configurer la tâche dans Jenkins, vous avez besoin d’un service d’application Azure sur Linux. Un registre de conteneurs est également nécessaire pour stocker et gérer vos images conteneurs Docker privées. Vous pouvez utiliser DockerHub ; nous utilisons Azure Container Registry pour cet exemple.

* Vous pouvez suivre les étapes indiquées [ici](../app-service/containers/quickstart-nodejs.md) pour créer une application web sur Linux. 
* Azure Container Registry est un service [de registre Docker] géré (https://docs.docker.com/registry/) basé sur le registre open source Docker 2.0. Suivez les étapes indiquées [ici] (/azure/container-registry/container-registry-get-started-azure-cli) pour plus d’informations sur la marche à suivre. Vous pouvez également utiliser DockerHub.

### <a name="to-deploy-using-docker"></a>Pour déployer à l’aide de Docker :

1. Créez un nouveau projet freestyle dans le tableau de bord Jenkins.
2. Configurez la **gestion du code source** de sorte à utiliser votre duplication locale de l’[Application web Java simple pour Azure](https://github.com/azure-devops/javawebappsample) en fournissant l’**URL du dépôt**. Par exemple : http://github.com/&lt;votre_id>/javawebappsample.
Ajoutez une étape de génération pour générer le projet à l’aide de Maven. Pour cela, ajoutez une étape **Exécuter un interpréteur de commandes** et ajoutez la ligne suivante dans **Commande** :    
```bash
mvn clean package
```

3. Ajoutez une action post-build en sélectionnant **Publier une application web Azure**.
4. Indiquez, **mySp**, le principal de service Azure stocké à l’étape précédente, comme informations d’identification Azure.
5. Dans la section **Configuration de l’application**, choisissez le groupe de ressources et une application web Linux compris dans votre abonnement.
6. Choisissez Publier via Docker.
7. Renseignez le chemin **Dockerfile**. Vous pouvez conserver la valeur par défaut « /Dockerfile » pour l’**URL du registre Docker** fournie au format https://&lt;monRegistre>.azurecr.io si vous utilisez Azure Container Registry. Laissez ce champ vide si vous utilisez DockerHub.
8. Dans le champ **Informations d’identification du registre**, ajoutez celles d’Azure Container Registry. Vous pouvez obtenir l’ID utilisateur et un mot de passe en exécutant les commandes suivantes dans Azure CLI. La première commande active le compte d’administrateur.    
```azurecli-interactive
az acr update -n <yourRegistry> --admin-enabled true
az acr credential show -n <yourRegistry>
```

9. Le nom et la balise de l’image Docker sous l’onglet **Avancé** sont facultatifs. Par défaut, le nom de l’image est obtenue à partir du nom de l’image que vous avez configuré dans le portail Azure (paramètre Conteneur Docker.) La balise est générée à partir de $BUILD_NUMBER. Vérifiez que vous spécifiez le nom de l’image dans le portail Azure ou fournissez une valeur pour **Image Docker** sous l’onglet **Avancé**. Pour cet exemple, indiquez « &lt;votreRegistre>.azurecr.io/calculator » pour **Image Docker** et ne renseignez pas **Balise d’image Docker**.
10. Notez que le déploiement échoue si vous utilisez le paramètre Image Docker prédéfini. Veillez bien à modifier la configuration Docker pour utiliser l’image personnalisée dans le paramètre Conteneur Docker dans le portail Azure. Pour l’image prédéfinie, utilisez une approche de déploiement par chargement de fichier.
11. À l’instar de l’approche par chargement de fichier, vous pouvez choisir un emplacement autre qu’un emplacement de production.
12. Enregistrez et générez le projet. Vous constatez que votre image conteneur est placée dans votre registre et que l’application web est déployée.

### <a name="deploy-to-web-app-on-linux-through-docker-using-jenkins-pipeline"></a>Déployer sur une application web sur Linux via Docker en utilisant le pipeline Jenkins

1. Dans l’interface utilisateur web de GitHub, ouvrez le fichier **Jenkinsfile_container_plugin**. Cliquez sur l’icône du crayon pour modifier ce fichier et mettre à jour le groupe de ressources, ainsi que le nom de votre application web sur les lignes 11 et 12, respectivement.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Remplacer la ligne 13 par votre serveur de registre de conteneurs    
```java
def registryServer = '<registryURL>'
```    

3. Remplacer la ligne 16 pour mettre à jour l’ID des informations d’identification dans votre instance Jenkins    
```java
azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
```    
### <a name="create-jenkins-pipeline"></a>Créer un pipeline Jenkins    

1. Ouvrez Jenkins dans un navigateur web, cliquez sur **Nouvel élément**.
2. Fournissez un nom pour le travail et sélectionnez **Pipeline**. Cliquez sur **OK**.
3. Ensuite, cliquez sur l’onglet **Pipeline**.
4. Pour **Définition**, sélectionnez **Script de pipeline à partir de SCM**.
5. Pour **SCM**, sélectionnez **Git**.
6. Entrez l’URL GitHub de votre dépôt dupliqué : https:&lt;votre dépôt dupliqué>.git</li>
7, Mettez à jour le **chemin du script** en indiquant « Jenkinsfile_container_plugin ».
8. Cliquez sur **Enregistrer** et exécutez la tâche.

## <a name="verify-your-web-app"></a>Vérifier votre application web

1. Pour vérifier le déploiement du fichier WAR dans votre application web. Ouvrez un navigateur web.
2. Accédez à http://&lt;nom_application>.azurewebsites.net/api/calculator/ping. Vous voyez :    
     Bienvenue dans l’application web Java ! La mise à jour est effectuée !
   Sun Jun 17 16:39:10 UTC 2017
3. Accédez à http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (remplacez &lt;x> et &lt;y> par n’importe quels chiffres) pour obtenir la somme de x et de y        
    ![Calculatrice : ajouter](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-app-service-on-linux"></a>Pour App Service sur Linux

* Pour vérifier, dans l’interface CLI Azure, exécutez :

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Vous obtenez le résultat suivant :
    
    ```
    [
    "calculator"
    ]
    ```
    
    Accédez à http://&lt;app_name>.azurewebsites.net/api/calculator/ping. Le message suivant s’affiche : 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Accédez à http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (remplacez &lt;x> et &lt;y> par n’importe quels chiffres) pour obtenir la somme de x et de y
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous utilisez le plug-in Azure App Service pour effectuer le déploiement sur Azure.

Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer Jenkins pour déployer Azure App Service via FTP 
> * Configurer Jenkins pour déployer sur Azure App Service sur Linux par le biais de Docker 
