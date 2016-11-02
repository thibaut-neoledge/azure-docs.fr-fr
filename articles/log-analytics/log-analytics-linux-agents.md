<properties
    pageTitle="Connecter des ordinateurs Linux à Log Analytics | Microsoft Azure"
    description="Avec Log Analytics, vous pouvez collecter et exploiter les données générées par des ordinateurs Linux."
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
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="connect-linux-computers-to-log-analytics"></a>Connecter des ordinateurs Linux à Log Analytics

Avec Log Analytics, vous pouvez collecter et exploiter les données générées par des ordinateurs Linux. L’ajout de données collectées sur Linux dans OMS vous permet de gérer les systèmes Linux et les solutions de conteneur telles que Docker, indépendamment de l’emplacement de vos ordinateurs. Ces sources de données peuvent résider sur des serveurs physiques de votre centre de données local, sur des ordinateurs virtuels dans un service hébergé sur le cloud comme Amazon Web Services (AWS) ou Microsoft Azure, voire sur votre ordinateur portable. De plus, OMS collecte les données des ordinateurs Windows de la même façon, prenant en charge un véritable environnement informatique hybride.

Vous pouvez afficher et gérer les données de toutes ces sources avec Log Analytics dans OMS, via un portail unique. Plus besoin de multiplier les systèmes de surveillance des données. De plus, vous pouvez exporter toutes les données que vous souhaitez vers la solution ou le système d’analyse marketing dont vous disposez.

Cet article est un guide de démarrage rapide qui vous aide à collecter et gérer les données de vos ordinateurs Linux à l’aide de l’Agent OMS pour Linux. Pour des détails plus techniques, tels que la configuration du serveur proxy, des informations sur les mesures CollectD, et des sources de données JSON personnalisées, voir la [présentation de l’Agent OMS pour Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) (en anglais) et la [documentation complète de l’Agent OMS pour Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) (en anglais) sur Github.


Pour l’instant, vous pouvez collecter les types de données suivants sur des ordinateurs Linux :

- Mesures de performances
- Événements Syslog
- Alertes de Nagios et Zabbix
- Journaux, inventaire et mesures de performances du conteneur Docker

## <a name="supported-linux-versions"></a>Versions de Linux prises en charge

Les versions x86 et x64 sont officiellement prises en charge sur diverses distributions de Linux. Toutefois, l’Agent OMS pour Linux peut également s’exécuter dans d’autres distributions non répertoriées.

- Amazon Linux 2012.09 à 2015.09
- CentOS Linux 5, 6 et 7
- Oracle Linux 5, 6 et 7
- Red Hat Enterprise Linux Server 5, 6 et 7
- Debian GNU/Linux 6, 7 et 8
- Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10
- SUSE Linux Enterprise Server 11 et 12

## <a name="oms-agent-for-linux"></a>Agent OMS pour Linux
L’Agent Operations Management Suite pour Linux comprend plusieurs packages. Les packages suivants sont disponibles en exécutant l’application shell avec `--extract`.

**Package** | **Version** | **Description**
----------- | ----------- | --------------
omsagent | 1.1.0 | Agent Operations Management Suite pour Linux
omsconfig | 1.1.1 | Agent de configuration de l’Agent OMS
omi | 1.0.8.3 | Open Management Infrastructure (OMI) - Serveur CIM léger
scx | 1.6.2 | Fournisseurs CIM OMI pour les mesures de performances des systèmes d’exploitation
apache-cimprov | 1.0.0 | Fournisseur de surveillance des performances d’Apache HTTP Server pour OMI. Installé uniquement si Apache HTTP Server est détecté.
mysql-cimprov | 1.0.0 | Fournisseur de surveillance des performances de MySQL Server pour OMI. Installé uniquement si MySQL/MariaDB Server est détecté.
docker-cimprov | 0.1.0 | Fournisseur de Docker pour OMI. Installé uniquement si Docker est détecté.

### <a name="additional-installation-artifacts"></a>Artefacts d’installation supplémentaires
Après avoir installé les packages de l’Agent OMS pour Linux, les modifications de configuration système suivantes sont appliquées. Ces artefacts sont supprimés lorsque le package omsagent est désinstallé.
- Un utilisateur sans privilège nommé `omsagent` est créé. Il s’agit du compte utilisé par le démon omsagent pour s’exécuter.
- Un fichier « include » sudoers est créé à l’emplacement /etc/sudoers.d/omsagent. Il autorise omsagent à redémarrer les démons syslog et omsagent. Si les directives « include » sudo ne sont pas prises en charge dans la version installée de sudo, ces entrées sont inscrites dans /etc/sudoers.
- La configuration de syslog est modifiée pour transférer un sous-ensemble d’événements à l’agent. Pour plus d’informations, consultez la section **Configuration de la collecte des données** ci-dessous.

### <a name="linux-data-collection-details"></a>Détails sur la collecte de données Linux

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte.

| source | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | Fréquence de collecte |
|---|---|---|---|---|---|---|
|Zabbix|![Oui](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|1 minute|
|Nagios|![Oui](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|À l’arrivée|
|syslog|![Oui](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|Depuis Azure Storage : 10 minutes ; depuis l’agent : à l’arrivée|
|Compteurs de performances Linux|![Oui](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|Comme prévu, minimum de 10 secondes|
|Suivi des modifications|![Oui](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Non](./media/log-analytics-linux-agents/oms-bullet-red.png)|Toutes les heures|



### <a name="package-requirements"></a>Packages requis
| **Package requis**  | **Description**   | **Version minimum**|
|--------------------- | --------------------- | -------------------|
|Glibc |    Bibliothèque C de GNU   | 2.5-12|
|Openssl    | Bibliothèques OpenSSL | 0.9.8e ou 1.0|
|Curl | Client web cURL | 7.15.5
|Python-ctypes |Bibliothèques de fonctions | n/a|
|PAM | Pluggable Authentication Modules  |n/a |

>[AZURE.NOTE] rsyslog ou syslog-ng est requis pour collecter les messages syslog. Le démon syslog par défaut sur la version 5 de Red Hat Enterprise Linux, CentOS et Oracle Linux (sysklog) ne prend pas en charge la collecte des événements syslog. Pour collecter les données syslog avec cette version de ces distributions, le démon rsyslog doit être installé et configuré à la place de sysklog.

## <a name="quick-install"></a>Installation rapide

Exécutez les commandes suivantes pour télécharger l’omsagent, valider la somme de contrôle, puis installer et intégrer l’agent. Ces commandes sont de type 64 bits. L’ID et la clé primaire de l’espace de travail sont disponibles sur le portail OMS dans **Paramètres**, sous l’onglet **Sources connectées**.

![détails sur l’espace de travail](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh
sha256sum ./omsagent-1.1.0-28.universal.x64.sh
sudo sh ./omsagent-1.1.0-28.universal.x64.sh --upgrade -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

Il existe plusieurs autres méthodes pour installer l’agent et le mettre à niveau. Pour les découvrir, consultez [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux).

Vous pouvez également regarder la [présentation vidéo d’Azure](https://www.youtube.com/watch?v=mF1wtHPEzT0).

## <a name="choose-your-linux-data-collection-method"></a>Choisir la mode de collecte des données Linux

Le choix des types de données à collecter varie selon que vous souhaitez utiliser le portail OMS ou modifier plusieurs fichiers de configuration directement sur vos clients Linux. Si vous choisissez d’utiliser le portail, la configuration est automatiquement envoyée à tous vos clients Linux. Si vous avez besoin de différentes configurations pour différents clients Linux, vous devez modifier les fichiers de chaque client ou utiliser une solution telle que PowerShell DSC, Chef ou Puppet.

Vous pouvez spécifier les événements syslog et les compteurs de performances que vous souhaitez collecter, à l’aide des fichiers de configuration sur les ordinateurs Linux. *Si vous avez choisi de configurer la collecte des données en modifiant les fichiers de configuration de l’agent, vous devez désactiver la configuration centralisée.*   Les instructions ci-dessous permettent de configurer la collecte des données dans les fichiers de configuration de l’agent, mais aussi de désactiver la configuration centralisée sur tous les agents OMS pour Linux ou sur chaque ordinateur individuellement.

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>Désactiver la gestion OMS sur un ordinateur Linux

La collecte centralisée des données de configuration est désactivée sur un ordinateur Linux grâce au script OMS_MetaConfigHelper.py. Ce script est très utile si quelques ordinateurs requièrent une configuration spéciale.

Pour désactiver la configuration centralisée :

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

Pour réactiver la configuration centralisée :

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Compteurs de performances Linux

Les compteurs de performances Linux sont similaires aux compteurs de performances Windows : ils fonctionnent de la même façon. Vous pouvez utiliser les procédures suivantes pour les ajouter et les configurer. Une fois ces compteurs ajoutés dans OMS, les données sont collectées toutes les 30 secondes.

### <a name="to-add-a-linux-performance-counter-in-oms"></a>Pour ajouter un compteur de performances Linux dans OMS

1. Pour configurer des agents OMS pour Linux à l’aide du portail OMS, ajoutez des compteurs de performances Linux dans la page Paramètres, puis cliquez sur **Données**.  
2. Dans la page **Paramètres**, sous **Données**, cliquez sur **Compteurs de performance Linux**, puis sélectionnez ou tapez le nom du compteur à ajouter.  
    ![données](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. Si vous ne connaissez pas le nom complet du compteur, commencez à taper les premières lettres et la liste des compteurs correspondants s’affiche. Lorsque vous avez trouvé le compteur à ajouter, cliquez sur son nom dans la liste puis sur l’icône de signe plus pour l’ajouter.
4. Une fois ajouté, le compteur apparaît dans la liste des compteurs avec une barre de couleur.
5. Par défaut, l’option **Appliquer la configuration ci-dessous à mes machines** est sélectionnée. Si vous souhaitez désactiver l’envoi des données de configuration, décochez la case.
6. Lorsque vous avez terminé la modification des compteurs de performances, en bas de la page, cliquez sur **Enregistrer** pour finaliser vos modifications. Les modifications de configuration que vous avez apportées sont ensuite envoyées à tous les Agents OMS pour Linux enregistrés dans OMS, généralement dans les 5 minutes.

### <a name="configure-linux-performance-counters-in-oms"></a>Configurer des compteurs de performances Linux dans OMS

Pour les compteurs de performances Windows, vous pouvez choisir une instance spécifique de chaque compteur de performances. En revanche, pour les compteurs de performances Linux, l’instance de compteur que vous choisissez s’applique à tous les compteurs enfants du compteur parent. Le tableau suivant montre les instances courantes disponibles pour les compteurs de performances Linux et Windows.

| **Nom de l’instance** | **Signification** |
| --- | --- |
| \_Total | Total de toutes les instances |
| \* | Toutes les instances |
| (/&#124;/var) | Correspond aux instances nommées : / ou /var |


De même, l’intervalle d’échantillonnage que vous choisissez pour un compteur parent s’applique à tous ses compteurs enfants. En d’autres termes, tous les intervalles d’échantillonnage et toutes les instances des compteurs enfants sont liés entre elles.

### <a name="add-and-configure-performance-metrics-with-linux"></a>Ajouter et configurer des mesures de performances avec Linux

Les mesures de performances à collecter sont contrôlées par la configuration du fichier /etc/opt/microsoft/omsagent/conf/omsagent.conf. Pour obtenir les classes et mesures disponibles pour l’Agent OMS pour Linux, consultez [Available Performance Metrics](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) .

Chaque objet, ou catégorie, de mesures de performances à collecter doit être défini dans le fichier de configuration comme un seul élément `<source>` . La syntaxe suit le modèle suivant.

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

Les paramètres configurables de cet élément sont :

- **Object\_name** : nom de l’objet à collecter.
- **Instance\_regex** : a *expression régulière* définissant les instances à collecter. La valeur `.*` spécifie toutes les instances. Pour ne collecter les mesures de processeur que de l’instance \_Total, vous pouvez spécifier `_Total`. Pour ne collecter les mesures de processeur que des instances crond ou sshd, vous pouvez spécifier `(crond|sshd)`.
- **Counter\_name\_regex** : *expression régulière* définissant les compteurs (de l’objet) à collecter. Pour collecter tous les compteurs de l’objet, spécifiez : `.*`. Pour ne collecter que les compteurs du fichier d’échange de l’objet mémoire, vous pouvez spécifier : `.+Swap.+`
- **Interval**: fréquence de collecte des compteurs de l’objet.

La configuration par défaut des mesures de performances est la suivante :

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>Activer des compteurs de performances MySQL à l’aide de commandes Linux

Si MySQL Server ou MariaDB Server est détecté sur l’ordinateur où omsagent est installé, un fournisseur de surveillance des performances de MySQL Server est automatiquement installé. Ce fournisseur se connecte au serveur MySQL/MariaDB local pour afficher les statistiques de performances. Vous devez configurer les informations d’identification de l’utilisateur MySQL, afin que le fournisseur puisse accéder à MySQL Server.

Pour définir le compte utilisateur par défaut du serveur MySQL sur l’hôte local, utilisez l’exemple de commande suivant.

>[AZURE.NOTE] Le fichier des informations d’identification doit être accessible en lecture par le compte omsagent. L’exécution de la commande mycimprovauth comme omsgent est recommandée.


```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


Sinon, vous pouvez spécifier les informations d’identification MySQL requises dans un fichier, en créant le fichier : /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth. Pour plus d’informations sur la gestion des informations d’identification MySQL à des fins de surveillance par le fichier mysql-auth, consultez [Gérer des informations d’identification de surveillance de MySQL dans le fichier d’authentification](#manage-mysql-monitoring-credentials-in-the-authentication-file).

Pour plus d’informations sur les autorisations d’objet requises par l’utilisateur MySQL pour collecter les données de performances de MySQL Server, consultez [Autorisations de base de données requises par l’utilisateur MySQL pour collecter les données de performances de MySQL Server](#database-permissions-required-for-mysql-performance-counters) .

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>Activer les compteurs de performances d’Apache HTTP Server à l’aide de commandes Linux

Si Apache HTTP Server est détecté sur l’ordinateur où omsagent est installé, un fournisseur de surveillance des performances d’Apache HTTP Server est automatiquement installé. Ce fournisseur repose sur un « module » Apache qui doit être chargé dans Apache HTTP Server afin d’accéder aux données de performances.

Vous pouvez charger le module avec la commande suivante :

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Pour décharger le module de surveillance Apache, exécutez la commande suivante :

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>Pour afficher les données de performances avec Log Analytics

1. Dans le portail Operations Management Suite, cliquez sur la mosaïque Recherche de journal.
2. Dans la barre de recherche, tapez `* (Type=Perf)` pour afficher tous les compteurs de performances.


Comme OMS collecte également les données des compteurs de performances Windows, vous devez restreindre la recherche aux données spécifiquement Linux. Ainsi, l’exemple suivant affiche les données de performances concernant un exemple de serveur Linux nommé Chorizo21.

```
Type=Perf Computer=chorizo*
```

![exemple de serveur affiché dans les résultats de la recherche](./media/log-analytics-linux-agents/oms-perfsearch01.png)

Dans les résultats, vous pouvez cliquer sur **Mesures** pour afficher les compteurs pour lesquels des données ont été collectées. Les données en temps réel sont indiquées sous forme graphique pour chaque compteur.

![Mesures](./media/log-analytics-linux-agents/oms-perfmetrics01.png)


## <a name="syslog"></a>syslog

Syslog est un protocole de journalisation des événements, similaire aux Journaux des événements Windows : les deux fonctionnent de la même façon lorsqu’ils sont affichés dans OMS.

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>Pour ajouter une nouvelle fonctionnalité syslog Linux dans OMS

1. Dans la page **Paramètres** sous **Données**, cliquez sur **Syslog** puis, à gauche de l’icône plus, tapez le nom de la fonctionnalité Syslog à ajouter.
    ![Syslog Linux](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2.  Si vous ne connaissez pas le nom complet de la fonctionnalité, tapez les premières lettres et la liste des fonctionnalités syslog correspondantes s’affiche. Lorsque vous avez trouvé la fonctionnalité syslog à ajouter, cliquez sur son nom dans la liste puis sur l’icône de signe plus pour l’ajouter.
3.  Une fois la fonctionnalité ajoutée, le compteur apparaît dans la liste des compteurs avec une barre de couleur. Ensuite, choisissez les niveaux de gravité (catégories d’informations sur les fonctionnalités syslog) que vous souhaitez collecter.
4.  En bas de la page, cliquez sur **Enregistrer** pour finaliser vos modifications. Les modifications de configuration que vous avez apportées sont ensuite envoyées à tous les Agents OMS pour Linux enregistrés dans OMS, généralement dans les 5 minutes.


### <a name="configure-linux-syslog-facilities-in-linux"></a>Configurer des fonctionnalités syslog Linux dans Linux

Des événements syslog sont envoyés du démon syslog, par exemple rsyslog ou syslog-ng, à un port local écouté par l’agent. Le port par défaut est 25224. Lorsque l’agent est installé, une configuration syslog par défaut est appliquée. Elle se trouve à l’emplacement suivant :


Rsyslog : /etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng : /etc/syslog-ng/syslog-ng.conf


La configuration syslog par défaut de l’agent OMS télécharge les événements syslog de toutes les fonctionnalités ayant au moins le niveau de gravité Avertissement.

>[AZURE.NOTE] Si vous modifiez cette configuration, vous devez redémarrer le démon syslog pour que les modifications prennent effet.

La configuration syslog par défaut de l’Agent OMS pour Linux dans OMS est la suivante :

#### <a name="rsyslog"></a>Rsyslog

```
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>Syslog-ng

```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>Pour afficher tous les événements Syslog avec Log Analytics

1. Dans le portail Operations Management Suite, cliquez sur la mosaïque **Recherche de journal** .
2. Dans le groupe **Gestion de journal** , sélectionnez une recherche syslog prédéfinie puis exécutez-la.

Cet exemple montre tous les événements Syslog.

![Événements syslog affichés dans Recherche de journal](./media/log-analytics-linux-agents/oms-linux-syslog.png)

Maintenant, vous pouvez examiner les résultats de la recherche plus en détail.

## <a name="linux-alerts"></a>Alertes Linux

Si vous utilisez Nagios ou Zabbix pour gérer vos ordinateurs Linux, OMS peut recevoir les alertes générées par ces outils. Toutefois, il n’existe actuellement aucune méthode pour configurer les données d’alerte entrantes à l’aide du portail OMS. En revanche, vous devez modifier un fichier de configuration pour commencer à envoyer des alertes à OMS.



### <a name="collect-alerts-from-nagios"></a>Collecter les alertes de Nagios

Pour collecter les alertes d’un serveur Nagios, vous devez apporter les modifications de configuration suivantes.

1. Accordez à l’utilisateur **omsagent** un accès en lecture au fichier journal Nagios (/var/log/nagios/nagios.log/var/log/nagios/nagios.log). Si le fichier nagios.log appartient au groupe **nagios**, vous pouvez ajouter l’utilisateur **omsagent** au groupe **nagios**.

    ```
    sudo usermod –a -G nagios omsagent
    ```

2. Modifiez le fichier de configuration omsagent.conf (/etc/opt/microsoft/omsagent/conf/omsagent.conf). Vérifiez que les entrées suivantes sont présentes et non mises en commentaire :

    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>

    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```

3. Redémarrez le démon omsagent :

    ```
    sudo service omsagent restart
    ```

### <a name="collect-alerts-from-zabbix"></a>Collecter les alertes de Zabbix

Pour collecter les alertes d’un serveur Zabbix, vous allez effectuer des étapes similaires à celles de Nagios ci-dessus, mais vous devez spécifier un utilisateur et un mot de passe en *clair*. Cette solution n’est pas idéale, mais elle devrait changer rapidement. Pour résoudre ce problème, nous vous recommandons de créer l’utilisateur et de ne lui accorder que l’autorisation de surveillance.

Une section du fichier de configuration omsagent.conf (/etc/opt/microsoft/omsagent/conf/omsagent.conf) pour Zabbix pourrait ressembler à celle ci-dessous :

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### <a name="view-alerts-in-log-analytics-search"></a>Afficher les alertes dans la recherche Log Analytics

Après avoir configuré vos ordinateurs Linux pour envoyer des alertes à OMS, vous pouvez utiliser quelques requêtes simples pour afficher les alertes. La requête suivante renvoie toutes les alertes enregistrées qui ont été générées. Par exemple, en cas de problème dans votre infrastructure informatique, les résultats de la requête suivante peuvent en révéler la cause. Et vous pouvez facilement examiner les alertes par système source afin de restreindre votre champ d’investigation. L’avantage est que vous n’avez plus à utiliser différents systèmes de gestion. Si vos alertes sont envoyées à OMS, vous pouvez utiliser cet outil.

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>Pour afficher toutes les alertes Nagios avec Log Analytics
1. Dans le portail Operations Management Suite, cliquez sur la mosaïque **Recherche de journal** .
2. Dans la barre de requête, tapez la requête suivante :

    ```
    Type=Alert SourceSystem=Nagios
    ```
![Alertes Nagios affichées dans Recherche de journal](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

Après avoir consulté les résultats de la recherche, vous pouvez examiner les détails supplémentaires tels que *AlertState*.

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>Pour afficher tous les événements Zabbix avec Log Analytics
1. Dans le portail Operations Management Suite, cliquez sur la mosaïque **Recherche de journal** .
2. Dans la barre de requête, tapez la requête suivante :

    ```
    Type=Alert SourceSystem=Zabbix
    ```
![Alertes Zabbix affichées dans Recherche de journal](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

Après avoir consulté les résultats de la recherche, vous pouvez examiner les détails supplémentaires tels que *AlertName*.


## <a name="compatibility-with-system-center-operations-manager"></a>Compatibilité avec System Center Operations Manager

L’Agent OMS pour Linux partage ses fichiers binaires avec l’agent System Center Operations Manager. L’installation de l’Agent OMS pour Linux sur un système géré par Operations Manager met à niveau les packages OMI et SCX sur l’ordinateur. L’Agent OMS pour Linux et System Center 2012 R2 sont compatibles. Toutefois, **System Center 2012 SP1 et ses versions antérieures ne sont actuellement pas compatibles avec l’Agent OMS pour Linux.**

>[AZURE.NOTE] Si l’Agent OMS pour Linux est installé sur un ordinateur non géré par Operations Manager, et que vous décidez ensuite de gérer l’ordinateur avec Operations Manager, vous devez modifier la configuration d’OMI avant de détecter l’ordinateur. **Cette étape n’est pas nécessaire si l’agent Operations Manager est installé avant l’Agent OMS pour Linux.**

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>Pour permettre à l’Agent OMS pour Linux de communiquer avec Operations Manager

1. Modifiez le fichier /etc/opt/omi/conf/omiserver.conf.
2. Vérifiez que la ligne commençant par **httpsport =** spécifie le port 1270. Par exemple `httpsport=1270`
3. Redémarrez le serveur OMI.

    ```
    service omiserver restart or systemctl restart omiserver
    ```




## <a name="database-permissions-required-for-mysql-performance-counters"></a>Autorisations de base de données requises par l’utilisateur MySQL pour collecter les données de performances de MySQL Server

Pour accorder des autorisations à un utilisateur surveillant MySQL, l’utilisateur doit posséder l’autorisation « GRANT option », ainsi que l’autorisation accordée.

Pour que l’utilisateur MySQL renvoie les données de performances, il doit accéder aux requêtes suivantes :

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

Outre ces requêtes, l’utilisateur de MySQL requiert un accès SELECT aux tables par défaut suivantes :

- information_schema
- mysql

Ces privilèges peuvent être accordés à l’aide des commandes Grant suivantes.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>Gérer des informations d’identification de surveillance de MySQL dans le fichier d’authentification

Les sections suivantes vous aident à gérer les informations d’identification de MySQL.

### <a name="configure-the-mysql-omi-provider"></a>Configurer le fournisseur OMI de MySQL

Le fournisseur OMI de MySQL nécessite un utilisateur MySQL préconfiguré et des bibliothèques clientes MySQL installées pour interroger les informations d’intégrité et de performances de l’instance MySQL.

### <a name="mysql-omi-authentication-file"></a>Fichier d’authentification OMI de MySQL

Le fournisseur OMI de MySQL utilise un fichier d’authentification pour déterminer l’adresse de liaison et le port écouté par l’instance MySQL, ainsi que les informations d’identification à utiliser pour collecter des mesures. Pendant l’installation, le fournisseur OMI de MySQL recherche dans les fichiers de configuration my.cnf (emplacements par défaut) l’adresse de liaison et le port, et configure partiellement le fichier d’authentification OMI de MySQL.

Pour surveiller une instance de serveur MySQL, ajoutez un fichier d’authentification OMI MySQL prégénéré dans le répertoire correct.

### <a name="authentication-file-format"></a>Format du fichier d’authentification

Le fichier d’authentification OMI de MySQL est un fichier texte qui contient les informations suivantes :

- Port
- Adresse de liaison
- Nom d’utilisateur MySQL
- Mot de passe encodé en base 64

Le fichier d’authentification OMI de MySQL n’accorde que des droits de lecture/écriture à l’utilisateur de Linux qui l’a généré.

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

Un fichier d’authentification OMI de MySQL par défaut contient une instance et un numéro de port par défaut, selon les informations disponibles et analysées dans le fichier de configuration MySQL trouvé.

L’instance par défaut est un moyen pour faciliter la gestion de plusieurs instances MySQL sur un hôte Linux et est représentée par l’instance avec le port 0. Toutes les instances ajoutées hériteront des propriétés de l’instance par défaut. Par exemple, si l’instance MySQL à l’écoute du port 3308 est ajoutée, l’adresse de liaison, le nom d’utilisateur et le mot de passe encodé en base 64 de l’instance par défaut sont utilisés pour tester et surveiller l’instance à l’écoute de 3308. Si l’instance sur 3308 est liée à une autre adresse et utilise les mêmes nom d’utilisateur et mot de passe MySQL, seule la répétition de l’adresse de liaison est nécessaire. Les autres propriétés seront héritées.

Voici à quoi peuvent ressembler des fichiers d’authentification.

Instance par défaut et instance avec port 3308 :

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

Instance par défaut et instance avec port 3308 + mot de passe différent encodé en base 64 :

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **Propriété** | **Description** |
| --- | --- |
| Port | Le port représente le port actif écouté par l’instance MySQL.  Le port 0 implique que les propriétés suivantes sont utilisées pour l’instance par défaut. |
| Adresse de liaison | Cette adresse est l’adresse de liaison MySQL actuelle. |
| username | Il s’agit du nom d’utilisateur MySQL que vous souhaitez utiliser pour surveiller l’instance de serveur MySQL. |
| Mot de passe encodé en base 64 | Il s’agit du mot de passe encodé en base 64, de l’utilisateur surveillant MySQL. |
| AutoUpdate | Lors de sa mise à niveau, le fournisseur OMI de MySQL recherche les modifications dans le fichier my.cnf et remplace le fichier d’authentification OMI de MySQL. Activez (True) ou désactivez (False) cet indicateur selon que le fichier d’authentification OMI de MySQL requiert ou non des mises à jour. |

#### <a name="authentication-file-location"></a>Emplacement du fichier d’authentification

Le fichier d’authentification OMI de MySQL doit être situé à l’emplacement suivant et s’appeler « mysql-auth » :

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

L’utilisateur omsagent doit posséder le fichier (ou le répertoire auth/omsagent).

## <a name="agent-logs"></a>Journaux de l’agent

Les journaux de l’Agent OMS pour Linux se trouvent à l’emplacement suivant :

/var/opt/microsoft/omsagent/log/

Les journaux de l’Agent OMS pour Linux pour le programme omsconfig (configuration de l’agent) se trouvent à l’emplacement suivant :

/var/opt/microsoft/omsconfig/log/

Les journaux des composants OMI et SCX (qui fournissent des données de mesures de performances) se trouvent à l’emplacement suivant :

/var/opt/omi/log/ et /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>Résolution de problèmes de l’Agent OMS pour Linux

Pour diagnostiquer et résoudre des problèmes courants, utilisez les informations suivantes.

Si aucune des informations de résolution de problèmes de cette section ne vous est utile, les ressources suivantes peuvent également vous aider à résoudre votre problème.

- Les clients bénéficiant du Support Premier peuvent soumettre un cas de support via le site [Premier](https://premier.microsoft.com/)
- Les clients titulaires d’un contrat de support Azure peuvent soumettre des cas de support via le [portail Azure](https://manage.windowsazure.com/?getsupport=true)
- Déposer un [problème GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)
- Forum de commentaires pour formuler des idées et créer des rapports de bogue [http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback)

### <a name="important-log-locations"></a>Emplacement de journaux importants

Fichier | Chemin
---- | -----
Fichier journal de l’Agent OMS pour Linux | `/var/opt/microsoft/omsagent/log/omsagent.log `
Fichier journal de configuration de l’Agent OMS | `/var/opt/microsoft/omsconfig/omsconfig.log`

### <a name="important-configuration-files"></a>Fichiers de configuration importants

Catégorie | Emplacement du fichier
----- | -----
syslog | `/etc/syslog-ng/syslog-ng.conf` ou `/etc/rsyslog.conf` ou `/etc/rsyslog.d/95-omsagent.conf`
Performances, Nagios, Zabbix, sortie OMS et agent général | `/etc/opt/microsoft/omsagent/conf/omsagent.conf`
Configurations supplémentaires | `/etc/opt/microsoft/omsagent/conf.d/*.conf`

>[AZURE.NOTE] Les fichiers de changement de configuration pour les compteurs de performances et Syslog sont remplacés si la Configuration du portail OMS est activée. Vous pouvez désactiver la configuration sur le portail OMS pour tous les nœuds ou un seul d’entre eux en exécutant la commande suivante :

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>Activer l’enregistrement du débogage

Pour activer l’enregistrement du débogage, vous pouvez utiliser le plug-in de sortie OMS et une sortie détaillée.

#### <a name="oms-output-plugin"></a>Plug-in de sortie OMS

FluentD permet au plug-in de spécifier des niveaux de journalisation pour différents niveaux du journal en relation avec les entrées et sorties. Pour spécifier un autre niveau du journal pour une sortie OMS, modifiez la configuration générale de l’agent dans le fichier `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

Près du bas du fichier de configuration, modifiez la propriété `log_level` de `info` en `debug`.

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

La journalisation du débogage vous permet de voir les chargements par lot sur le Service OMS, répartis par type, nombre d’éléments de données et temps d’envoi.

*Exemple de journal activé pour le débogage :*
```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>Sortie détaillée.
Au lieu d’utiliser le plug-in de sortie OMS, vous pouvez acheminer les éléments de données directement vers `stdout`, qui est visible dans le fichier journal de l’Agent OMS pour Linux.

Dans le fichier de configuration générale de l’Agent OMS sur `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, commentez le plug-in de sortie OMS en ajoutant le symbole `#` au début de chaque ligne.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Sous le plug-in de sortie, retirez le commentaire de la section suivante en supprimant le symbole `#` au début de chaque ligne.

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>Les messages Syslog transférés n’apparaissent pas dans le journal

#### <a name="probable-causes"></a>Causes probables

- La configuration appliquée au serveur Linux n’autorise pas la collecte des fonctionnalités et/ou niveaux du journal envoyés.
- Le message Syslog n’est pas transmis correctement au serveur Linux.
- Le nombre de messages transmis par seconde est trop élevé pour être géré avec la configuration de base de l’Agent OMS pour Linux.

#### <a name="resolutions"></a>Résolutions

- Vérifiez que la configuration du portail OMS pour Syslog possède toutes les fonctionnalités nécessaires et les niveaux du journal corrects.
  - **Portail OMS > Paramètres > Données > Syslog**
-  Vérifiez que les démons de messagerie Syslog natifs (`rsyslog`, `syslog-ng`) sont en mesure de recevoir les messages transférés.
- Vérifiez les paramètres de pare-feu sur le serveur Syslog pour vous assurer que les messages ne sont pas bloqués.
-  Simulez un message Syslog à OMS à l’aide de la commande `logger`. Par exemple :
  - `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>Problèmes de connexion à OMS lors de l’utilisation d’un proxy

#### <a name="probable-causes"></a>Causes probables

- Le proxy spécifié lors de l’installation et la configuration de l’agent est incorrect.
- Les points de terminaison du Service OMS ne sont pas approuvés dans votre centre de données.

#### <a name="resolutions"></a>Résolutions

- Réinstallez l’Agent OMS pour Linux à l’aide de la commande suivante avec l’option `-v` activée. Cela autorise une sortie détaillée de l’agent qui se connecte via le proxy au Service OMS.
  - `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  - Consultez la documentation relative au proxy d’OMS fournie dans la rubrique [Configuring the agent for use with an HTTP proxy server](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server) (Configuration de l’agent pour une utilisation avec un serveur proxy HTTP).
- Vérifiez que les points de terminaison du Service OMS suivants sont approuvés.

Ressource de l'agent | Ports
---- | ----
&#42;.ods.opinsights.azure.com | Port 443
&#42;.oms.opinsights.azure.com | Port 443
ods.systemcenteradvisor.com | Port 443
&#42;.blob.core.windows.net/ | Port 443

### <a name="a-403-error-is-displayed-when-onboarding"></a>Une erreur 403 s’affiche lors de l’intégration

#### <a name="probable-causes"></a>Causes probables

- La date et l’heure sont incorrectes sur le serveur Linux.
- L’ID et la clé d’espace de travail utilisés sont incorrects.

#### <a name="resolution"></a>Résolution :

- Vérifiez l’heure sur votre serveur Linux avec la commande `date`. Si les données sont postérieures ou antérieures de 15 minutes par rapport à l’heure actuelle, l’intégration échoue. Pour corriger ce problème, mettez à jour de la date et/ou le fuseau horaire de votre serveur Linux.
- La dernière version de l’Agent OMS pour Linux vous avertit si un décalage horaire entraîne l’échec de l’intégration.
- Recommencez l’intégration à l’aide de l’ID et de la clé d’espace de travail corrects. Pour plus d’informations, voir [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Intégration à l’aide de la ligne de commande).

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>Une erreur 500 ou 404 apparaît dans le fichier journal après l’intégration

Il s’agit d’un problème connu qui se produit lors du premier chargement de données Linux dans un espace de travail OMS. Cela n’affecte pas les données envoyées et ne génère pas d’autres problèmes. Vous pouvez ignorer les erreurs lors de l’intégration initiale.

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>Les données Nagios n’apparaissant pas dans le portail OMS

#### <a name="probable-causes"></a>Causes probables
- L’utilisateur omsagent n’a pas les autorisations nécessaires pour lire le fichier journal Nagios.
- Les sections de source et de filtre Nagios sont toujours commentées dans le fichier omsagent.conf.

#### <a name="resolutions"></a>Résolutions

- Ajoutez l’utilisateur omsagent afin de lire à partir du fichier Nagios. Pour plus d’informations, voir [Nagios alerts](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts) (Alertes Nagios).
- Dans l’Agent OMS pour le fichier de configuration générale de Linux sur `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, assurez-vous que les commentaires ont été supprimés **des deux** sections de source et de filtre Nagios, comme dans l’exemple suivant.

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesn't-appear-in-the-oms-portal"></a>Les données Linux n’apparaissent pas dans le portail OMS

#### <a name="probable-causes"></a>Causes probables

- L’intégration au Service OMS a échoué.
- La connexion au Service OMS est bloquée.
- Les données de l’Agent OMS pour Linux sont sauvegardées.

#### <a name="resolutions"></a>Résolutions

- Vérifiez que l’intégration au Service OMS a réussi en vous assurant que le fichier `/etc/opt/microsoft/omsagent/conf/omsadmin.conf` existe.
- Recommencez l’intégration à l’aide de la ligne de commande omsadmin.sh. Pour plus d’informations, voir [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Intégration à l’aide de la ligne de commande).
- Si vous utilisez un proxy, suivez les étapes de résolution de problèmes de proxy décrites plus haut.
- Dans certains cas, lorsque l’Agent OMS pour Linux ne peut pas communiquer avec le Service OMS, les données de l’Agent sont sauvegardées à la taille de mémoire tampon saturée de 50 Mo. Redémarrez l’Agent OMS pour Linux en exécutant la commande `service omsagent restart` ou `systemctl restart omsagent`.
  >[AZURE.NOTE] Ce problème est résolu dans l’Agent version 1.1.0-28 et versions ultérieures.

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>La configuration du compteur de performances Syslog Linux n’est pas appliquée dans le portail OMS

#### <a name="probable-causes"></a>Causes probables

- L’agent de configuration de l’Agent OMS pour Linux n’a pas pu extraire la configuration la plus récente à partir du portail OMS.
- Les paramètres modifiés dans le portail n’ont pas été appliqués.

#### <a name="resolutions"></a>Résolutions

`omsconfig` est l’agent de configuration de l’Agent OMS pour Linux qui récupère les changements de configuration du portail OMS toutes les 5 minutes. Cette configuration est ensuite appliquée aux fichiers de configuration de l’Agent OMS pour Linux dans `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

- Dans certains cas, l’agent de configuration de l’Agent OMS pour Linux n’est pas en mesure de communiquer avec le service de configuration du portail, de sorte que la configuration la plus récente n’est pas appliquée.
- Vérifiez que l’agent `omsconfig` est installé avec les éléments suivants :
  - `dpkg --list omsconfig` ou `rpm -qi omsconfig`
  - S’il n’est pas installé, réinstallez la dernière version de l’Agent OMS pour Linux.

- Vérifiez que l’agent `omsconfig` peut communiquer avec le Service OMS.
  - Exécutez la commande `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.
    - La commande ci-dessus retourne la configuration que l’agent récupère à partir du portail, dont les paramètres Syslog, les compteurs de performances Linux et les journaux personnalisés
    - En cas d’échec de la commande ci-dessus, exécutez la commande `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Cette commande force l’agent omsconfig à communiquer avec le Service OMS pour récupérer la configuration la plus récente.


### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>Les données du journal Linux personnalisé n’apparaissent pas sur le portail OMS

#### <a name="probable-causes"></a>Causes probables

- L’intégration au Service OMS a échoué.
- Le paramètre **Appliquer la configuration suivante à mes serveurs Linux** n’a pas été sélectionné.
- L’agent omsconfig n’a pas récupéré le dernier journal personnalisé sur le portail.
- Le programme `omsagent` ne peut pas accéder au journal personnalisé en raison d’un problème d’autorisation ou parce que le programme `omsagent` est introuvable. Dans ce cas, la sortie suivante s’affiche :
  - `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  - `[DATETIME] [error]: file not accessible by omsagent.`
- Il s’agit d’un problème connu lié à la condition de concurrence, qui a été résolu dans l’Agent OMS pour Linux version 1.1.0-217.

#### <a name="resolutions"></a>Résolutions
- Vérifiez que vous avez correctement effectué l’intégration en déterminant si le fichier `/etc/opt/microsoft/omsagent/conf/omsadmin.conf` existe.
  - Si nécessaire, recommencez l’intégration à l’aide de la ligne de commande omsadmin.sh. Pour plus d’informations, voir [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Intégration à l’aide de la ligne de commande).
- Sur le portail OMS, dans **Paramètres** sous l’onglet **Données**, vérifiez que l’option **Appliquer la configuration suivante à mes serveurs Linux** est activée.  
  ![appliquer la configuration](./media/log-analytics-linux-agents/customloglinuxenabled.png)

- Vérifiez que l’agent `omsconfig` peut communiquer avec le Service OMS.
  - Exécutez la commande `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.
  - La commande ci-dessus retourne la configuration que l’agent récupère à partir du portail, dont les paramètres Syslog, les compteurs de performances Linux et les journaux personnalisés.
  - En cas d’échec de la commande ci-dessus, exécutez la commande `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Cette commande force l’agent omsconfig à communiquer avec le Service OMS pour récupérer la configuration la plus récente.


Au lieu que l’utilisateur de l’Agent OMS pour Linux opère en tant qu’utilisateur `root` privilégié, l’Agent OMS pour Linux opère en tant qu’utilisateur `omsagent` . Dans la plupart des cas, une autorisation explicite doit être accordée à l’utilisateur pour la lecture de certains fichiers.

Pour accorder l’autorisation à l’utilisateur `omsagent`, exécutez les commandes suivantes :

1. Ajoutez l’utilisateur `omsagent` à un groupe spécifique avec `sudo usermod -a -G <GROUPNAME> <USERNAME>`.
2. Accordez l’accès en lecture universel au fichier requis avec `sudo chmod -R ugo+rw <FILE DIRECTORY>`.

Il existe un problème connu lié à la condition de concurrence, qui a été résolu dans l’Agent OMS pour Linux version 1.1.0-217. Après mise à jour vers le dernier agent, exécutez la commande suivante pour obtenir la dernière version du plug-in de sortie :

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/conf/omsagent.conf
```

## <a name="known-limitations"></a>Limites connues
Consultez les sections suivantes pour en savoir plus sur les limites actuelles de l’Agent OMS pour Linux.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Pour les machines virtuelles Linux s’exécutant dans Azure, des étapes supplémentaires peuvent être nécessaires pour permettre la collecte des données par Azure Diagnostics et Operations Management Suite. **version 2.2** d’Extension Diagnostics pour Linux est nécessaire pour la compatibilité avec l’Agent OMS pour Linux.

Pour plus d’informations sur l’installation et la configuration de l’Extension Diagnostics pour Linux, voir [Utiliser la commande de l’interface de ligne de commande Azure afin d’activer l’extension de diagnostic Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension).

**Mise à niveau de l’Extension Diagnostics pour Linux 2.0 vers 2.2 avec l’interface de ligne de commande Azure en mode ASM :**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

Ces exemples de commandes font référence à un fichier nommé PrivateConfig.json. Le format de ce fichier doit ressembler à l’exemple suivant.

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>Sysklog n’est pas pris en charge

rsyslog ou syslog-ng est requis pour collecter les messages syslog. Le démon syslog par défaut sur la version 5 de Red Hat Enterprise Linux, CentOS et Oracle Linux (sysklog) ne prend pas en charge la collecte des événements syslog. Pour collecter les données syslog avec cette version de ces distributions, le démon rsyslog doit être installé et configuré à la place de sysklog. Pour plus d’informations sur le remplacement de sysklog par rsyslog, consultez [Install the newly built rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM).

## <a name="next-steps"></a>Étapes suivantes

- [Ajoutez des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et collecter des données.
- Familiarisez-vous avec les [recherches de journal](log-analytics-log-searches.md) pour afficher les informations détaillées collectées par les solutions.
- Utilisez les [tableaux de bord](log-analytics-dashboards.md) pour enregistrer et afficher vos propres recherches personnalisées.



<!--HONumber=Oct16_HO2-->


