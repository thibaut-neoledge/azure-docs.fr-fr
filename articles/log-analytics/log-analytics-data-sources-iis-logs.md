<properties
   pageTitle="Journaux IIS dans Log Analytics | Microsoft Azure"
   description="Internet Information Services (IIS) enregistre l'activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par Log Analytics. Cet article décrit comment configurer la collecte des journaux IIS et des détails des enregistrements qu'ils créent dans le référentiel OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="bwren" />

# Journaux IIS dans Log Analytics
Internet Information Services (IIS) enregistre l'activité des utilisateurs dans des fichiers journaux qui peuvent être collectés par Log Analytics.

![Journaux IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## Configuration de journaux IIS
Log Analytics collecte les entrées des fichiers journaux créés par IIS, et vous devez donc [configurer IIS pour la journalisation](https://technet.microsoft.com/library/hh831775.aspx) et sélectionne les champs que Log Analytics doit collecter. IIS ne consigne pas tous les champs par défaut : il est conseillé de sélectionner manuellement les champs supplémentaires en plus des valeurs par défaut.

Log Analytics prend uniquement en charge les fichiers journaux IIS stockés au format W3C. Il ne collecte pas les journaux au format natif NCSA ou IIS.

Configurez les journaux IIS dans Log Analytics à partir du [menu Données dans Paramètres de Log Analytics](log-analytics-data-sources.md/configuring-data-sources). Aucune configuration n’est requise autre que la sélection de l’option **Collecter les fichiers journaux IIS au format W3C**.

Quand vous activez la collecte de journaux IIS, nous vous recommandons de configurer le paramètre de substitution de journal IIS sur chaque serveur.


## Collecte des données

Log Analytics collecte les entrées du journal IIS de chaque source connectée toutes les 15 minutes environ. L’agent enregistre sa place dans chaque journal d’événements d’où il la collecte. Si l’agent est mis hors connexion pendant un moment, Log Analytics collecte les événements à partir de là où il s’était arrêté, même si ces événements ont été créés lorsque l’agent était hors connexion.


## Propriétés d’enregistrement de journal IIS

Les enregistrements de journal IIS sont de type **W3CIISLog** et leurs propriétés sont décrites dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Ordinateur | Nom de l'ordinateur à partir duquel l'événement a été collecté. |
| cIP | Adresse IP du client. |
| csMethod | Méthode de la requête, par exemple GET ou POST. |
| csReferer | Site à partir duquel l'utilisateur a suivi un lien vers le site actuel. |
| csUserAgent | Type de navigateur du client. |
| csUserName | Nom de l'utilisateur authentifié qui a accédé au serveur. Les utilisateurs anonymes sont indiqués par un trait d'union. |
| csUriStem | Cible de la requête, par exemple une page web. |
| csUriQuery | Requête, le cas échéant, que le client tentait d'effectuer. |
| ManagementGroupName | Nom du groupe d'administration pour les agents SCOM. Pour les autres agents, il s'agit d’AOI-<ID de l'espace de travail> |
| RemoteIPCountry | Pays de l'adresse IP du client. |
| RemoteIPLatitude | Latitude de l'adresse IP du client. |
| RemoteIPLongitude | Longitude de l'adresse IP du client. |
| scStatus | Code d'état HTTP. |
| scSubStatus | Code d'erreur du sous-état. |
| scWin32Status | Code d’état Windows. |
| sIP | Adresse IP du serveur web. |
| SourceSystem | OpsMgr |
| sPort | Port sur le serveur auquel client est connecté. |
| sSiteName | Nom du site IIS. |
| TimeGenerated | Date et heure de consignation de l'entrée. |
| TimeTaken | Délai de traitement de la requête en millisecondes. |

## Recherches de journaux avec les journaux IIS

Le tableau suivant fournit plusieurs exemples de requêtes de journaux qui extraient des enregistrements de journaux IIS.

| Interroger | Description |
|:--|:--|
| Type=IISLog | Tous les enregistrements de journaux IIS. |
| Type=IISLog EventLevelName=error | Tous les événements Windows avec la gravité de l'erreur. |
| Type=W3CIISLog | Measure count() by cIP | Nombre d’entrées de journaux IIS par adresse IP du client. |
| Type=W3CIISLog csHost="www.contoso.com" | Measure count() by csUriStem | Nombre d’entrées de journaux IIS par URL pour l'hôte www.contoso.com. |
| Type=W3CIISLog | Measure Sum(csBytes) by Computer | top 500000| Nombre total d'octets reçus par chaque ordinateur IIS. |

## Étapes suivantes

- Configurez Log Analytics pour collecter d’autres [sources de données](log-analytics-data-sources.md) à analyser.
- Découvrez les [recherches de journaux](log-analytics-log-searches.md) pour analyser les données collectées à partir de sources de données et de solutions.
- Configurez les alertes dans Log Analytics pour être informé de façon proactive des conditions importantes rencontrées dans les journaux IIS.

<!---HONumber=AcomDC_0518_2016-->