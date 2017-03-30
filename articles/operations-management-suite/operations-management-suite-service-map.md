---
title: Carte de service dans Operations Management Suite (OMS) | Microsoft Docs
description: "Carte de service est une solution OMS (Operations Management Suite) qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services.  Cet article fournit des informations sur le déploiement de Carte de service dans votre environnement et son utilisation dans divers scénarios."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: aca6cd69647406d0140d8b0d0cba8e4690b04ae1
ms.lasthandoff: 03/18/2017


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>Utilisation de Carte de service dans Operations Management Suite (OMS)
Carte de service détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Elle vous permet d’afficher les serveurs comme des systèmes interconnectés qui fournissent des services critiques.  Carte de service affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée à TCP, sans configuration requise autre que l’installation d’un agent.

Cet article décrit les détails de l’utilisation de Carte de service.  Pour plus d’informations sur la configuration de Carte de service et l’intégration des agents, consultez [Configuration de la solution Carte de service dans Operations Management Suite (OMS)](operations-management-suite-service-map-configure.md)


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Cas d’utilisation : Intégrer la dépendance dans les processus informatiques

### <a name="discovery"></a>Découverte
Carte de service crée automatiquement une carte de référence commune des dépendances entre vos serveurs, les processus et les services tiers.  Il découvre et mappe toutes les dépendances TCP, en identifiant les connexions inattendues, les systèmes tiers distants dont vous dépendez et les dépendances envers les zones sombres traditionnelles de votre réseau comme Active Directory.  Carte de service détecte les connexions réseau ayant échoué que vos systèmes gérés tentent d’établir, en vous aidant à identifier les problèmes réseau, les interruptions de service et les configurations de serveur erronées.

### <a name="incident-management"></a>Gestion des incidents
Carte de service vous montre comment les systèmes sont connectés et ont une incidence les uns sur les autres, ce qui vous évide de devoir isoler vous-même le problème.  Outre les connexions qui ont échoué, les informations sur les clients connectés aident à identifier les équilibreurs de charge mal configurés, les charges étonnantes ou excessives sur les services critiques et les clients non autorisés tels que les ordinateurs des développeurs qui communiquent avec les systèmes de production.  Les flux de travail intégrés au suivi des modifications OMS permettent également de voir si un événement de modification sur un ordinateur ou service principal explique la cause racine d’un incident.

### <a name="migration-assurance"></a>Garantie d’une migration réussie
Carte de service vous permet de planifier, d’accélérer et de valider efficacement les migrations Azure, en vous assurant que rien n’est délaissé et qu’il n’existe aucune interruption inattendue.  Vous pouvez découvrir tous les systèmes interdépendants qui doivent migrer en même temps, évaluer la capacité et la configuration du système et déterminer si un système en cours d’exécution continue de servir les utilisateurs ou si une mise hors service peut être envisagée au lieu de la migration.  Une fois la migration terminée, vous pouvez vérifier la charge et l’identité du client pour contrôler que les systèmes de test et les clients se connectent.  Si vos définitions de pare-feu et de planification de sous-réseau rencontrent des problèmes, les connexions ayant échoué dans les mappages Carte de service vous dirigent vers les systèmes nécessitant une connectivité.

### <a name="business-continuity"></a>Continuité de l’activité
Si vous utilisez Azure Site Recovery et que vous avez besoin d’aide pour définir la séquence de récupération pour votre environnement d’application, Carte de service peut automatiquement vous indiquer comment les systèmes s’appuient les uns sur les autres pour s’assurer que votre plan de récupération est fiable.  En choisissant un serveur critique et en affichant ses clients, vous pouvez identifier les systèmes frontaux qui doivent être récupérés uniquement après que le serveur critique est restauré et disponible.  À l’inverse, en examinant les dépendances principales d’un serveur critique, vous pouvez identifier les systèmes qui doivent être récupérés avant la restauration de votre système principal.

### <a name="patch-management"></a>Gestion des correctifs
Carte de service améliore l’utilisation de l’évaluation des mises à jour du système OMS en vous montrant quels sont les autres équipes et serveurs qui dépendent de votre service. Vous pouvez ainsi notifier ces derniers à l’avance avant d’interrompre vos systèmes pour la mise à jour corrective.  Carte de service améliore également la gestion des correctifs dans OMS en vous montrant si vos services sont disponibles et connectés correctement une fois que la mise à jour corrective a été appliquée et qu’ils ont redémarré.


## <a name="mapping-overview"></a>Vue d’ensemble du mappage
Les agents Carte de service rassemblent des informations sur tous les processus connectés via TCP sur le serveur où ils sont installés, ainsi que des détails sur les connexions entrantes et sortantes pour chaque processus.  À l’aide de la liste des ordinateurs à gauche de la solution Carte de service, les ordinateurs incluant des agents Carte de service peuvent être sélectionnés pour visualiser leurs dépendances sur une période donnée.  Les mappages de dépendances d’ordinateurs ciblent un ordinateur spécifique et affichent tous les ordinateurs qui sont des clients ou serveurs TCP directs de cet ordinateur.

![Vue d’ensemble de Carte de service](media/oms-service-map/service-map-overview.png)

Les ordinateurs peuvent être développés sur le mappage de manière à afficher les processus en cours d’exécution avec les connexions réseau actives pendant la période sélectionnée.  Lorsqu’un ordinateur distant avec un agent Carte de service est développé pour afficher les détails du processus, seuls les processus qui communiquent avec l’ordinateur ciblé sont affichés.  Le nombre d’ordinateurs frontaux sans agent qui se connectent à l’ordinateur ciblé est indiqué à gauche des processus auxquels ils se connectent.  Si l’ordinateur actif se connecte à un ordinateur principal sans agent, ce serveur principal est inclus dans un groupe de ports du serveur, avec d’autres connexions vers le même numéro de port.

Par défaut, les mappages Carte de service affichent les 10 dernières minutes des informations de dépendance.  À l’aide des contrôles de temps en haut à gauche, les mappages peuvent être interrogés selon des plages horaires d’historique, jusqu’à une échelle d’une heure, pour montrer l’aspect passé des dépendances, par exemple, pendant un incident ou avant une modification.    Les données Carte de service sont stockées pendant 30 jours dans les espaces de travail payants et pendant 7 jours dans les espaces de travail gratuits.

## <a name="status-badges-and-border-coloring"></a>Badges d’état et couleur de bordure
En bas de chaque serveur dans le mappage, une liste de badges état affiche des informations sur l’état du serveur.  Les badges indiquent qu’il existe des informations pertinentes concernant le serveur provenant d'une des intégrations de solutions OMS.  En cliquant sur un badge, vous accédez directement aux détails de l’état dans le volet droit.  Les badges d’état actuellement disponibles incluent les alertes, les modifications, la sécurité et les mises à jour.

Selon la gravité des badges d’état, les bordures de nœud de machine peuvent être affichées en rouge (critique), jaune (avertissement) ou bleu (informatif).  La couleur est celle du badge d’état dont la gravité est la plus critique.  Une bordure grise indique un nœud sans indicateur de statut actuel.

![Badges d’état](media/oms-service-map/status-badges.png)

## <a name="role-icons"></a>Icônes de rôle
Certains processus jouent des rôles particuliers sur les machines : serveurs web, serveurs d’applications, serveurs de base de données, etc.  La carte de service annote les zones de processus et de machine avec des icônes de rôle pour identifier en un coup d’œil le rôle que joue un processus ou un serveur.

| Icône de rôle | Description |
|:--|:--|
| ![Serveur web](media/oms-service-map/role-web-server.png) | Serveur web |
| ![App Server](media/oms-service-map/role-application-server.png) | Serveur d’applications |
| ![Serveur de base de données](media/oms-service-map/role-database.png) | Serveur de base de données |
| ![Serveur LDAP](media/oms-service-map/role-ldap.png) | Serveur LDAP |
| ![Serveur SMB](media/oms-service-map/role-smb.png) | Serveur SMB |

![Icônes de rôle](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>Connexions ayant échoué
Les connexions ayant échoué sont affichées dans les mappages Carte de service des processus et ordinateurs, avec une ligne rouge en pointillés qui indique si un système client ne parvient pas à atteindre un processus ou un port.  Les connexions ayant échoué sont signalées par n’importe quel système ayant un agent Carte de service déployé si ce système est celui qui a tenté d’établir de telles connexions.  Carte de service mesure cela en observant les sockets TCP qui ne parviennent pas à établir une connexion.  Cela peut être dû à un pare-feu, à une configuration incorrecte du client ou serveur ou à un service distant non disponible.

![Connexions ayant échoué](media/oms-service-map/failed-connections.png)

Le fait de comprendre les connexions ayant échoué peut faciliter le dépannage, la validation de la migration, l’analyse de la sécurité et la compréhension globale de l’architecture.  Il arrive parfois que les connexions ayant échoué soient sans incidence. Mais elles pointent souvent directement un problème, tel qu’un environnement de basculement subitement inaccessible ou deux couches d’application qui ne sont pas en mesure de communiquer après une migration vers le cloud.

## <a name="client-groups"></a>Groupes de clients
Les groupes de clients sont des zones sur la carte qui représentent des ordinateurs clients n’ayant pas d’agents de dépendance.  Un groupe de clients représente l’ensemble des clients d’un processus.

![Groupes de clients](media/oms-service-map/client-groups.png)

Pour afficher les adresses IP des serveurs d’un groupe de clients, sélectionnez le groupe.  Le contenu du groupe apparaît dans le panneau Propriétés.

![Propriétés du groupe de clients](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Groupes de ports du serveur
Les groupes de ports du serveur sont des zones qui représentent les ports de chaque serveur n’ayant pas d’agents de dépendance.  La zone répertorie les ports du serveur, ainsi que le nombre de serveurs ayant des connexions à ces ports.  Développez la zone pour afficher chaque serveur et ses connexions.  S’il n’y a qu’un serveur dans la zone, son nom ou son adresse IP s’affiche.

![Groupes de ports du serveur](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu contextuel
Cliquez sur les trois points dans la partie supérieure droite d'un serveur afin d'afficher le menu contextuel de ce serveur.

![Connexions ayant échoué](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>Charger la carte du serveur
L'option Charger la carte du serveur permet d’accéder à une nouvelle carte avec le serveur sélectionné comme nouvel ordinateur ciblé.

### <a name="showhide-self-links"></a>Afficher/masquer les liens réflexifs
L'option Afficher les liens réflexifs redessine le nœud du serveur, y compris les liens réflexifs, qui représentent les connexions TCP qui commencent et se terminent sur des processus au sein du serveur.  Si des liens réflexifs sont affichés, le menu passe à Masquer les liens réflexifs, permettant aux utilisateurs d'afficher ou de masquer les liens réflexifs.

## <a name="computer-summary"></a>Résumé de l’ordinateur
Le volet Résumé de l’ordinateur inclut une vue d’ensemble du système d’exploitation d’un serveur et du nombre de dépendances avec une grande variété de données issues d’autres solutions OMS, notamment les mesures de performances, le suivi des modifications, la sécurité, les mises à jour, etc.

![Résumé de l’ordinateur](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Propriétés des ordinateurs et processus
Lorsque vous parcourez un mappage Carte de service, vous pouvez sélectionner des ordinateurs et des processus afin d’obtenir un contexte supplémentaire sur leurs propriétés.  Les ordinateurs fournissent des informations sur le nom DNS, les adresses IPv4, la capacité en termes de processeur et de mémoire, le type de machine virtuelle, la version du système d’exploitation, l’heure du dernier redémarrage et les ID de leurs agents OMS et Carte de service.

![Propriétés de machine virtuelle](media/oms-service-map/machine-properties.png)

Les détails du processus sont collectés à partir des métadonnées du système d’exploitation sur l’exécution des processus, y compris le nom du processus, sa description, le nom d’utilisateur et le domaine (sur Windows), le nom de la société, le nom ou la version du produit, le répertoire de travail, la ligne de commande et l’heure de démarrage du processus.

![Propriétés des processus](media/oms-service-map/process-properties.png)

Le volet Résumé du processus fournit des informations supplémentaires sur la connectivité de ce processus, y compris les ports liés, les connexions entrantes et sortantes et les connexions ayant échoué.

![Résumé du processus](media/oms-service-map/process-summary.png)

## <a name="oms-alerts-integration"></a>Intégration des alertes OMS
Carte de service intègre les alertes OMS pour afficher les alertes déclenchées pour le serveur sélectionné pendant la période sélectionnée.  Le serveur affiche une icône si des alertes sont en cours et le panneau d’alertes de la machine répertorie les alertes

![Panneau d’alertes de la machine](media/oms-service-map/machine-alerts.png)

Pour que Carte de service puisse afficher les alertes pertinentes, la règle d’alerte doit être créée afin de se déclencher pour un ordinateur spécifique.  Pour créer des alertes appropriées :
- Inclure une clause de regroupement par ordinateur : "by Computer interval 1minute"
- Choisir d’alerter en fonction des mesures

![Configuration des alertes](media/oms-service-map/alert-configuration.png)


## <a name="oms-log-events-integration"></a>Intégration des événements du journal OMS
Carte de service intègre la recherche dans les journaux pour afficher un nombre de tous les événements du journal disponibles pour le serveur sélectionné au cours de la période sélectionnée.  Vous pouvez cliquer sur n’importe quelle ligne dans la liste des événements pour passer à la recherche dans les journaux et consulter les événements du journal individuel.

![Événements du journal](media/oms-service-map/log-events.png)

## <a name="oms-change-tracking-integration"></a>Intégration du suivi des modifications OMS
L’intégration de Carte de service avec le suivi des modifications est automatique lorsque les deux solutions sont activées et configurées dans votre espace de travail OMS.

Le volet de suivi des modifications de l’ordinateur affiche une liste de toutes les modifications, les plus récentes étant affichées en premier, ainsi qu’un lien pour rechercher des informations supplémentaires dans la recherche de journal.
![Volet de suivi des modifications de l’ordinateur](media/oms-service-map/change-tracking.png)

Voici une vue détaillée de l’événement de modification de la configuration après la sélection de l’option **Afficher dans Log Analytics**.
![Événement de modification de la configuration](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>Intégration des performances OMS
Le panneau Performances de la machine affiche les mesures de performances standard pour le serveur sélectionné.  Les mesures incluent l’utilisation du processeur, l’utilisation de la mémoire, les octets réseau envoyés et reçus, et une liste des principaux processus en octets réseau envoyés et reçus.  Notez que pour obtenir les données de performances réseau, vous devez également activer la solution Wire Data 2.0 dans OMS.
![Volet de suivi des modifications de l’ordinateur](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>Intégration de la sécurité OMS
L’intégration de Carte de service avec la sécurité et l’audit est automatique lorsque les deux solutions sont activées et configurées dans votre espace de travail OMS.

Le panneau de sécurité de la machine affiche les données de la solution de sécurité et d’audit OMS pour le serveur sélectionné.  Le panneau affiche un résumé de tout problème de sécurité en attente pour le serveur pendant la période sélectionnée.  Le fait de cliquer sur l’un des problèmes de sécurité donne accès à une recherche de journal pour plus d’informations sur les problèmes de sécurité.
![Volet de suivi des modifications de l’ordinateur](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>Intégration des mises à jour OMS
L’intégration de Carte de service avec la gestion des mises à jour est automatique lorsque les deux solutions sont activées et configurées dans votre espace de travail OMS.

Le panneau de mises à jour de la machine affiche les données de la solution de gestion des mises à jour OMS pour le serveur sélectionné.  Le panneau affiche un résumé des mises à jour manquantes pour le serveur pendant la période sélectionnée.
![Volet de suivi des modifications de l’ordinateur](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Enregistrements Log Analytics
Les données d’inventaire des ordinateurs et processus Carte de service peuvent faire l’objet d’une [recherche](../log-analytics/log-analytics-log-searches.md) dans Log Analytics.  Cela peut s’appliquer à de nombreux scénarios, y compris la planification de la migration, l’analyse de la capacité, la détection et la résolution des problèmes de performances ad hoc.

Un enregistrement est généré par heure pour chaque processus et ordinateur unique en plus des enregistrements générés quand un processus ou ordinateur démarre ou est intégré à Carte de service.  Les propriétés de ces enregistrements sont décrites dans les tableaux suivants.  Les champs et les valeurs des événements ServiceMapComputer_CL sont mappés aux champs de la ressource Ordinateur dans l’API ARM ServiceMap.  Les champs et les valeurs des événements ServiceMapProcess_CL sont mappés aux champs de la ressource Processus dans l’API ARM ServiceMap.  Le champ ResourceName_s correspond au champ du nom de la ressource ARM correspondante. Remarque : à mesure que les fonctionnalités Service Map se développent, ces champs sont susceptibles d’être modifiés.


Il existe des propriétés générées en interne que vous pouvez utiliser pour identifier les ordinateurs et processus uniques :

- Ordinateur : utilisez ResourceId ou ResourceName_s pour identifier de façon unique un ordinateur au sein d’un espace de travail OMS.
- Processus : utilisez ResourceId pour identifier de façon unique un processus au sein d’un espace de travail OMS. ResourceName_s est unique dans le contexte de l’ordinateur sur lequel le processus est en cours d’exécution (MachineResourceName_s) 

Étant donné que plusieurs enregistrements peuvent exister pour un processus et un ordinateur donnés dans une plage de temps spécifique, les requêtes peuvent renvoyer plusieurs enregistrements pour le même ordinateur ou processus. Pour inclure uniquement l’enregistrement le plus récent, ajoutez "| dedup ResourceId" à la requête.

### <a name="servicemapcomputercl-records"></a>Enregistrements ServiceMapComputer_CL
Les enregistrements de type **ServiceMapComputer_CL** ont des données d’inventaire pour les serveurs incluant des agents Carte de service.  Les propriétés de ces enregistrements sont décrites dans le tableau suivant :

| Propriété | Description |
|:--|:--|
| Type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | identificateur unique de l’ordinateur dans l’espace de travail |
| ResourceName_s | identificateur unique de l’ordinateur dans l’espace de travail |
| ComputerName_s | nom de domaine complet de l'ordinateur |
| Ipv4Addresses_s | liste des adresses IPv4 du serveur |
| Ipv6Addresses_s | liste des adresses IPv6 du serveur |
| DnsNames_s | tableau de noms DNS |
| OperatingSystemFamily_s | Windows ou Linux |
| OperatingSystemFullName_s | nom complet du système d’exploitation  |
| Bitness_s | nombre de bits de l’ordinateur (32 bits) ou (64 bits) |
| PhysicalMemory_d | mémoire physique en Mo |
| Cpus_d | nombre de processeurs |
| CpuSpeed_d | vitesse du processeur en MHz|
| VirtualizationState_s | « inconnu », « physique », « virtuel », « hyperviseur » |
| VirtualMachineType_s | « hyperv », « vmware », etc. |
| VirtualMachineNativeMachineId_g | ID de machine virtuelle attribué par son hyperviseur |
| VirtualMachineName_s | Nom de la machine virtuelle |
| BootTime_t | durée de démarrage |



### <a name="servicemapprocesscl-type-records"></a>Enregistrements de type ServiceMapProcess_CL
Les enregistrements de type **ServiceMapProcess_CL** ont des données d’inventaire pour les processus connectés à TCP sur des serveurs ayant des agents Carte de service.  Les propriétés de ces enregistrements sont décrites dans le tableau suivant :

| Propriété | Description |
|:--|:--|
| Type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | identificateur unique du processus dans l’espace de travail |
| ResourceName_s | identificateur unique du processus sur l’ordinateur sur lequel il est en cours d’exécution|
| MachineResourceName_s | nom de ressource de la machine |
| ExecutableName_s | nom de l’exécutable du processus |
| StartTime_t | heure de début du pool de processus |
| FirstPid_d | premier pid dans le pool de processus |
| Description_s | description du processus |
| CompanyName_s | nom de la société |
| InternalName_s | nom interne |
| ProductName_s | nom du produit |
| ProductVersion_s | version du produit |
| FileVersion_s | version du fichier |
| CommandLine_s | ligne de commande |
| ExecutablePath _s | chemin d’accès au fichier exécutable |
| WorkingDirectory_s | répertoire de travail |
| Nom d’utilisateur | compte sous lequel le processus s’exécute |
| UserDomain | domaine sous lequel le processus s’exécute |


## <a name="sample-log-searches"></a>Exemples de recherches de journaux

### <a name="list-all-known-machines"></a>Liste de tous les ordinateurs connus
Type=ServiceMapComputer_CL | dedup ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Répertorier la capacité de mémoire physique de tous les ordinateurs gérés
Type=ServiceMapComputer_CL | select PhysicalMemory_d, ComputerName_s | Dedup ResourceId

### <a name="list-computer-name-dns-ip-and-os"></a>Répertorier le nom de l’ordinateur, le DNS, l’adresse IP et le système d’exploitation.
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s  | dedup ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Rechercher tous les processus contenant « sql » dans la ligne de commande
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Rechercher un ordinateur (enregistrement le plus récent) par nom de ressource
Type=ServiceMapComputer_CL "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Rechercher un ordinateur (enregistrement le plus récent) par adresse IP
Type=ServiceMapComputer_CL "10.229.243.232" | dedup ResourceId

### <a name="list-all-known-processes-on-a-given-machine"></a>Répertorier tous les processus connus sur un ordinateur donné
Type=ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="list-all-computers-running-sql"></a>Répertorier tous les ordinateurs exécutant SQL
Type=ServiceMapComputer_CL ResourceName_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineResourceName_s} | dedup ResourceId | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Liste de toutes les versions de produit uniques CURL dans mon centre de données
Type=ServiceMapProcess_CL ExecutableName_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Créer un groupe d’ordinateurs de tous les ordinateurs exécutant CentOS
Type=ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinct ComputerName_s


## <a name="rest-api"></a>API REST
L’ensemble des données relatives au serveur, au processus et aux dépendances dans Carte de service est disponible via [l’API REST Carte de service](https://docs.microsoft.com/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation
Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Carte de service. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service Carte de service. Les données incluent des informations sur la configuration de votre logiciel (comme le système d’exploitation et la version) et également l’adresse IP, le nom DNS et le nom du poste de travail afin de fournir des fonctionnalités de dépannage précises et efficaces. Nous ne collectons pas votre nom, votre adresse, ni vos autres coordonnées.

Pour plus d’informations sur l’utilisation et la collecte de données, consultez la [déclaration de confidentialité Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) dans Log Analytics pour récupérer les données collectées par Carte de service.


## <a name="troubleshooting"></a>Résolution des problèmes
- Consultez la [section Résolution des problèmes du document Configuration de la solution Carte de service](operations-management-suite-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Commentaires
Avez-vous des commentaires à nous transmettre à propos de la carte de service ou de sa documentation ?  Si c’est le cas, sachez que notre page [User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), vous permet de nous suggérer des fonctionnalités ou de voter pour les suggestions en cours.

