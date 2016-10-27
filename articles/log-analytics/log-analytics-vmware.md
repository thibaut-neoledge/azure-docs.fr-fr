<properties
    pageTitle="Solution Analyse VMware dans Log Analytics | Microsoft Azure"
    description="Découvrez comment la solution Analyse VMware peut vous aider à gérer les journaux et à surveiller les hôtes ESXi."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="banders"/>


# <a name="vmware-monitoring-(preview)-solution-in-log-analytics"></a>Solution Analyse VMware (version préliminaire) dans Log Analytics

La solution Analyse VMware dans Log Analytics vous aide à créer une approche centralisée de la journalisation et de l’analyse des journaux VMware volumineux. Cet article décrit comment dépanner, capturer et gérer les hôtes ESXi dans un emplacement unique à l’aide de la solution. La solution vous permet de consulter des données détaillées pour tous vos hôtes ESXi dans un emplacement unique. Vous pouvez voir le nombre, l’état et les tendances des principaux événements des hôtes de machine virtuelle et ESXi, fournis via les journaux d’hôte ESXi. Vous pouvez résoudre des problèmes en consultant des journaux d’hôte ESXi centralisés et en y effectuant des recherches. Et vous pouvez créer des alertes basées sur des requêtes de recherche de journal.

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution

Utilisez les informations suivantes pour installer et configurer la solution.

- Ajoutez la solution Analyse VMware à votre espace de travail OMS en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).

#### <a name="supported-vmware-esxi-hosts"></a>Hôtes VMware ESXi pris en charge
vSphere ESXi Host 5.5 et 6.0

#### <a name="prepare-a-linux-server"></a>Préparer un serveur Linux
Créez une machine virtuelle de système d’exploitation Linux pour recevoir toutes les données Syslog des hôtes ESXi. L’[Agent OMS Linux](log-analytics-linux-agents.md) est le point de regroupement de toutes les données syslog de l’hôte ESXi. Vous pouvez utiliser plusieurs hôtes ESXi pour transférer des journaux à un seul serveur Linux, comme dans l’exemple suivant.  

   ![Flux Syslog](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurer la collecte Syslog

1. Configurez le transfert de Syslog à VSphere. Pour plus d’informations sur la configuration du transfert de Syslog, voir [Configuration de Syslog sur ESXi 5.x et 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Accédez à **Configuration de l’hôte ESXi** > **Logiciel** > **Paramètres avancés** > **Syslog**.
  ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  

2. Dans le champ *Syslog.global.logHost*, ajoutez votre serveur Linux et le numéro de port *1514*. Par exemple, `tcp://hostname:1514` ou `tcp://123.456.789.101:1514`.

3. Ouvrez le pare-feu d’hôte ESXi pour Syslog. **Configuration de l’hôte ESXi** > **Logiciels** > **Profil de sécurité** > **Pare-feu**, puis ouvrez **Propriétés**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  

4. Vérifiez sur la Console vSphere que ce Syslog est correctement configuré. Vérifiez sur l’hôte ESXI que le port **1514** est configuré.

5. Testez la connectivité entre le serveur Linux et de l’hôte ESXi en utilisant la commande `nc`sur l’hôte ESXi. Par exemple :

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

6. Téléchargez et installez l’Agent OMS pour Linux sur le serveur Linux. Pour plus d’informations, voir la [documentation de l’Agent OMS pour Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).

7. Une fois l’Agent OMS pour Linux installé, accédez au répertoire /etc/opt/microsoft/omsagent/sysconf/omsagent.d, copiez le fichier vmware_esxi.conf vers le répertoire /etc/opt/microsoft/omsagent/conf/omsagent.d, puis modifiez le propriétaire/groupe et les autorisations du fichier. Par exemple : 

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```

8.  Redémarrez l’Agent OMS pour Linux en exécutant `sudo /opt/microsoft/omsagent/bin/service_control restart`.

9. Dans le portail OMS, effectuer une recherche de journal pour `Type=VMware_CL`. Quand OMS collecte les données Syslog, il conserve le format Syslog. Dans le portail, certains des champs sont capturés, tel que *Hostname* et *ProcessName*.  

    ![type](./media/log-analytics-vmware/type.png)  

    Si vos résultats de recherche de vue de journal sont similaires à l’image ci-dessus, vous êtes prêt à utiliser le tableau de bord de la solution OMS Analyse VMware.  

## <a name="vmware-data-collection-details"></a>Détails sur la collecte de données Linux

La solution Analyse VMware collecte diverses mesures de performances et données de journaux à partir des hôtes ESXi à l’aide des Agents OMS pour Linux que vous avez activés.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte.

| plateforme | Agent OMS pour Linux | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
|---|---|---|---|---|---|---|
| Linux|![Oui](./media/log-analytics-vmware/oms-bullet-green.png)|![Non](./media/log-analytics-vmware/oms-bullet-red.png)|![Non](./media/log-analytics-vmware/oms-bullet-red.png)|            ![Non](./media/log-analytics-containers/oms-bullet-red.png)|![Non](./media/log-analytics-vmware/oms-bullet-red.png)| Toutes les 3 minutes.|


Le tableau suivant affiche des exemples de champs de données collectés par la solution Analyse VMware :

| Nom du champ | Description |
| --- | --- |
| Device_s| appareils de stockage VMware |
| ESXIFailure_s | types d’échec |
| EventTime_t | heure à laquelle l’événement s’est produit |
| HostName_s | nom d’hôte ESXi |
| Operation_s | créer ou supprimer une machine virtuelle |
| ProcessName_s | nom de l’événement |
| ResourceId_s | nom de l’hôte VMware |
| ResourceLocation_s | VMware |
| ResourceName_s | VMware |
| ResourceType_s | Hyper-V |
| SCSIStatus_s | Statut SCSI de VMware |
| SyslogMessage_s | données Syslog |
| UserName_s | utilisateur qui a créé ou supprimé la machine virtuelle |
| VMName_s | nom de la machine virtuelle |
| Ordinateur | ordinateur hôte |
| TimeGenerated | heure à laquelle les données ont été générées |
| DataCenter_s | centre de données VMware |
| StorageLatency_s | latence de stockage (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Présentation de la solution Analyse VMware

La vignette VMware apparaît dans le portail OMS. Elle fournit une vue d’ensemble des erreurs. Lorsque vous cliquez sur la vignette, vous accédez à l’affichage du tableau de bord.

![vignette](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Accédez à l’affichage du tableau de bord

Dans l’affichage du tableau de bord **VMware**, les panneaux sont organisés par :

- Nombre d’états d’échec
- Hôte principal par nombre d’événements
- Nombres d’événements principaux
- Activités de machine virtuelle
- Événements de disque de l’hôte ESXi


![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Cliquez sur n’importe quel panneau pour ouvrir le volet de recherche de Log Analytics qui affiche des informations détaillées spécifiques pour le panneau.

À partir d’ici, vous pouvez modifier la requête de recherche pour l’adapter à un aspect spécifique. Pour un didacticiel relatifs aux principes de base de recherche OMS, voir le [didacticiel de recherche de journal OMS](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Rechercher des événements de l’hôte ESXi

Un seul hôte ESXi génère plusieurs journaux basés sur leurs processus. La solution Analyse VMware les centralise et résume les nombres d’événements. Cette vue centralisée vous permet de comprendre quel hôte ESXi a un volume élevé d’événements ainsi que les événements qui se produisent le plus fréquemment dans votre environnement.

![événement](./media/log-analytics-vmware/events.png)

Vous pouvez approfondir davantage en cliquant sur un hôte ESXi ou un type d’événement.

Lorsque vous cliquez sur un nom d’hôte ESXi, vous voyez les informations de cet hôte ESXi. Si vous souhaitez affiner des résultats avec le type d’événement, ajoutez `“ProcessName_s=EVENT TYPE”` à votre requête de recherche. Vous pouvez sélectionner **ProcessName** dans le filtre de recherche. Cela restreint les informations pour vous.

![explorer](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Rechercher les activités de machine virtuelle élevées

Une machine virtuelle peut être créée et supprimée sur tout hôte ESXi. Il est utile pour un administrateur d’identifier le nombre de machines virtuelles que crée un hôte ESXi. Cela aide ensuite à comprendre la planification des performances et de la capacité. Il est essentiel de suivre les événements d’activité de machine virtuelle lors de la gestion de votre environnement.

![explorer](./media/log-analytics-vmware/vmactivities1.png)

Si vous souhaitez voir d’autres données de création de machine virtuelle hôte ESXi, cliquez sur un nom d’hôte ESXi.

![explorer](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Requêtes de recherche courantes

La solution inclut d’autres requêtes utiles qui peuvent vous aider à gérer vos hôtes ESXi, telles que l’espace de stockage élevé, la latence du stockage et la défaillance de chemin.

![requêtes](./media/log-analytics-vmware/queries.png)

#### <a name="save-queries"></a>Enregistrer des requêtes

L’enregistrement des requêtes de recherche est une fonctionnalité standard dans OMS, qui peut vous aider à conserver toutes les requêtes que vous avez trouvées utiles. Après avoir créé une requête que vous trouvez utile, enregistrez-la en cliquant sur **Favorites**. Vous pouvez réutiliser facilement une requête enregistrée à partir de la page [Mon tableau de bord](log-analytics-dashboards.md) dans laquelle vous pouvez créer vos propres tableaux de bord personnalisés.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Créer des alertes à partir de requêtes

Après avoir créé vos requêtes, vous pouvez les utiliser pour vous avertir quand des événements spécifiques se produisent. Pour plus d’informations sur la création d’alertes, voir [Alertes dans Log Analytics](log-analytics-alerts.md). Pour obtenir des exemples de requêtes d’alerte et d’autres requêtes, voir le billet de blog [Monitor VMware using OMS Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) (Analyser VMware à l’aide d’OMS Log Analytics).


## <a name="next-steps"></a>Étapes suivantes

- Utiliser des [recherches de journal](log-analytics-log-searches.md) dans Log Analytics pour afficher des données détaillées de l’hôte VMware.
- [Créer vos propres tableaux de bord](log-analytics-dashboards.md) affichant des données de l’hôte VMware.
- [Créer des alertes](log-analytics-alerts.md) lorsque des événements d’hôte VMware spécifiques se produisent.



<!--HONumber=Oct16_HO2-->


