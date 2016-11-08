---
title: Solution Conteneurs dans Log Analytics | Microsoft Docs
description: La solution Conteneurs dans Log Analytics vous aide à afficher et gérer vos hôtes de conteneur Docker dans un emplacement unique.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="containers-(preview)-solution-log-analytics"></a>Solution Conteneurs (version préliminaire) Log Analytics
Cet article décrit comment configurer et utiliser la solution Conteneurs dans Log Analytics, vous aide à afficher et gérer vos hôtes de conteneur Docker dans un emplacement unique. Docker est un système de virtualisation logicielle utilisé pour créer des conteneurs qui automatisent le déploiement de logiciels dans leur infrastructure informatique.

La solution vous permet de voir les conteneurs exécutés sur les hôtes de votre conteneur, et les images exécutées dans les conteneurs. Vous pouvez afficher des informations d’audit détaillées montrant les commandes utilisées avec les conteneurs. Vous pouvez résoudre des problèmes de conteneurs en consultant des journaux centralisés et en y effectuant des recherches sans devoir afficher à distance les hôtes Docker. Vous pouvez rechercher des conteneurs bruyants et consommant des ressources excessives sur un ordinateur hôte. Et vous pouvez consulter des informations centralisées sur le processeur, la mémoire, le stockage ainsi que l’utilisation et les performances du réseau.

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

Ajoutez la solution Conteneurs à votre espace de travail OMS en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).

Il existe deux façons d’installer et d’utiliser Docker avec OMS :

* sur les systèmes d’exploitation Linux pris en charge, installer et exécuter Docker, puis installer et configurer l’Agent OMS pour Linux ;
* sur CoreOS, installer et exécuter Docker, puis configurer OMSAgent pour qu’il s’exécute à l’intérieur d’un conteneur.

Sur [GitHub](https://github.com/Microsoft/OMS-docker), passez en revue les versions de Docker et du système d’exploitation Linux prises en charge pour votre hôte de conteneur.

> [!IMPORTANT]
> Docker doit être en cours d’exécution **avant** l’installation de l’[Agent OMS pour Linux](log-analytics-linux-agents.md) sur vos hôtes de conteneur. Si vous avez déjà installé l’agent avant d’installer Docker, vous devez réinstaller l’Agent OMS pour Linux. Pour plus d’informations sur Docker, voir le [site web Docker](https://www.docker.com).
> 
> 

Pour pouvoir analyser les conteneurs, vous devez avoir préalablement configuré les paramètres suivants sur vos hôtes de conteneur.

## <a name="configure-settings-for-the-linux-container-host"></a>Configurer les paramètres pour l’hôte du conteneur Linux
Après avoir installé Docker, utilisez les paramètres suivants pour votre hôte de conteneur afin de configurer l’agent en vue d’une utilisation avec Docker. CoreOS ne prend pas en charge cette méthode de configuration.

### <a name="to-configure-settings-for-the-container-host---systemd-(suse,-opensuse,-centos-7.x,-rhel-7.x,-and-ubuntu-15.x-and-higher)"></a>Pour configurer les paramètres de l’hôte de conteneur - systemd (SUSE, openSUSE, CentOS 7.x, RHEL 7.x et Ubuntu 15.x et versions ultérieures)
1. Modifiez docker.service pour ajouter les éléments suivants :
   
    ```
    [Service]
    ...
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ...
    ```
2. Ajoutez $DOCKER\_OPTS dans &quot;ExecStart=/usr/bin/docker daemon&quot; dans votre fichier docker.service. Suivez l’exemple suivant.
   
    ```
    [Service]
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ExecStart=/usr/bin/docker daemon -H fd:// $DOCKER_OPTS
    ```
3. Redémarrez le service Docker. Par exemple : 
   
    ```
    sudo systemctl restart docker.service
    ```

### <a name="to-configure-settings-for-the-container-host---upstart-(ubuntu-14.x)"></a>Pour configurer les paramètres de l’hôte de conteneur - Upstart (Ubuntu 14.x)
1. Modifiez /etc/default/docker et ajoutez le code suivant :
   
    ```
    DOCKER_OPTS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```
2. Enregistrez le fichier, puis redémarrez les services Docker et OMS.
   
    ```
    sudo service docker restart
    ```

### <a name="to-configure-settings-for-the-container-host---amazon-linux"></a>Pour configurer les paramètres de l’hôte de conteneur - Amazon Linux
1. Modifiez /etc/sysconfig/docker et ajoutez le code suivant :
   
    ```
    OPTIONS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```
2. Enregistrez le fichier, puis redémarrez le service Docker.
   
    ```
    sudo service docker restart
    ```

## <a name="configure-settings-for-coreos-containers"></a>Configurer les paramètres pour des conteneurs CoreOS
Après avoir installé Docker, utilisez les paramètres suivants pour CoreOS afin d’exécuter Docker et de créer un conteneur. Avec cette méthode de configuration, vous pouvez utiliser n’importe quelle version prise en charge de Linux, donc CoreOS. Vous devez disposer des [ID et clé de votre espace de travail OMS](log-analytics-linux-agents.md).

### <a name="to-use-oms-for-all-containers-with-coreos"></a>Pour utiliser OMS pour tous les conteneurs avec CoreOS
* Démarrez le conteneur OMS que vous souhaitez analyser. Modifiez et utilisez l’exemple suivant.
  
  ```
  sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25224:25224/udp -p 127.0.0.1:25225:25225 --name="omsagent" --log-driver=none --restart=always microsoft/oms
  ```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>Passage de l’utilisation d’un agent installé à un agent dans un conteneur
Si vous utilisiez précédemment l’agent directement installé et souhaitez désormais utiliser à la place un agent qui s’exécute dans un conteneur, vous devez commencez par supprimer OMSAgent. Voir [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) (Étapes d’installation de l’Agent OMS pour Linux).

## <a name="containers-data-collection-details"></a>Détails sur la collecte de données des conteneurs
La solution Conteneurs collecte diverses mesures de performances et données de journaux à partir des hôtes de conteneur et des conteneurs utilisant les Agents OMS pour Linux que vous avez activés, et d’OMSAgent s’exécutant dans des conteneurs.

Le tableau suivant présente les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées pour la solution Conteneurs.

| plateforme | Agent OMS pour Linux | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![Oui](./media/log-analytics-containers/oms-bullet-green.png) |![Non](./media/log-analytics-containers/oms-bullet-red.png) |![Non](./media/log-analytics-containers/oms-bullet-red.png) |![Non](./media/log-analytics-containers/oms-bullet-red.png) |![Non](./media/log-analytics-containers/oms-bullet-red.png) |Toutes les 3 minutes. |

Le tableau suivant présente des exemples de types de données collectées par la solution Conteneurs :

| Type de données | Champs |
| --- | --- |
| Performances des hôtes et des conteneurs |Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Inventaire de conteneur |TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContinerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventaire d’image de conteneur |TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Journal de conteneur |TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Journal de service de conteneur |TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |

## <a name="monitor-containers"></a>Analyser les conteneurs
Une fois la solution activée dans le portail OMS, vous voyez la vignette **Conteneurs** affichant des informations récapitulatives sur vos hôtes de conteneur et les conteneurs s’exécutant dans les hôtes.

![Vignette Conteneurs](./media/log-analytics-containers/containers-title.png)

La vignette affiche une vue d’ensemble du nombre de conteneurs présents dans l’environnement, et indique s’ils sont en échec, en cours d’exécution ou arrêtés.

### <a name="using-the-containers-dashboard"></a>Utilisation du tableau de bord Conteneurs
Cliquez sur la vignette **Conteneurs**. À partir de là, vous voyez les vues organisées par :

* Événements de conteneur
* Erreurs
* État des conteneurs
* Inventaire d’image de conteneur
* Performances du processeur et de la mémoire

Chaque volet dans le tableau de bord est une représentation visuelle d’une recherche exécutée sur des données collectées.

![Tableau de bord Conteneurs](./media/log-analytics-containers/containers-dash01.png)

![Tableau de bord Conteneurs](./media/log-analytics-containers/containers-dash02.png)

Dans le panneau **État du conteneur**, cliquez pour la zone supérieure, comme illustré ci-dessous.

![État des conteneurs](./media/log-analytics-containers/containers-status.png)

La fenêtre Recherche de journal s’ouvre, affichant des informations sur les hôtes et les conteneurs en cours d’exécution en leur sein.

![Recherche de journal pour les conteneurs](./media/log-analytics-containers/containers-log-search.png)

À partir d’ici, vous pouvez modifier la requête de recherche de façon à trouver les informations spécifiques qui vous intéressent. Pour plus d’informations sur les recherches dans les journaux, voir [Recherches de journal dans Log Analytics](log-analytics-log-searches.md).

Par exemple, vous pouvez modifier la requête de recherche afin qu’elle affiche tous les conteneurs arrêtés au lieu des conteneurs en cours d’exécution, en changeant **Running** en **Stopped**.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Résoudre des problèmes en recherchant un conteneur en échec
OMS marque un conteneur comme étant en **Échec** s’il a été fermé avec un code de sortie autre que zéro. Vous pouvez consulter un aperçu des erreurs et des échecs de l’environnement dans le panneau **Conteneurs défectueux**.

### <a name="to-find-failed-containers"></a>Pour rechercher les conteneurs défectueux
1. Cliquez sur le panneau **Événements des conteneurs**.  
   ![Événements des conteneurs](./media/log-analytics-containers/containers-events.png)
2. La fenêtre Recherche de journal s’ouvre, affichant l’état des conteneurs, comme ci-dessous.  
   ![État des conteneurs](./media/log-analytics-containers/containers-container-state.png)
3. Ensuite, cliquez sur la valeur défectueuse pour afficher des informations supplémentaires telles que la taille d’image et le nombre d’images arrêtées et défectueuses. Développez **Afficher plus** pour afficher l’ID de l’image.  
   ![Conteneurs défectueux](./media/log-analytics-containers/containers-state-failed.png)
4. Ensuite, recherchez le conteneur qui exécute cette image. Tapez ce qui suit dans la requête de recherche.
   `Type=ContainerInventory <ImageID>` Cette commande affiche les journaux. Vous pouvez faire défiler pour voir le conteneur défectueux.  
   ![Conteneurs défectueux](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Rechercher des données de conteneur dans les journaux
Lorsque vous résolvez une erreur spécifique, il peut être utile de voir l’emplacement où elle se produit dans votre environnement. Les types de journaux suivants vous aident à créer des requêtes qui retournent les informations souhaitées.

* **ContainerInventory** : utilisez ce type de journal lorsque vous recherchez des informations sur l’emplacement des conteneurs, leurs noms et les images qu’ils exécutent.
* **ContainerImageInventory** : utilisez ce type de journal lorsque vous recherchez des informations organisées par image, et de consulter des informations sur les images, telles que leurs ID ou tailles.
* **ContainerLog** : utilisez ce type de journal lorsque vous recherchez des informations et entrées spécifiques du journal des erreurs.
* **ContainerServiceLog** : utilisez ce type de journal lorsque vous recherchez des informations de piste d’audit pour le démon Docker, telles que les commandes de démarrage, d’arrêter, de suppression ou d’extraction.

### <a name="to-search-logs-for-container-data"></a>Pour rechercher des données de conteneur dans les journaux
* Choisissez une image qui a échoué récemment et recherchez-la dans les journaux des erreurs. Commencez par rechercher un nom de conteneur exécutant cette image avec une recherche **ContainerInventory**. Par exemple, recherchez `Type=ContainerInventory ubuntu Failed`  
    ![Recherche de conteneurs Ubuntu](./media/log-analytics-containers/search-ubuntu.png)
  
  Notez le nom du conteneur en regard de **Name**, puis recherchez ces journaux. Dans cet exemple, il s’agit de `Type=ContainerLog adoring_meitner`.

**Afficher les informations de performances**

Lorsque vous commencez à créer des requêtes, il peut être utile de voir d’abord ce qui est possible. Par exemple, pour afficher toutes les données de performances, essayez d’utiliser une large requête en tapant la requête de recherche suivante.

```
Type=Perf
```

![Performances des conteneurs](./media/log-analytics-containers/containers-perf01.png)

Vous pouvez voir cela sous une forme plus graphique en cliquant sur le mot **Metrics** dans les résultats.

![Performances des conteneurs](./media/log-analytics-containers/containers-perf02.png)

Vous pouvez limiter les données de performances que vous voyez à un conteneur spécifique en tapant le nom de celui-ci à droite de votre requête.

```
Type=Perf <containerName>
```

Cela a pour effet d’afficher la liste des mesures de performances collectées pour un conteneur spécifique.

![Performances des conteneurs](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Exemples de requêtes de recherche de journal
Il est souvent utile de créer des requêtes en commençant par un exemple ou deux, puis en les modifiant afin de les adapter à votre environnement. Comme point de départ, vous pouvez utiliser le panneau **Requêtes notables** pour vous aider à créer des requêtes plus avancées.

![Requêtes de conteneurs](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>Enregistrement de requêtes de recherche de journal
L’enregistrement des requêtes est une fonctionnalité standard dans Log Analytics. En les enregistrant, vous aurez aisément accès à celles que vous avez trouvées utiles pour une utilisation ultérieure.

Après avoir créé une requête qui vous semble utile, enregistrez-la en cliquant sur **Favorites** en haut de la page Recherche de journal. Vous pourrez ainsi y accéder facilement par la suite à partir de la page **Mon tableau de bord**.

## <a name="next-steps"></a>Étapes suivantes
* [Rechercher dans les journaux](log-analytics-log-searches.md) pour consulter des enregistrements de données de conteneur détaillées.

<!--HONumber=Oct16_HO2-->


