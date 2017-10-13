---
title: "Collecte des données JSON personnalisées dans OMS Log Analytics | Documents Microsoft"
description: "Les sources de données JSON personnalisées peuvent être collectées dans Log Analytics à l’aide de l’agent OMS pour Linux.  Ces sources de données personnalisées peuvent être de simples scripts qui renvoient JSON en tant que cURL ou l’un des 300 plug-ins de FluentD. Cet article décrit la configuration requise pour cette collecte de données."
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
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 800ee1269556e7c2d56fbbf2b497c10509b5c78c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>Collecte des sources de données JSON personnalisées à l’aide de l’agent OMS pour Linux dans Log Analytics
Les sources de données JSON personnalisées peuvent être collectées dans Log Analytics à l’aide de l’agent OMS pour Linux.  Ces sources de données personnalisées peuvent être des scripts simples qui renvoient JSON en tant que [cURL](https://curl.haxx.se/) ou l’un des [300 plug-ins de FluentD](http://www.fluentd.org/plugins/all). Cet article décrit la configuration requise pour cette collecte de données.

> [!NOTE]
> L’agent OMS pour Linux v1.1.0-217 et versions ultérieures est requis pour les données JSON personnalisées

## <a name="configuration"></a>Configuration

### <a name="configure-input-plugin"></a>Configuration du plug-in d’entrée

Pour collecter des données JSON dans Log Analytics, ajoutez `oms.api.` au début d’une balise FluentD dans un plug-in d’entrée.

Par exemple, ceci est un fichier de configuration distinct `exec-json.conf` dans `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Cet exemple utilise le plug-in FluentD `exec` pour exécuter une commande cURL toutes les 30 secondes.  La sortie de cette commande est collectée par le plug-in de sortie JSON.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
La propriété du fichier de configuration ajouté sous `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` devra être modifiée à l’aide de la commande suivante.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Configuration du plug-in de sortie 
Ajouter la configuration de plug-in de sortie suivante à la configuration principale dans `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ou en tant que fichier de configuration distinct placé dans `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-oms-agent-for-linux"></a>Redémarrage de l’agent OMS pour Linux
Redémarrez l’agent OMS pour le service Linux à l’aide de la commande suivante.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Sortie
Les données seront collectées dans Log Analytics avec un enregistrement de type `<FLUENTD_TAG>_CL`.

Par exemple, la balise personnalisée `tag oms.api.tomcat` dans Log Analytics avec un enregistrement de type `tomcat_CL`.  Vous pouvez extraire tous les enregistrements de ce type à l’aide de la recherche de journal suivante.

    Type=tomcat_CL

Les sources de données JSON imbriquées sont prises en charge, mais sont indexées en fonction du champ parent. Par exemple, les données JSON suivantes sont renvoyées à partir d’une recherche Log Analytics en tant que `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Étapes suivantes
* Découvrez les [recherches de journal](log-analytics-log-searches.md) pour analyser les données collectées dans des sources de données et des solutions. 
 