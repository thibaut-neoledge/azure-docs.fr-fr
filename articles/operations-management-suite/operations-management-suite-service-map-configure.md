---
title: Configuration de Carte de service dans Operations Management Suite (OMS) | Microsoft Docs
description: "Carte de service est une solution OMS (Operations Management Suite) qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services.  Cet article fournit des informations sur le déploiement de Carte de service dans votre environnement et son utilisation dans divers scénarios."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: 1ce47043f85e30f616c8b22e1107b192d4962d8a
ms.openlocfilehash: 73c35da427f1e2080ab6fdd086d3168dad495415
ms.lasthandoff: 02/16/2017


---
# <a name="configuring-service-map-solution-in-operations-management-suite-oms"></a>Configuration de la solution Carte de service dans Operations Management Suite (OMS)
Carte de service détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Elle vous permet d’afficher les serveurs comme des systèmes interconnectés qui fournissent des services critiques.  Carte de service affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée à TCP, sans configuration requise autre que l’installation d’un agent.

Cet article décrit en détail la configuration de Carte de service et de l’intégration des agents.  Pour plus d’informations sur l’utilisation de Carte de service, consultez [Utilisation de la solution Carte de service dans Operations Management Suite (OMS)](operations-management-suite-service-map.md)


## <a name="connected-sources"></a>Sources connectées
Carte de service obtient ses données à partir de l’agent de dépendances Microsoft.  L’agent de dépendances dépend de l’agent OMS pour ses connexions à OMS.  Cela signifie qu’un serveur doit disposer de l’agent OMS installé et configuré avant que l’agent de dépendances puisse être installé.  Le tableau suivant décrit les sources connectées qui sont prises en charge par la solution Carte de service.

| Source connectée | Pris en charge | Description |
|:--|:--|:--|
| Agents Windows | Oui | Carte de service analyse et collecte des données à partir des ordinateurs agents Windows.  <br><br>Outre [l’agent OMS](../log-analytics/log-analytics-windows-agents.md), les agents Windows nécessitent l’agent de dépendances Microsoft.  Pour obtenir la liste complète des versions de système d’exploitation, consultez [Systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Agents Linux | Oui | Carte de service analyse et collecte des données à partir des ordinateurs agents Linux.  <br><br>Outre [l’agent OMS](../log-analytics/log-analytics-linux-agents.md), les agents Linux nécessitent l’agent de dépendances Microsoft.  Pour obtenir la liste complète des versions de système d’exploitation, consultez [Systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Groupe d’administration SCOM | Oui | Carte de service analyse et collecte des données à partir des agents Windows et Linux dans un [groupe d’administration System Center Operations Manager (SCOM)](../log-analytics/log-analytics-om-agents.md) connecté. <br><br>Une connexion directe entre OMS et l’ordinateur agent SCOM est requise. Les données sont envoyées directement du groupe d’administration au référentiel OMS.|
| Compte Azure Storage | Non | Carte de service collecte les données à partir des ordinateurs agents. Aucune donnée n’est collectée à partir du stockage Azure. |

Notez que Carte de service prend en charge uniquement les plateformes 64 bits.

Sous Windows, Microsoft Monitoring Agent (MMA) est utilisé par SCOM et OMS pour collecter et envoyer des données d’analyse.  (Cet agent est appelé Agent SCOM, Agent OMS, MMA ou Agent Direct selon le contexte.)  SCOM et OMS fournissent différentes versions prêtes à l’emploi de MMA, mais chacune de ces versions peut faire référence à SCOM et/ou OMS.  Sur Linux, l’agent OMS pour Linux collecte et envoie les données d’analyse à OMS.  Vous pouvez utiliser Carte de service sur des serveurs avec des agents directs OMS ou sur des serveurs qui sont attachés à OMS via des groupes d’administration SCOM.  Dans cette documentation, nous faisons référence à tous les agents (Linux ou Windows, connectés à un groupe d’administration SCOM ou directement à OMS) sous le terme « agent OMS », à moins que le nom de déploiement spécifique de l’agent soit nécessaire pour le contexte.

L’agent Carte de service ne transmet pas les données lui-même et il n’est pas nécessaire d’apporter des modifications au pare-feu ni aux ports.  Les données Carte de service sont toujours transmises par l’agent OMS à OMS, directement ou via la passerelle OMS.

![Agents Carte de service](media/oms-service-map/agents.png)

Si vous êtes un client SCOM avec un groupe d’administration connecté à OMS :

- Si vos agents SCOM peuvent accéder à internet pour se connecter à OMS, aucune configuration supplémentaire n’est requise.  
- Si vos agents SCOM ne peuvent pas accéder à OMS sur Internet, vous devez configurer la passerelle OMS pour qu’elle fonctionne avec SCOM.
  
Si vous utilisez l’agent direct OMS, vous devez le configurer pour qu’il se connecte à OMS ou à votre passerelle OMS.  La passerelle OMS peut être téléchargée à l’adresse [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Éviter les données en double

Si vous êtes un client SCOM, vous ne devez pas configurer vos agents SCOM pour qu’ils communiquent directement avec OMS, sinon, les données sont signalées à deux reprises.  Dans Carte de service, les ordinateurs apparaissent alors deux fois dans la liste des ordinateurs.

La configuration d’OMS doit se produire uniquement dans l’un des emplacements suivants :

- Volet Operations Management Suite de la console SCOM pour les ordinateurs gérés
- Configuration Azure Operational Insights dans les propriétés MMA

L’utilisation de ces deux configurations avec le *même* espace de travail dans chacune d’elles entraîne une duplication des données. L’utilisation des deux configurations avec *différents* espaces de travail peut entraîner un conflit de configurations (une avec la solution Carte de service activée et l’autre sans) qui peut empêcher la transmission complète des données à Carte de service.  

Même si l’ordinateur lui-même n’est pas spécifié dans la configuration OMS de la console SCOM, si un groupe d’instances comme « Groupe d’instances Windows Server » est actif, il se peut que l’ordinateur reçoive toujours la configuration OMS via SCOM.



## <a name="management-packs"></a>Packs d’administration
Quand la solution Carte de service est activée dans un espace de travail OMS, un pack d’administration de 300 Ko est envoyé à tous les agents Microsoft Monitoring Agent dans cet espace de travail.  Si vous utilisez des agents SCOM dans un [groupe d’administration connecté](../log-analytics/log-analytics-om-agents.md), le pack d’administration Carte de service est déployé à partir de SCOM.  Si les agents sont directement connectés, le pack d’administration est fourni par OMS.

Le pack d’administration se nomme Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Il est enregistré dans le répertoire *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\*.  La source de données utilisée par le pack d’administration est *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configuration
En plus des ordinateurs Windows et Linux qui disposent d’un agent installé et connecté à OMS, le programme d’installation de l’agent de dépendances doit être téléchargé à partir de la solution Carte de service, puis installé comme racine ou admin sur chaque serveur géré.  Une fois que l’agent Carte de service est installé sur un serveur qui communique avec OMS, les mappages de dépendances Carte de service apparaissent dans un délai maximal de 10 minutes.


### <a name="migrating-from-bluestripe-factfinder"></a>Migration à partir de BlueStripe FactFinder
Carte de service fournit la technologie BlueStripe à OMS en plusieurs phases. FactFinder est toujours pris en charge pour les clients existants, mais n’est plus disponible pour un achat individuel.  Cette version préliminaire de l’agent de dépendances ne peut communiquer qu’avec OMS.  Si vous êtes un client FactFinder, identifiez un ensemble de serveurs de test pour Carte de service qui ne sont pas gérés par FactFinder. 

### <a name="download-the-dependency-agent"></a>Télécharger l’agent de dépendances
Outre les agents MMA (Microsoft Management Agent) et Linux OMS qui établissent la connexion entre l’ordinateur et l’OMS, tous les ordinateurs analysés par Carte de service doivent avoir l’agent de dépendances installé.  Sous Linux, l’agent OMS pour Linux doit être installé avant l’agent de dépendances. 

![Vignette Carte de service](media/oms-service-map/additional_configuration.png)

Pour télécharger l’agent de dépendances, cliquez sur **Configurer une solution** dans la vignette **Carte de service** pour ouvrir le panneau **Agent de dépendances**.  Le panneau Agent de dépendances comporte des liens pour les agents Windows et Linux. Consultez les sections suivantes pour plus d’informations sur l’installation de l’agent sur différents systèmes.

### <a name="install-the-dependency-agent"></a>Installer l’agent de dépendances

#### <a name="microsoft-windows"></a>Microsoft Windows
Des privilèges d’administrateur sont requis pour installer ou désinstaller l’agent.

L’agent de dépendances est installé sur les ordinateurs Windows avec InstallDependencyAgent-Windows.exe. Si vous exécutez ce programme sans aucune option, il démarre un Assistant que vous pouvez suivre pour une installation interactive.  

Utilisez les étapes suivantes pour installer l’agent de dépendances sur chaque ordinateur Windows :

1.    Vérifiez que l’agent OMS est installé en suivant les instructions disponibles dans Connecter les ordinateurs directement à OMS.
2.    Téléchargez l’agent Windows et exécutez-le avec la commande suivante : <br>*InstallDependencyAgent-Windows.exe*
3.    Suivez les instructions de l’Assistant pour installer l’agent.
4.    Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux. Sur les agents Windows, le répertoire des journaux est *C:\Program Files\Microsoft Dependency Agent\logs*. 

L’agent de dépendances pour Windows peut être désinstallé par un administrateur via le Panneau de configuration.


#### <a name="linux"></a>Linux
L’accès racine est requis pour installer ou configurer l’agent.

L’agent de dépendances est installé sur les ordinateurs Linux avec InstallDependencyAgent-Linux64.bin, un script shell avec un fichier binaire à extraction automatique. Vous pouvez exécuter le fichier avec sh ou ajouter des autorisations d’exécution au fichier lui-même.
 
Utilisez les étapes suivantes pour installer l’agent de dépendances sur chaque ordinateur Linux :

1.    Vérifiez que l’agent OMS est installé en suivant les instructions disponibles dans [Connecter et gérer les données à partir d’ordinateurs Linux.  L’agent OMS doit être installé avant l’agent de dépendances Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.    Installez l’agent de dépendances Linux en tant que racine à l’aide de la commande suivante :<br>*sh InstallDependencyAgent-Linux64.bin*.
3.    Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux. Sur les agents Linux, le répertoire des journaux est */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Désinstaller l’agent de dépendances sur Linux
Pour désinstaller complètement l’agent de dépendances de Linux, vous devez supprimer l’agent lui-même et le connecteur qui est installé automatiquement avec lui.  Vous pouvez désinstaller les deux avec la commande ci-dessous :

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>Installation à partir d’une ligne de commande
La section précédente fournit des conseils sur l’installation de l’agent Dependency Monitor à l’aide des options par défaut.  La section ci-après fournit des conseils sur l’installation de l’agent à partir d’une ligne de commande à l’aide d’options personnalisées.

#### <a name="windows"></a>Windows
Utilisez les options dans le tableau suivant pour effectuer l’installation à partir d’une ligne de commande. Pour afficher la liste des indicateurs d’installation, exécutez le programme d’installation avec l’indicateur /? comme suit.

    InstallDependencyAgent-Windows.exe /?

| Indicateur | Description |
|:--|:--|
| /S | Effectuez une installation silencieuse sans invite utilisateur. |

Les fichiers de l’agent de dépendances Windows sont placés dans le répertoire *C:\Program Files\Microsoft Dependency Agent* par défaut.


#### <a name="linux"></a>Linux
Utilisez les options dans le tableau suivant pour effectuer l’installation. Pour afficher la liste des indicateurs d’installation, exécutez le programme d’installation avec l’indicateur -help comme suit.

    InstallDependencyAgent-Linux64.bin -help

| Indicateur    Description
|:--|:--|
| -s | Effectuez une installation silencieuse sans invite utilisateur. |
| --check | Vérifie les autorisations et le système d’exploitation, mais n’installe pas l’agent. |

Les fichiers de l’agent de dépendances sont placés dans les répertoires suivants :

| Fichiers | Lieu |
|:--|:--|
| Fichiers principaux | /opt/microsoft/dependency-agent |
| Fichiers journaux | /var/opt/microsoft/dependency-agent/log |
| Fichiers de configuration | /etc/opt/microsoft/dependency-agent/config |
| Exécutables du service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Fichiers de stockage binaires | /var/opt/microsoft/dependency-agent/storage |



## <a name="troubleshooting"></a>Résolution des problèmes
Si vous rencontrez des problèmes avec Carte de service, vous pouvez collecter des informations de dépannage à partir de plusieurs composants en utilisant les informations suivantes.

### <a name="windows-agents"></a>Agents Windows

#### <a name="microsoft-dependency-agent"></a>Agent de dépendances Microsoft
Pour générer des données de dépannage à partir de l’agent de dépendances, ouvrez une invite de commandes en tant qu’administrateur et exécutez le script CollectDependencyAgentData.vbs à l’aide de la commande suivante.  Vous pouvez ajouter l’indicateur --help pour afficher des options supplémentaires.

    cd C:\Program Files\Microsoft Dependency Agent\scripts
    cscript CollectDependencyData.vbs

Le package de données de prise en charge est enregistré dans le répertoire %PROFIL_UTILISATEUR% de l’utilisateur actuel.  Vous pouvez utiliser l’option --file <filename> pour l’enregistrer dans un emplacement différent.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Pack d’administration de l’agent de dépendances Microsoft pour MMA
Le pack d’administration de l’agent de dépendances s’exécute au sein de Microsoft Management Agent.  Il reçoit les données de l’agent de dépendances et les transmet au service cloud Carte de service.
  
Vérifiez que le pack d’administration est téléchargé en effectuant les étapes suivantes :

1.    Recherchez le fichier nommé Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp dans C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.  
2.    Si le fichier n’est pas présent et que l’agent est connecté à un groupe d’administration SCOM, vérifiez qu’il a été importé dans SCOM en passant en revue les packs d’administration dans l’espace de travail Administration de la console Opérateur.

Le pack d’administration Carte de service enregistre les événements dans le journal des événements Windows Operations Manager.  Le journal peut être [recherché dans OMS](../log-analytics/log-analytics-log-searches.md) via la solution du journal système, où vous pouvez configurer les fichiers journaux à télécharger.  Si les événements de débogage sont activés, ils sont enregistrés dans le journal des événements d’application, avec la source d’événements *ADMConnector*.

#### <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Pour collecter des traces de diagnostic, ouvrez une fenêtre d’invite de commandes en tant qu’administrateur, puis exécutez les commandes suivantes : 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Les traces sont enregistrées dans C:\Windows\Logs\OpsMgrTrace.  Vous pouvez arrêter le traçage avec StopTracing.cmd.


### <a name="linux-agents"></a>Agents Linux

#### <a name="microsoft-dependency-agent"></a>Agent de dépendances Microsoft
Pour générer des données de dépannage à partir de l’agent de dépendances, connectez-vous avec un compte disposant des privilèges sudo ou racine et exécutez la commande suivante.  Vous pouvez ajouter l’indicateur --help pour afficher des options supplémentaires.

    /opt/microsoft/dependency-agent/lib/scripts/collect-dependency-agent-data.sh

Le package de données de prise en charge est enregistré dans /var/opt/microsoft/dependency-agent/log (avec des privilèges racine) sous le répertoire d’installation de l’agent ou dans le répertoire de l’utilisateur qui exécute le script (avec des privilèges non racine).  Vous pouvez utiliser l’option --file <filename> pour l’enregistrer dans un emplacement différent.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Plug-in Fluentd de l’agent de dépendances Microsoft pour Linux
Le plug-in Fluentd de l’agent de dépendances s’exécute à l’intérieur de l’agent Linux OMS.  Il reçoit les données de l’agent de dépendances et les transmet au service cloud Carte de service.  

Les journaux sont enregistrés dans les deux fichiers suivants.

- /var/opt/microsoft/omsagent/log/omsagent.log
- /var/log/messages

#### <a name="oms-agent-for-linux"></a>Agent OMS pour Linux
Une ressource de résolution des problèmes de connexion des serveurs Linux à OMS peut être trouvée ici : [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

Les journaux de l’agent OMS pour Linux sont situés dans le répertoire */var/opt/microsoft/omsagent/log/*.  

Les journaux pour omsconfig (agent de configuration) sont situés dans le répertoire */var/opt/microsoft/omsconfig/log/*.
 
Les journaux des composants OMI et SCX qui fournissent des données de mesures de performances se trouvent dans les répertoires */var/opt/omi/log/* et */var/opt/microsoft/scx/log*.


## <a name="data-collection"></a>Collecte des données
Vous pouvez vous attendre à ce que chaque agent transmette par jour environ 25 Mo selon la complexité des dépendances du système.  Les données de dépendance Carte de service sont envoyées par chaque agent toutes les 15 secondes.  

L’agent de dépendance consomme généralement 0,1 % de la mémoire système et 0,1 % du processeur système.


## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Les sections suivantes répertorient les systèmes d’exploitation pris en charge par l’agent de dépendances.   Les architectures&32; bits ne sont prises en charge par aucun système d’exploitation.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows Desktop
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux et Oracle Linux (avec noyau RHEL)
- Seules les versions du noyau SMP Linux et par défaut sont prises en charge.
- Les versions de noyau non standard, telles que PAE et Xen, ne sont prises en charge par aucune distribution Linux. Par exemple, un système avec la chaîne de version « 2.6.16.21-0.8-xen » n’est pas pris en charge.
- Les noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.
- Centos Plus kernel n’est pas pris en charge.
- Oracle Unbreakable Kernel (UEK) est traité dans une autre section plus bas.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Version du SE | Version du noyau |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Version du SE | Version du noyau |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Version du SE | Version du noyau |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux avec noyau incassable (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Version du SE | Version du noyau
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Version du SE | Version du noyau
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Version du SE | Version du noyau
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Version du SE | Version du noyau
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation
Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Carte de service. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service Carte de service. Les données incluent des informations sur la configuration de votre logiciel (comme le système d’exploitation et la version) et également l’adresse IP, le nom DNS et le nom du poste de travail afin de fournir des fonctionnalités de dépannage précises et efficaces. Nous ne collectons pas votre nom, votre adresse, ni vos autres coordonnées.

Pour plus d’informations sur l’utilisation et la collecte de données, consultez la [déclaration de confidentialité Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [utiliser Carte de service](operations-management-suite-service-map.md) une fois le déploiement et la configuration effectués.

