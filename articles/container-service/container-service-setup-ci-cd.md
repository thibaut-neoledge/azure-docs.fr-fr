---
title: CI/CD avec Azure Container Service et DC/OS | Microsoft Docs
description: "Comment automatiser de bout en bout la création et le déploiement d’une application Docker à conteneurs multiples sur un cluster Azure Container Service exécutant DC/OS."
services: container-service
documentationcenter: 
author: spboyer
manager: wpickett
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Mesos, Azure
ms.assetid: b16b767a-2eaa-418a-becc-8c032713a1f2
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: johnsta
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: acfba48585b960a1ce39b77e35d292189aa9732b
ms.lasthandoff: 04/03/2017


---

# <a name="continuous-integration-and-deployment-of-multi-container-docker-applications-to-azure-container-service"></a>Intégration et déploiement continus d’applications Docker à conteneurs multiples sur Azure Container Service 
Ce didacticiel explique comment automatiser de bout en bout la création et le déploiement d’une application Docker à conteneurs multiples sur un cluster Azure Container Service exécutant DC/OS. Si les avantages de l’intégration et du déploiement continus (CI/CD) sont connus, de nouveaux éléments doivent être pris en compte lors de l’intégration des conteneurs dans votre workflow. À l’aide des nouvelles commandes Azure Container Registry et CLI, nous allons configurer un flux de bout en bout que vous pourrez personnaliser.

## <a name="get-started"></a>Prise en main
Vous pouvez exécuter cette procédure pas à pas sous OS X, Windows ou Linux.
- Vous avez besoin d’un abonnement Azure. Si vous n’en avez pas, vous pouvez vous [inscrire pour obtenir un compte](https://azure.microsoft.com/).
- Installez [Azure CLI 2.0](/cli/azure/install-az-cli2).

## <a name="what-well-create"></a>Ce que nous allons créer
Voici certains aspects clés de l’application et de son flux de déploiement que nous allons configurer :
* **L’application est composée de plusieurs services**. Les ressources Docker, les fichiers Docker et le fichier de configuration docker-compose.yml définissent les services de notre application, chacun étant exécuté dans des conteneurs distincts. Ces outils permettent de mettre à l’échelle les différentes parties de l’application de manière indépendante. Chaque service peut être écrit dans un langage de programmation et une infrastructure spécifiques. Le code de l’application peut être hébergé dans un ou plusieurs référentiels Git source (les outils prennent actuellement en charge GitHub et Visual Studio Team Services).

* L’application s’exécute dans un **cluster ACS configuré avec DC/OS**. L’orchestrateur de conteneurs peut gérer l’intégrité de notre cluster et vérifier que le nombre requis d’instances de conteneur continuent à s’exécuter. 

* Le processus de **création et de déploiement des images de conteneur est entièrement automatisé et n’occasionne aucun temps d’arrêt**. Nous demandons aux développeurs de l’équipe d’effectuer une opération Push Git vers une branche pour déclencher automatiquement le processus d’intégration. Autrement dit, nous créons et marquons les images de conteneur, nous exécutons des tests sur chaque conteneur, puis nous envoyons ces images vers un registre Docker privé. À partir de là, les nouvelles images sont automatiquement déployées dans un environnement de pré-production partagé sur un cluster ACS à des fins de test.

* Nous allons à présent **faire passer une version d’un environnement à l’autre**, par exemple Dev -> Test -> Intermédiaire -> Production. Chaque fois que nous passons à un environnement en aval, nous n’aurons pas besoin de recréer nos images de conteneur pour être sûrs de déployer les mêmes images qui ont été testées dans l’environnement précédent. Ce processus repose sur le concept de *services immuables* et réduit le risque de propagation d’erreurs non détectées dans l’environnement de production.

* Pour utiliser plus efficacement les ressources de calcul dans notre cluster ACS, nous allons utiliser le même cluster pour exécuter les tâches de génération qui conteneurisent l’ensemble des étapes de génération et de déploiement. Le cluster héberge également nos différents environnements de développement/test/production.


## <a name="create-an-azure-container-service-cluster-configured-with-dcos"></a>Créer un cluster Azure Container Service configuré avec DC/OS

>[!IMPORTANT]
> Pour créer un cluster sécurisé, transmettez le fichier de clés publiques SSH requis lorsque vous appelez `az acs create`. Vous pouvez utiliser Azure CLI 2.0 pour générer et transmettre les clés en utilisant l’option `--generate-ssh-keys`, ou renseigner le chemin d’accès à vos clés en utilisant l’option `--ssh-key-value` (l’emplacement par défaut est `~/.ssh/id_rsa.pub` sous Linux et `%HOMEPATH%\.ssh\id_rsa.pub` sous Windows, mais vous pouvez le modifier).
<!---Loc Comment: What do you mean by "you pass your SSH public key file to pass"? Thank you.--->
> Pour créer des fichiers de clés SSH publiques et privées sous Linux, consultez l’article [Créer des clés SSH sur Linux et Mac](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fcontainer-services%2ftoc.json). 
> Pour créer des fichiers de clés SSH publiques et privées sous Windows, consultez l’article [Créer des clés SSH sur Windows](../virtual-machines/linux/ssh-from-windows.md?toc=%2fazure%2fcontainer-services%2ftoc.json). 

1. Tout d’abord, saisissez la commande [az login](/cli/azure/#login) dans une fenêtre de terminal pour vous connecter à votre abonnement Azure avec Azure CLI : 

    `az login`

1. Créez un groupe de ressources dans lequel vous ajoutez le cluster à l’aide de la commande [az group create](/cli/azure/group#create) :
    
    `az group create --name myacs-rg --location westus`

    Vous pouvez indiquer la [région de centre de données Azure](https://azure.microsoft.com/regions) la plus proche de vous. 

1. Créez un cluster ACS avec les paramètres par défaut en utilisant la commande [az acs create](/cli/azure/acs#create) et en transmettant le chemin d’accès à votre fichier de clés publiques SSH : 

    ```azurecli
    az acs create \
    --resource-group myacs-rg 
    --name myacs \
    --dns-prefix myacs \
    --ssh-key-value ~/.ssh/id_rsa.pub
    ```
    
Cette étape prend quelques minutes, alors n’hésitez pas à continuer votre lecture.  La commande `acs create` renvoie des informations sur le cluster que vous venez de créer. Vous pouvez également afficher la liste des clusters ACS dans votre abonnement en utilisant la commande `az acs list`. Pour découvrir les autres options de configuration d’ACS, [reportez-vous à l’article sur la création et la configuration d’un cluster ACS](container-service-deployment.md).

## <a name="set-up-sample-code"></a>Configurer un exemple de code
Lors de la création du cluster, vous pouvez configurer un exemple de code à déployer dans ACS.

1. [Répliquez](https://help.github.com/articles/fork-a-repo/) l’exemple de référentiel GitHub afin d’avoir votre propre copie : [https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git](https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git). L’application est essentiellement une version à conteneurs multiples de « Hello World ».
1. Une fois que vous avez créé une copie répliquée dans votre propre compte GitHub, clonez localement le référentiel sur votre ordinateur :

    ```bash
    git clone  https://github.com/your-github-account/container-service-dotnet-continuous-integration-multi-container.git
    cd container-service-dotnet-continuous-integration-multi-container
    ```
    
Examinons le code de plus près :
* `/service-a` est une application web basée sur Angular.js avec un serveur principal Node.js.
* `/service-b` est un service .NET Core appelé par `service-a` via l’API REST.
* `service-a` et `service-b` contiennent tous deux un fichier Docker `Dockerfile` dans chacun de leurs répertoires qui décrivent, respectivement, les images de conteneur Node.js et .NET Core. 
* `docker-compose.yml` définit l’ensemble des services qui sont générés et déployés.
* Outre `service-a` et `service-b`, un troisième service nommé `cache` exécute un cache Redis que peut utiliser `service-a`. Le service `cache` diffère des deux premiers services dans la mesure où aucun code ne lui est associé dans notre référentiel source. Au lieu de cela, nous extrayons une image `redis:alpine` prédéfinie à partir de Docker Hub et la déployons vers ACS.
* `/service-a/server.js` contient du code où `service-a` appelle à la fois `service-b` et `cache`. Notez que le code `service-a` fait référence à `service-b` et `cache` en utilisant leur nom respectif dans `docker-compose.yml`. Si nous exécutons ces services sur notre ordinateur local via `docker-compose`, Docker assure la bonne mise en réseau des services qui peuvent se trouver mutuellement grâce à leur nom. Exécuter des services dans un environnement de cluster dont la mise en réseau est soumise à l’équilibrage de charge est bien plus complexe que d’exécuter ces services localement. La bonne nouvelle, c’est que les commandes d’interface de ligne de commande Azure configurent un flux CI/CD pour s’assurer que ce code simple de détection de service continue à s’exécuter tel quel dans ACS. 

    ![Présentation de l’exemple d’application à conteneurs multiples](media/container-service-setup-ci-cd/multi-container-sample-app-overview.PNG)

## <a name="set-up-continuous-integration-and-deployment"></a>Configurer une intégration et un déploiement continus
1. Assurez-vous que le cluster ACS est prêt : exécutez [az acs list](/cli/azure/acs#list) et vérifiez que votre cluster ACS est répertorié. (Remarque : ACS doit exécuter une version DC/OS 1.8 ou ultérieure.)

1. [Créez un jeton d’accès personnel GitHub](https://help.github.com/articles/creating-an-access-token-for-command-line-use/) en lui attribuant au minimum l’étendue `repo`.  N’oubliez pas de copier le jeton dans votre Presse-papiers, car vous en aurez besoin dans la prochaine commande (un [webhook](https://help.github.com/articles/about-webhooks/) sera configuré dans le référentiel GitHub). 

1. Configurez votre répertoire actuel à la racine de votre référentiel source cloné et créez un pipeline de génération et de mise en production à l’aide du jeton _&lt;GitHubPersonalAccessToken&gt; que vous venez de créer :
    
    `cd container-service-dotnet-continuous-integration-multi-container`

    ```azurecli
    az container release create \
    --target-name myacs \
    --target-resource-group myacs-rg \
    --remote-access-token <GitHubPersonalAccessToken>
    ```

    Où `--target-name` est le nom de votre cluster ACS, et `--target-resource-group` le nom du groupe de ressources du cluster ACS.

La première exécution de cette commande peut prendre environ une minute. Une fois la commande exécutée, des informations importantes sont renvoyées à propos du pipeline de génération et de mise en production tout juste créé :
* `sourceRepo` : un [webhook](https://help.github.com/articles/about-webhooks/) est configuré pour le référentiel source afin que le pipeline de génération et de mise en production se déclenche automatiquement à chaque fois qu’il reçoit du code source.  
* `vstsProject` : [Visual Studio Team Services](https://www.visualstudio.com/team-services/) (VSTS) est configuré pour *exécuter* le workflow (les tâches effectives de génération et de déploiement s’exécutent au sein des conteneurs dans ACS). Si vous souhaitez utiliser un projet et un compte VSTS spécifiques, vous pouvez les configurer à l’aide des paramètres `--vsts-account-name` et `--vsts-project-name`.
* `buildDefinition` : définit les tâches qui s’exécutent pour chaque génération. Les images de conteneur sont générées pour chaque service défini dans le fichier docker-compose.yml, puis envoyées dans un registre de conteneur Docker. 
* `containerRegistry` : Azure Container Registry est un service géré qui exécute un registre de conteneur Docker. Un nouveau registre de conteneur Azure est créé avec un nom par défaut. Vous pouvez également définir vous-même le nom du registre via le paramètre `--registry-name`.
* `releaseDefinition` : définit les tâches qui sont exécutées pour chaque déploiement. Les images de conteneur associées aux services définis dans le fichier docker-compose.yml sont extraites du registre de conteneur et déployées dans le cluster ACS. Trois environnements sont créés par défaut : *Dev*, *Test* et *Production*. La définition de version est configurée par défaut pour être automatiquement déployée dans l’environnement *Dev* chaque fois qu’une génération est effectuée avec succès. Une version peut être déployée manuellement dans l’environnement *Test* ou *Production* sans qu’il soit nécessaire d’effectuer une nouvelle génération. Le flux par défaut peut être personnalisé dans VSTS. 
* `containerService` : le cluster ACS cible (doit exécuter DC/OS version 1.8).


L’extrait de code suivant est un exemple de commande que vous saisiriez si vous disposez déjà d’un registre de conteneur Azure nommé `myregistry`. Créez et générez des définitions de version à l’aide d’un compte VSTS à l’adresse `myvstsaccount.visualstudio.com` et d’un projet VSTS `myvstsproject` existant :
        
```azurecli
az container release create \
--target-name myacs \
--target-resource-group myacs-rg \
--registry-name myregistry \
--vsts-account-name myvstsaccount \
--vsts-project-name myvstsproject \
--remote-access-token <GitHubPersonalAccessToken>
```

## <a name="view-deployment-pipeline-progress"></a>Afficher la progression du pipeline de déploiement
Une fois que le pipeline est créé, une première opération de génération et de déploiement est automatiquement déclenchée. Les générations suivantes sont déclenchées à chaque nouvel envoi de code au référentiel source. Vous pouvez suivre la progression d’une génération et/ou d’une version en ouvrant l’URL de définition de build ou de version dans votre navigateur.

Pour trouver l’URL de définition de version associée à un cluster ACS, exécutez la commande suivante :

```azurecli
az container release list \
--target-name myacs \
--target-resource-group myacs-rg
``` 

![Build VSTS](media/container-service-setup-ci-cd/vsts-build.PNG)

*Capture d’écran de VSTS affichant les résultats de l’intégration continue (CI) de notre application à conteneurs multiples*

![Version VSTS](media/container-service-setup-ci-cd/vsts-release.PNG)

*Version docker-compose VSTS à environnements multiples*

## <a name="view-the-application"></a>Afficher l’application
À ce stade, l’application est déployée dans l’environnement de développement partagé et n’est pas exposée publiquement. En attendant, utilisez le tableau de bord DC/OS pour afficher et gérer les services et [créez un tunnel SSH vers les points de terminaison associés à DC/OS](https://azure.microsoft.com/documentation/articles/container-service-connect/) ou exécutez une commande de commodité fournie par l’interface de ligne de commande Azure.

> [!IMPORTANT]
> S’il s’agit d’un premier déploiement, vérifiez que la version VSTS a bien été déployée avant de poursuivre.

> [!NOTE]
> Windows uniquement : vous devez configurer [Pageant](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) pour terminer cette étape.
> 
>* Lancez *PuttyGen* et chargez la clé SSH privée utilisée pour créer le cluster ACS (%homepath%\id_rsa).
>* Enregistrez la clé SSH privée en tant que `id_rsa.ppk` dans le même dossier.
>* Lancez *Pageant* (l’outil démarrera et une icône s’affichera dans la barre d’état système située en bas à droite).
>* Cliquez avec le bouton droit sur l’icône de la barre d’état système et sélectionnez *Ajouter une clé*.
>* Ajoutez le fichier `id_rsa.ppk`.
> 
> 

1. Ouvrez le tableau de bord DC/OS du cluster ACS à l’aide de la commande de commodité d’interface de ligne de commande Azure suivante :
    
    `az acs dcos browse -g myacs-rg -n myacs`

    * `-g` est le nom du groupe de ressources du cluster ACS cible.
    * `-n` est le nom du cluster ACS cible.
    * Vous devrez peut-être saisir votre mot de passe de compte local, car cette commande requiert des privilèges d’administrateur. La commande crée un tunnel SSH vers un point de terminaison DC/OS, ouvre votre navigateur par défaut à l’adresse de ce point de terminaison et configure temporairement le proxy web du navigateur. 

    > [!TIP]
    > Pour trouver le nom de votre cluster ACS, vous pouvez afficher la liste de tous les clusters ACS dans votre abonnement en exécutant la commande `az acs list`. 

1. Dans le tableau de bord DC/OS, cliquez sur **Services** dans le menu de navigation de gauche ([http://localhost/#/services](http://localhost/#/services)). Les services déployés via notre pipeline sont regroupés dans un dossier racine nommé *dev* (qui correspond au nom de l’environnement dans la définition de version VSTS). 

![IU Marathon](media/container-service-setup-ci-cd/marathon-ui.png)

Le tableau de bord DC/OS offre de très nombreuses fonctionnalités :

* Suivi de l’état du déploiement pour chaque service
* Affichage de la configuration requise en termes de mémoire et de processeur
* Affichage des journaux
* Mise à l’échelle du nombre d’instances pour chaque service

**Pour afficher l’application web pour service-a** : accédez au dossier racine *dev*, puis parcourez l’arborescence du dossier jusqu’à `service-a`. Cette vue répertorie les tâches en cours d’exécution (ou les instances de conteneur) pour `service-a`.

![service a](media/container-service-setup-ci-cd/service-a.png)

Cliquez sur une tâche pour ouvrir la vue correspondante, puis sélectionnez l’un des points de terminaison associés disponibles.

![tâche service a](media/container-service-setup-ci-cd/service-a-task.PNG)

Notre application web simple appelle `service-a`, qui appelle `service-b`, et renvoie un message Hello World. Un compteur est incrémenté sur le cache Redis à chaque nouvelle requête.

![application web service a](media/container-service-setup-ci-cd/service-a-web-app.PNG)

### <a name="optional-reaching-a-service-from-the-command-line"></a>(Facultatif) Accéder à un service à partir de la ligne de commande
Pour accéder à un service via curl à partir de la ligne de commande, procédez comme suit :

1. Exécutez `az acs dcos browse --verbose -g myacs-rg -n myacs` et prenez note de la ligne affichant « Proxy running on <ip-address>:<port> » (Proxy s’exécutant sur <adresse-ip> :) après avoir saisi votre mot de passe.
1. Dans une nouvelle fenêtre de terminal, saisissez :

    `export http_proxy=http://<web-proxy-service-ip>:<portnumber>`

    Par exemple : `export http_proxy=http://127.0.0.1:55405`

1. Vous pouvez désormais lancer une requête curl `curl http://service-url` sur votre point de terminaison de service, où `service-url` est l’adresse qui s’affiche lorsque vous accédez à votre point de terminaison de service à partir de l’interface utilisateur Marathon. Pour désactiver la variable http_proxy depuis votre ligne de commande, saisissez `unset http_proxy`.
 

## <a name="scale-services"></a>Mettre à l’échelle les services
Profitons d’être dans le tableau de bord DC/OS pour mettre à l’échelle nos services.
1. Accédez à l’application dans le sous-dossier *dev*.
1. Passez la souris sur `service-b`, cliquez sur l’icône d’engrenage et sélectionnez **Mettre à l’échelle**.

    ![Menu Action](media/container-service-setup-ci-cd/marathon-ui-action-menu.PNG)

1. Définissez le nombre sur 3 et cliquez sur **Scale Service** (Mettre à l’échelle le service).

    ![Mettre à l’échelle les services](media/container-service-setup-ci-cd/marathon-ui-scale-service.png)

1. Accédez à nouveau à l’application web en cours d’exécution, puis cliquez à plusieurs reprises sur le bouton *Say It Again* (Répéter). Notez que les appels de `service-b` appliquent la méthode Tourniquet (round robin) sur un ensemble de noms d’hôte, tandis que l’instance unique de `service-a` indique toujours le même hôte.   

## <a name="promote-a-release-to-downstream-environments-without-rebuilding-container-images"></a>Faire passer une version dans des environnements en aval sans recréer les images de conteneur
Notre pipeline de mise en production VSTS configure trois environnements par défaut : *Dev*, *Test* et *Production*. Pour l’instant, nous avons déployé la version dans l’environnement *Dev*. Voyons comment procéder pour faire passer une version dans l’environnement en aval suivant (*Test*) sans recréer nos images de conteneur. Ce workflow nous permet de nous assurer que nous déployons exactement les mêmes images que celles testées dans l’environnement précédent. Il repose sur le concept de *services immuables* et réduit le risque de propagation d’erreurs non détectées dans l’environnement de production.

1. Dans l’interface utilisateur web VSTS, accédez à **Versions**.

    ![Menu Versions VSTS](media/container-service-setup-ci-cd/vsts-releases-menu.PNG)

1. Ouvrez la version la plus récente.

1. Dans la barre de menus de la définition de version, cliquez sur **Déployer**, puis définissez l’environnement de déploiement suivant sur **Test** pour démarrer un nouveau déploiement en utilisant les images précédemment déployées dans l’environnement *Dev*. Cliquez sur **Journaux** pour afficher les détails du déploiement.

    ![VSTS fait passer la version dans l’environnement suivant](media/container-service-setup-ci-cd/vsts-promote-release.PNG)

Une fois que la version a été déployée dans l’environnement *Test*, un nouveau dossier racine nommé *test* est créé dans l’interface utilisateur Marathon. Ce dossier contient les services en cours d’exécution pour cet environnement. 

![Sous-dossiers pour chaque environnement dans DC/OS](media/container-service-setup-ci-cd/marathon-ui-dev-test-environments.png)

## <a name="trigger-a-new-build-and-deployment"></a>Déclencher un nouveau workflow de génération et de déploiement
Voyons ce qu’il se passerait si un développeur de notre équipe envoyait une modification de code au référentiel source.

1. Dans l’éditeur de code, ouvrez `service-a/public/index.html`. 
1. Modifiez cette ligne de code :

    `<h2>Server Says</h2>`

    Par exemple :

    `<h2>Server Says Hello</h2>`

1. Enregistrez le fichier, puis validez et envoyez la modification de code à votre référentiel source.

    ```bash
    git commit -am 'updated title'
    git push
    ```

La validation génère automatiquement une nouvelle build, ainsi qu’une nouvelle version à déployer dans l’environnement *Dev*. Les services présents dans les environnements en aval (*Test* ou *Production*) restent inchangés jusqu’à ce que nous décidions de faire passer une version spécifique vers cet environnement.

Si vous ouvrez la définition de build dans VSTS, vous obtenez le résultat suivant : 

![Nouvelle génération déclenchée par opération Push Git](media/container-service-setup-ci-cd/new-build.png)



## <a name="expose-public-endpoint-for-production"></a>Exposer des points de terminaison publics pour la production

1. Ajoutez le code yaml suivant dans un nouveau fichier nommé `docker-compose.env.production.yml` dans le dossier racine de votre référentiel source. Le système ajoute une étiquette pour exposer un point de terminaison public pour `service-a`. 
    
    ```yaml
    version: "2"
    services:
      service-a:
        labels:
          com.microsoft.acs.dcos.marathon.vhost: "<FQDN, or custom domain>"
    ```

    * Définissez l’étiquette sur l’URL du nom de domaine complet de votre agent ACS ou sur un domaine personnalisé (par exemple, app.contoso.com). Pour connaître le nom de domaine complet de votre agent ACS, exécutez la commande `az acs list` et consultez la propriété `agentPoolProfiles.fqdn`. Par exemple, `myacsagents.westus.cloudapp.azure.com`.
    * L’exemple d’application par défaut est à l’écoute sur le port 80. Si votre application Docker est à l’écoute sur d’autres ports, par exemple `port 8080` ou `443`, attachez le numéro de port au nom de domaine complet. Par exemple, `myacsagents.westus.cloudapp.azure.com:8080`. Toutefois, lorsque vous tentez d’accéder à l’application depuis l’extérieur, vous devez exécuter des requêtes sur le port 80.
    * En suivant la convention de nom de fichier docker-compose.env.*environment-name*.yml, ces paramètres s’appliquent uniquement à l’environnement nommé (dans ce cas, l’environnement *Production*). Vérifiez la définition de version dans VSTS. La tâche de déploiement pour chaque environnement est configurée pour lire un fichier docker-compose nommé d’après cette convention.

1. Validez et envoyez le fichier à votre référentiel source principal pour démarrer une autre génération.

    ```bash
    git add .
    git commit -am "expose public port for service-a"
    git push
    ```

1. Attendez que la mise à jour soit générée et déployée dans l’environnement *Dev*, puis faites-la passer dans l’environnement *Test*, puis dans l’environnement *Production*. (Dans le cadre de ce didacticiel, vous pouvez la déployer directement dans l’environnement *Production*, mais il est préférable de prendre l’habitude de déployer uniquement vers l’environnement en aval suivant.)

1. (Facultatif) **Si vous avez spécifié un domaine personnalisé** pour vhost (par exemple, app.contoso.com), ajoutez un enregistrement DNS dans les paramètres du fournisseur de votre domaine. Connectez-vous à l’interface utilisateur d’administration du fournisseur de votre domaine et ajoutez un enregistrement DNS en procédant comme suit :

    * Type : CNAME
    * Hôte : votre domaine personnalisé, par exemple, app.contoso.com
    * Réponse : nom de domaine complet de l’agent ACS, par exemple, myacsagents.westus.cloudapp.azure.com
    * TTL (facultatif) : certains fournisseurs de domaine vous autorisent à modifier la durée de vie (TTL). Une valeur inférieure accélère la propagation de la mise à jour de l’enregistrement DNS.   

1. Une fois que la version a été déployée dans l’environnement *Production*, tout le monde peut y accéder. Ouvrez l’URL que vous avez spécifiée pour l’étiquette `com.microsoft.acs.dcos.marathon.vhost` dans votre navigateur. (Remarque : les versions pour les environnements de pré-production restent privées).

## <a name="summary"></a>Résumé
Félicitations ! Vous avez appris à créer un cluster ACS exécutant DC/OS et à configurer un pipeline de génération et de déploiement entièrement automatisé et en conteneur pour une application à conteneurs multiples.

Prochaines étapes :
* **Mettre à l’échelle les agents VSTS.** Si vous avez besoin de davantage de débit pour exécuter les tâches de génération et de validation, vous pouvez augmenter le nombre d’instances de l’agent VSTS. Accédez à **Services** dans le tableau de bord DS/OS, ouvrez le dossier vsts-agents et faites des tests en augmentant le nombre d’instances de l’agent VSTS.
* **Intégrer des tests unitaires.** Ce référentiel GitHub montre comment exécuter des tests unitaires et des tests d’intégration dans les conteneurs et les inclure dans les tâches de génération : [https://github.com/mindaro/sample-app](https://github.com/mindaro/sample-app). 
    * Astuce : reportez-vous à ces fichiers dans le référentiel : `service-a/unit-tests.js`, `service-a/service-tests.js`, `docker-compose.ci.unit-tests.yml` et `docker-compose.ci.service-tests.yml`.

## <a name="clean-up"></a>Nettoyer
Pour limiter les frais de calcul associés à ce didacticiel, exécutez la commande suivante et notez les ressources du pipeline de déploiement associées à un cluster ACS :

```azurecli    
az container release list --resource-name myacs --resource-group myacs-rg
```

Supprimez le cluster ACS :
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez le groupe de ressources qui contient votre cluster ACS.
1. Ouvrez l’interface utilisateur du panneau du groupe de ressources, puis cliquez sur **Supprimer** dans la barre de commandes du panneau.

Supprimez Azure Container Registry : dans le portail Azure, recherchez Azure Container Registry et supprimez-le. 

Le [compte Visual Studio Team Services fournit un niveau d’accès de base gratuit aux cinq premiers utilisateurs](https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/), mais vous pouvez supprimer les définitions de build et de version.

Supprimez la définition de build VSTS :
        
1. Ouvrez l’URL de définition de build dans votre navigateur, puis cliquez sur le lien **Définitions de build** (en regard du nom de la définition de build qui s’affiche).
2. Cliquez sur le menu Action en regard de la définition de build que vous souhaitez supprimer, puis sélectionnez **Supprimer la définition**.

`![Suppression de la définition de build VSTS](media/container-service-setup-ci-cd/vsts-delete-build-def.png) 

Supprimez la définition de version VSTS :

1. Ouvrez l’URL de la définition de version dans votre navigateur.
2. Dans la liste des définitions de version qui s’affiche à gauche, cliquez sur le menu déroulant en regard de la définition de version que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

`![Suppression de la définition de version VSTS](media/container-service-setup-ci-cd/vsts-delete-release-def.png)

