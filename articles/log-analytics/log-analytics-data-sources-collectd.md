---
title: "Collecter les données à partir de CollectD dans OMS Log Analytics | Documents Microsoft"
description: "CollectD est un démon Linux open source qui collecte périodiquement les données des applications et des informations de niveau système.  Cet article fournit des informations sur la collecte de données à partir de CollectD dans Log Analytics."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: magoedte
ms.openlocfilehash: a63b15ca5126b45451f0694c9ee75d7b67b1ceaf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Collecter des données à partir de CollectD sur les agents Linux dans Log Analytics
[CollectD](https://collectd.org/) est un démon Linux open source qui collecte périodiquement des mesures de performances à partir d’applications et d’informations de niveau système. Les applications peuvent être, par exemple, la machine virtuelle Java (JVM), le serveur MySQL et Nginx. Cet article fournit des informations sur la collecte des données de performances à partir de CollectD dans Log Analytics.

Vous trouverez la liste complète des plug-ins disponibles dans le [tableau de plug-ins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Vue d’ensemble de CollectD](media/log-analytics-data-sources-collectd/overview.png)

La configuration CollectD suivante est incluse dans l’agent OMS pour Linux pour acheminer des données CollectD vers l’agent OMS pour Linux.

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

En outre, si vous utilisez une version de CollectD antérieure à 5.5, utilisez plutôt la configuration suivante.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

La configuration CollectD utilise le plug-in par défaut`write_http` pour envoyer des données de mesure de performances sur le port 26000 à l’agent OMS pour Linux. 

> [!NOTE]
> Ce port peut être configuré sur un port personnalisé, si nécessaire.

L’agent OMS pour Linux écoute également les mesures CollectD sur le port 26000 et les convertit ensuite en mesures de schéma OMS. L’agent OMS pour la configuration Linux est le suivant : `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Versions prises en charge
- Log Analytics prend actuellement en charge la version 4.8 et les versions ultérieures de CollectD.
- L’agent OMS pour Linux v1.1.0-217 ou version ultérieure est requis pour la collecte des mesures CollectD.


## <a name="configuration"></a>Configuration
Les étapes de base pour configurer la collecte des données CollectD dans Log Analytics sont les suivantes.

1. Configurez CollectD pour qu’il envoie des données à l’agent OMS pour Linux à l’aide du plug-in write_http.  
2. Configurez l’agent OMS pour Linux afin qu’il écoute les données CollectD sur le port approprié.
3. Redémarrez CollectD et l’agent OMS pour Linux.

### <a name="configure-collectd-to-forward-data"></a>Configuration de CollectD pour le transfert des données 

1. Pour acheminer les données CollectD vers l’agent OMS pour Linux, `oms.conf` doit être ajouté au répertoire de configuration de CollectD. La destination de ce fichier dépend de la distribution Linux de votre machine.

    Si votre répertoire de configuration CollectD se trouve dans /etc/collectd.d/ :

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Si votre répertoire de configuration CollectD se trouve dans /etc/collectd/collectd.conf.d/ :

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Pour les versions de CollectD antérieures à 5.5, vous devrez modifier les balises dans `oms.conf`, comme indiqué ci-dessus.
    >

2. Copiez collectd.conf vers le répertoire de configuration omsagent de l’espace de travail souhaité.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Redémarrez CollectD et l’agent OMS pour Linux à l’aide des commandes suivantes.

    sudo service collectd restart  sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>Mesures CollectD pour la conversion de schéma Log Analytics
Pour conserver un modèle cohérent entre les mesures d’infrastructure déjà collectées par l’agent OMS pour Linux et les nouvelles mesures collectées par CollectD, le mappage de schéma suivant est utilisé :

| Champ Mesure CollectD | Champ Log Analytics |
|:--|:--|
| host | Ordinateur |
| plugin | Aucun |
| plugin_instance | Nom de l’instance<br>If **plugin_instance** is *null* then InstanceName="*_Total*" |
| type | ObjectName |
| type_instance | CounterName<br>If **type_instance** is *null* then CounterName=**blank** |
| dsnames[] | CounterName |
| dstypes | Aucun |
| values[] | CounterValue |

## <a name="next-steps"></a>Étapes suivantes
* Découvrez les [recherches de journal](log-analytics-log-searches.md) pour analyser les données collectées dans des sources de données et des solutions. 
* Utilisez les [Champs personnalisés](log-analytics-custom-fields.md) pour analyser les données des enregistrements syslog dans des champs individuels.

