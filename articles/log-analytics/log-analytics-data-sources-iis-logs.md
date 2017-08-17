---
title: Journaux IIS dans Log Analytics | Microsoft Docs
description: "Internet Information Services (IIS) enregistre l'activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par Log Analytics.  Cet article décrit comment configurer la collecte des journaux IIS et des détails des enregistrements qu'ils créent dans le référentiel OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 2114bdafb3b9fe2eb0632271840b8b70a76d10f1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/28/2017

---
# <a name="iis-logs-in-log-analytics"></a>Journaux IIS dans Log Analytics
Internet Information Services (IIS) enregistre l'activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par Log Analytics.  

![Journaux IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configuration de journaux IIS
Log Analytics regroupant les entrées des fichiers journaux créés par IIS, vous devez [configurer IIS pour la journalisation](https://technet.microsoft.com/library/hh831775.aspx).

Log Analytics prend en charge uniquement les fichiers journaux IIS stockés au format W3C, et ne prend pas en charge les champs personnalisés ou IIS Advanced Logging.  
Log Analytics ne collecte pas de journaux aux formats natifs NCSA ou IIS.

Configurez les journaux IIS dans Log Analytics à partir du [menu Données dans Paramètres Log Analytics](log-analytics-data-sources.md#configuring-data-sources).  Aucune configuration n’est requise autre que la sélection de l’option **Collecter les fichiers journaux IIS au format W3C**.

Quand vous activez la collecte de journaux IIS, nous vous recommandons de configurer le paramètre de substitution de journal IIS sur chaque serveur.

## <a name="data-collection"></a>Collecte des données
Log Analytics collecte les entrées du journal IIS de chaque source connectée toutes les 15 minutes environ.  L’agent enregistre sa position dans chaque journal des événements à partir duquel il collecte.  Si l’agent est mis hors connexion, Log Analytics collecte les événements à partir de la position où il s’était arrêté, même si ces événements ont été créés lorsque l’agent était hors connexion.

## <a name="iis-log-record-properties"></a>Propriétés d’enregistrement de journal IIS
Les enregistrements de journal IIS sont de type **W3CIISLog** et leurs propriétés sont décrites dans le tableau suivant :

| Propriété | Description |
|:--- |:--- |
| Ordinateur |Nom de l'ordinateur à partir duquel l'événement a été collecté. |
| cIP |Adresse IP du client. |
| csMethod |Méthode de la requête, par exemple GET ou POST. |
| csReferer |Site à partir duquel l'utilisateur a suivi un lien vers le site actuel. |
| csUserAgent |Type de navigateur du client. |
| csUserName |Nom de l'utilisateur authentifié qui a accédé au serveur. Les utilisateurs anonymes sont indiqués par un trait d'union. |
| csUriStem |Cible de la requête, par exemple une page web. |
| csUriQuery |Requête, le cas échéant, que le client tentait d'effectuer. |
| ManagementGroupName |Nom du groupe d’administration pour les agents Operations Manager.  Pour les autres agents, il s’agit d’AOI-\<workspace ID\> |
| RemoteIPCountry |Pays de l'adresse IP du client. |
| RemoteIPLatitude |Latitude de l'adresse IP du client. |
| RemoteIPLongitude |Longitude de l'adresse IP du client. |
| scStatus |Code d'état HTTP. |
| scSubStatus |Code d'erreur du sous-état. |
| scWin32Status |Code d’état Windows. |
| sIP |Adresse IP du serveur web. |
| SourceSystem |OpsMgr |
| sPort |Port sur le serveur auquel client est connecté. |
| sSiteName |Nom du site IIS. |
| TimeGenerated |Date et heure de consignation de l'entrée. |
| TimeTaken |Délai de traitement de la requête en millisecondes. |

## <a name="log-searches-with-iis-logs"></a>Recherches de journaux avec les journaux IIS
Le tableau suivant fournit plusieurs exemples de requêtes de journaux qui extraient des enregistrements de journaux IIS.

| Interroger | Description |
|:--- |:--- |
| Type=W3CIISLog |Tous les enregistrements de journaux IIS. |
| Type=W3CIISLog scStatus=500 |Tous les enregistrements de journaux IIS dont l’état renvoyé est 500. |
| Type=W3CIISLog &#124; Measure count() by cIP |Nombre d’entrées de journaux IIS par adresse IP du client. |
| Type=W3CIISLog csHost="www.contoso.com" &#124; Measure count() by csUriStem |Nombre d’entrées de journaux IIS par URL pour l'hôte www.contoso.com. |
| Type=W3CIISLog &#124; Measure Sum(csBytes) by Computer &#124; top 500000 |Nombre total d'octets reçus par chaque ordinateur IIS. |

>[!NOTE]
> Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête Log Analytics](log-analytics-log-search-upgrade.md), les requêtes ci-dessus sont remplacées par les requêtes ci-dessous.

> | Interroger | Description |
|:--- |:--- |
| W3CIISLog |Tous les enregistrements de journaux IIS. |
| W3CIISLog &#124; où scStatus==500 |Tous les enregistrements de journaux IIS dont l’état renvoyé est 500. |
| W3CIISLog &#124; résumer count() par cIP |Nombre d’entrées de journaux IIS par adresse IP du client. |
| W3CIISLog &#124; où csHost==« www.contoso.com » &#124; résumer count() par csUriStem |Nombre d’entrées de journaux IIS par URL pour l'hôte www.contoso.com. |
| W3CIISLog &#124; résumer sum(csBytes) par ordinateur &#124 ; prendre 500000 |Nombre total d'octets reçus par chaque ordinateur IIS. |

## <a name="next-steps"></a>Étapes suivantes
* Configurez Log Analytics pour collecter d’autres [sources de données](log-analytics-data-sources.md) à analyser.
* Découvrez les [recherches de journaux](log-analytics-log-searches.md) pour analyser les données collectées à partir de sources de données et de solutions.
* Configurez les alertes dans Log Analytics pour être informé de façon proactive des conditions importantes rencontrées dans les journaux IIS.

