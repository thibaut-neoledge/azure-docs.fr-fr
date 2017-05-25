---
title: Collecter et analyser les messages Syslog dans OMS Log Analytics | Microsoft Docs
description: "Syslog est un protocole de journalisation d’événements commun à Linux.   Cet article décrit comment configurer la collecte de messages Syslog dans Log Analytics et des détails des enregistrements qu’ils créent dans le référentiel OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 653696779e612726ed5b75829a5c6ed2615553d7
ms.openlocfilehash: 6e92a79c0b7ea35f110c779922255d6ddc93ed7c
ms.contentlocale: fr-fr
ms.lasthandoff: 01/24/2017


---
# <a name="syslog-data-sources-in-log-analytics"></a>Sources de données Syslog dans Log Analytics
Syslog est un protocole de journalisation d’événements commun à Linux.  Les applications envoient les messages qui peuvent être stockés sur l’ordinateur local ou remis à un collecteur Syslog.  Lorsque l’agent OMS pour Linux est installé, il configure le démon Syslog local pour qu’il transfère des messages à l’agent.  L’agent envoie ensuite le message à Log Analytics où un enregistrement correspondant est créé dans le référentiel OMS.  

> [!NOTE]
> Log Analytics prend en charge la collecte de messages envoyés par rsyslog ou syslog-ng. Le démon syslog par défaut sur la version 5 de Red Hat Enterprise Linux, CentOS et Oracle Linux (sysklog) ne prend pas en charge la collecte des événements syslog. Pour collecter les données syslog avec cette version de ces distributions, le [démon rsyslog](http://rsyslog.com) doit être installé et configuré à la place de syslog.
> 
> 

![Collecte de messages Syslog](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Configuration de Syslog
L’agent OMS pour Linux collecte uniquement les événements avec les installations et les niveaux de gravité spécifiés dans sa configuration.  Vous pouvez configurer Syslog avec le portail OMS ou en gérant les fichiers de configuration sur vos agents Linux.

### <a name="configure-syslog-in-the-oms-portal"></a>Configurer Syslog dans le portail OMS
Configurez Syslog à partir du [menu Données dans Paramètres Log Analytics](log-analytics-data-sources.md#configuring-data-sources).  Cette configuration est remise au fichier de configuration sur chaque agent Linux.

Vous pouvez ajouter une nouvelle installation en tapant son nom et en cliquant sur **+**.  Pour chaque installation, seuls les messages avec les niveaux de gravité sélectionnés seront collectés.  Vérifiez les niveaux de gravité de l’installation que vous souhaitez collecter.  Vous ne pouvez pas fournir de critères supplémentaires pour filtrer les messages.

![Configurer les messages Syslog](media/log-analytics-data-sources-syslog/configure.png)

Par défaut, toutes les modifications de configuration sont automatiquement transmises à l’ensemble des agents.  Si vous souhaitez configurer Syslog manuellement sur chaque agent Linux, décochez la case *Appliquer la configuration ci-dessous à mes machines Linux*.

### <a name="configure-syslog-on-linux-agent"></a>Configurer l’agent Syslog sur Linux
Lorsque [l’agent OMS est installé sur un client Linux](log-analytics-linux-agents.md), il installe un fichier de configuration syslog par défaut qui définit l’installation et le niveau de gravité des messages qui sont collectés.  Vous pouvez modifier ce fichier pour changer la configuration.  Le fichier de configuration est différent selon le démon Syslog installé par le client.

> [!NOTE]
> Si vous modifiez cette configuration, vous devez redémarrer le démon syslog pour que les modifications prennent effet.
> 
> 

#### <a name="rsyslog"></a>rsyslog
Le fichier de configuration de rsyslog se trouve dans **/etc/rsyslog.d/95-omsagent.conf**.  Voici son contenu par défaut.  Collecte les messages syslog envoyés à partir de l’agent local pour toutes les installations avec un niveau Avertissement ou supérieur.

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

Vous pouvez supprimer une installation en supprimant sa section du fichier de configuration.  Vous pouvez limiter les niveaux de gravité qui sont collectés pour une installation donnée en modifiant l’entrée de cette installation.  Par exemple, pour limiter l’installation de l’utilisateur aux messages avec un niveau Erreur ou supérieur, il vous faudrait modifier cette ligne du fichier de configuration pour obtenir la ligne suivante :

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
Le fichier de configuration de syslog-ng est situé dans **/etc/syslog-ng/syslog-ng.conf**.  Voici son contenu par défaut.  Collecte les messages syslog envoyés à partir de l’agent local pour toutes les installations et tous les niveaux de gravité.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Vous pouvez supprimer une installation en supprimant sa section du fichier de configuration.  Vous pouvez limiter les niveaux de gravité qui sont collectés pour une installation donnée en les supprimant de sa liste.  Par exemple, pour limiter l’installation de l’utilisateur aux messages de niveau Alerte et Critique, il vous faudrait modifier cette section du fichier de configuration pour obtenir la section suivante :

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="changing-the-syslog-port"></a>Modification du port Syslog
L’agent OMS écoute les messages Syslog sur le client local sur le port 25224.  Vous pouvez modifier ce port en ajoutant la section suivante au fichier de configuration de l’agent OMS situé à l’emplacement **/etc/opt/microsoft/omsagent/conf/omsagent.conf**.  Remplacez 25224 dans l’entrée **port** par le numéro de port que vous souhaitez.  Notez que vous devez également modifier le fichier de configuration pour que le démon Syslog envoie des messages vers ce port.

    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.syslog
    </source>


## <a name="data-collection"></a>Collecte des données
L’agent OMS écoute les messages Syslog sur le client local sur le port 25224. Le fichier de configuration du démon Syslog transfère les messages Syslog envoyés à partir de l’application à ce port, où ils sont collectés par Log Analytics.

## <a name="syslog-record-properties"></a>Propriétés d’enregistrement Syslog
Les enregistrements Syslog sont de type **Syslog** et leurs propriétés sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Ordinateur |Ordinateur sur lequel l’événement a été collecté. |
| Facility |Définit la partie du système qui a généré le message. |
| HostIP |Adresse IP du système qui envoie le message. |
| HostName |Nom du système qui envoie le message. |
| SeverityLevel |Niveau de gravité de l’événement. |
| SyslogMessage |Texte du message. |
| ProcessID |ID du processus qui a généré le message. |
| EventTime |Date et heure de génération de l’événement. |

## <a name="log-queries-with-syslog-records"></a>Requêtes de journaux avec des enregistrements Syslog
Le tableau suivant fournit plusieurs exemples de requêtes de journaux qui extraient des enregistrements Syslog.

| Requête | Description |
|:--- |:--- |
| Type = Syslog |Tous les Syslog. |
| Type=Syslog SeverityLevel=error |Tous les enregistrements Syslog avec le niveau de gravité Erreur. |
| Type=Syslog &#124; measure count() by Computer |Nombre d’enregistrements Syslog par ordinateur. |
| Type=Syslog &#124; measure count() by Facility |Nombre d’enregistrements Syslog par installation. |

## <a name="next-steps"></a>Étapes suivantes
* Découvrez les [recherches de journal](log-analytics-log-searches.md) pour analyser les données collectées dans des sources de données et des solutions. 
* Utilisez les [Champs personnalisés](log-analytics-custom-fields.md) pour analyser les données des enregistrements syslog dans des champs individuels.
* [Configurez les agents Linux](log-analytics-linux-agents.md) pour qu’ils collectent d’autres types de données. 


