---
title: Cluster Azure Kubernetes pour Windows | Microsoft Docs
description: "Déploiement et prise en main d’un cluster Kubernetes pour des conteneurs Windows dans Azure Container Service"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: dlepow
translationtype: Human Translation
ms.sourcegitcommit: 31aaa122bfca5140dcd22d2a2233c46cd28f27b9
ms.openlocfilehash: c139fc34d15545ce6a7a91842a3ebdff7c029a01
ms.lasthandoff: 02/23/2017


---

# <a name="get-started-with-windows-containers-in-a-kubernetes-cluster"></a>Prise en main de conteneurs Windows dans un cluster Kubernetes


Cet article montre comment créer un cluster Kubernetes dans Azure conteneur de Service qui contient les nœuds Windows pour exécuter des conteneurs Windows. 

> [!NOTE]
> Le support des conteneurs Windows avec Kubernetes est en version préliminaire. Utilisez le portail Azure ou un modèle Resource Manager pour créer un cluster Kubernetes avec des nœuds Windows. Cette fonctionnalité n’est pas prise en charge actuellement avec Azure CLI 2.0.



L’illustration suivante montre l’architecture de cluster Kubernetes dans Azure Container Service avec un seul nœud maître Linux et deux nœuds d’agent Windows. 

* Le maître Linux fait office d’API REST Kubernetes et est accessible par SSH sur le port 22 ou `kubectl` sur le port 443. 
* Les nœuds d’agent Windows sont rassemblés dans un groupe à haute disponibilité Azure et exécutent vos conteneurs. Les nœuds Windows sont accessibles par le biais d’un tunnel SSH RDP via le nœud principal. Les règles d’équilibreurs de charge Azure sont ajoutées dynamiquement au cluster en fonction des services exposés.


![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

Toutes les machines virtuelles figurent dans le même réseau privé virtuel et sont entièrement accessibles les unes par rapport aux autres. Toutes les machines virtuelles exécutant un kubelet, un Docker et un Proxy.

## <a name="prerequisites"></a>Composants requis


* **Clé publique SSH RSA** : lorsque vous déployez par le biais du portail ou de l’un des modèles de démarrage rapide Azure, vous devez fournir une clé publique SSH RSA pour l’authentification sur les machines virtuelles d’Azure Container Service. Pour créer des clés Secure Shell (SSH) RSA, consultez les conseils [OS X et Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **ID client principal du service et clé secrète** : pour plus d’informations et des conseils, consultez [À propos du principal du service pour un cluster Kubernetes](container-service-kubernetes-service-principal.md).




## <a name="create-the-cluster"></a>Création du cluster

Vous pouvez utiliser le portail Azure pour [créer un cluster Kubernetes](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal) avec des nœuds d’agent Windows. Notez les paramètres suivants lors de la création du cluster :

* Dans le panneau **Fonctions de base**,dans **Orchestrator**, sélectionnez **Kubernetes**. 
* Dans le panneau de **configuration du maître**, entrez les informations d’identification utilisateur et du principal du service des nœuds Linux principaux Linux.
* Dans le panneau**Configuration de l’agent**, sous **Système d’exploitation**, sélectionnez **Windows (version préliminaire)**. Entrez les informations d’identification d’administrateur pour les nœuds d’agent Windows.

Pour en savoir plus, consultez la rubrique[Déploiement d’un cluster Azure Container Service](container-service-deployment.md).

## <a name="connect-to-the-cluster"></a>Connexion au cluster

Utilisez l’outil de ligne de commande `kubectl` pour vous connecter à partir de votre ordinateur local au nœud principal du cluster Kubernetes. Pour en savoir plus sur les étapes d’installation et de configuration de `kubectl`, consultez [Connexion à un cluster Azure Container Service](container-service-connect.md#connect-to-a-kubernetes-cluster). Vous pouvez utiliser des commandes `kubectl` pour accéder à l’interface utilisateur web de Kubernetes et pour créer et gérer des charges de travail de conteneur Windows.

## <a name="create-your-first-kubernetes-service"></a>Création de votre premier service Kubernetes

Après avoir créé le cluster et l’avoir connecté à `kubectl`, vous pouvez essayer de démarrer une application web Windows de base et l’exposer à internet. Dans cet exemple, vous spécifiez les ressources de conteneur à l’aide d’un fichier YAML et utilisez ensuite `kubctl apply` pour le créer.

1. Pour afficher une liste de vos nœuds, saisissez `kubectl get nodes`. Si vous souhaitez obtenir tous les détails sur les nœuds, saisissez :  

  ```
  kubectl get nodes -o yaml
  ```

2. Créez un fichier nommé `simpleweb.yaml` et copiez le contenu suivant. Ce fichier configure une application web à l’aide de l’image du système d’exploitation Windows Server 2016 Server Core de base à partir de [Docker Hub](https://hub.docker.com/r/microsoft/windowsservercore/).  

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$ip = (Get-NetIPAddress | where {$$_.IPAddress -Like '*.*.*.*'})[0].IPAddress ; $$url = 'http://'+$$ip+':80/' ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add($$url) ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at {0}...' -f $$url) ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

3. Pour lancer l’application, saisissez :

  ```
  kubectl apply -f simpleweb.yaml
  ```
  
  > [!NOTE] 
  > La configuration comprend `type: LoadBalancer`. Ce paramètre entraîne une exposition à internet du service par le biais d’un équilibreur de charge Azure. Pour en savoir plus, consultez la rubrique [Équilibrer la charge des conteneurs dans un cluster Kubernetes dans Azure Container Service](container-service-kubernetes-load-balancing.md).
  
4. Pour vérifier le déploiement du service (l’opération prend environ 30 secondes), saisissez :

  ```
  kubectl get pods
  ```

5. Une fois que le service est en cours d’exécution, pour voir les adresses IP internes et externes du service, saisissez :

  ```
  kubectl get svc
  ``` 

  ![Adresses IP du service Windows](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

  L’ajout de l’adresse IP externe prend plusieurs minutes. Avant que l’équilibreur de charge configure l’adresse externe, il apparaît comme `<pending>`.


6. Une fois que l’adresse IP externe est disponible, vous pouvez accéder au service dans votre navigateur web.

  ![Application de serveur Windows dans le navigateur](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Accès aux nœuds Windows
Les nœuds Windows sont accessibles à partir d’un ordinateur Windows local par le biais d’une connexion Bureau à distance. Nous vous recommandons d’utiliser un tunnel SSH RDP par le biais du nœud principal. 

Il existe plusieurs options de création des tunnels SSH sous Windows. Cette section décrit comment utiliser PuTTY pour créer le tunnel.

1. [Téléchargez PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) sur votre système Windows.

2. Exécutez l'application.

3. Saisissez un nom d’hôte constitué du nom d’utilisateur administrateur des clusters et du nom DNS public du premier serveur principal du cluster. Le **nom d’hôte** est similaire à `adminuser@PublicDNSName`. Spécifiez le **port**22.

    ![Configuration PuTTY 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. Sélectionnez **SSH > Auth**. Ajoutez un chemin d’accès à votre fichier de clé privée (format .ppk) pour l’authentification. Vous pouvez utiliser un outil tel que [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) pour générer ce fichier à partir de la clé SSH utilisée pour créer le cluster.

    ![Configuration PuTTY 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. Sélectionnez **SSH > Tunnels** et configurez les ports transférés. Étant donné que votre machine Windows locale utilise déjà le port 3389, il est recommandé d’utiliser les paramètres suivants pour accéder aux nœuds Windows 0 et Windows 1 (continuez ce modèle pour des nœuds Windows supplémentaires).

  **Nœud Windows 0**

  * **Port source :** 3390
  * **Destination:** 10.240.245.5:3389

  **Nœud Windows 1**

  * **Port source :** 3391
  * **Destination:** 10.240.245.6:3389

  ![Image des tunnels de RDP Windows](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. Quand vous avez terminé, cliquez sur **Session > Enregistrer** pour enregistrer la configuration de la connexion.

7. Pour vous connecter à la session PuTTY, cliquez sur **Ouvrir**. Établissez la connexion au nœud principal.

8. Lancez la connexion Bureau à distance. Pour se connecter au premier nœud Windows, pour **Ordinateur**, spécifiez `localhost:3390`, puis cliquez sur **Se connecter** (pour vous connecter au second nœud, spécifiez `localhost:3390`, et ainsi de suite). Pour finaliser votre connexion, fournissez le mot de passe administrateur Windows local que vous avez configuré lors du déploiement.








## <a name="next-steps"></a>Étapes suivantes

Voici quelques liens recommandés pour en savoir plus sur Kubernetes :

* [Camp d’entraînement Kubernetes](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) : montre comment déployer, mettre à l’échelle, mettre à jour et déboguer des applications.
* [Guide d’utilisateur Kubernetes](http://kubernetes.io/docs/user-guide/) : fournit des informations sur les programmes en cours d’exécution dans un cluster Kubernetes existant.
* [Exemples de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) : fournit des exemples d’exécution d’applications réelles avec Kubernetes.
