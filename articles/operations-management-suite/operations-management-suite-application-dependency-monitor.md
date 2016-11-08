---
title: Application Dependency Monitor (ADM) dans Operations Management Suite (OMS) | Microsoft Docs
description: ADM (Application Dependency Monitor) est une solution OMS (Operations Management Suite) qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services.  Cet article fournit des informations sur le déploiement d’ADM dans votre environnement et son utilisation dans divers scénarios.
services: operations-management-suite
documentationcenter: ''
author: daseidma
manager: jwhit
editor: tysonn

ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2016
ms.author: daseidma;bwren

---
# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-(oms)"></a>Utilisation de la solution Application Dependency Monitor dans Operations Management Suite (OMS)
![Icône de gestion des alertes](media/operations-management-suite-application-dependency-monitor/icon.png) La solution ADM (Application Dependency Monitor) détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Elle vous permet d’afficher les serveurs comme des systèmes interconnectés qui fournissent des services critiques.  Application Dependency Monitor affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée à TCP, sans configuration requise autre que l’installation d’un agent.

Cet article décrit en détails l’utilisation d’Application Dependency Monitor.  Pour plus d’informations sur la configuration d’ADM et l’intégration des agents, consultez [Configuration de la solution Application Dependency Monitor dans Operations Management Suite (OMS)](operations-management-suite-application-dependency-monitor-configure.md)

> [!NOTE]
> Application Dependency Monitor est actuellement en version préliminaire privée.  Vous pouvez demander à y accéder à l’adresse [https://aka.ms/getadm](https://aka.ms/getadm).
> 
> Pendant la durée de la version préliminaire privée, tous les comptes OMS ont un accès illimité à ADM.  Les nœuds ADM sont gratuits, mais les données Log Analytics pour les types AdmComputer_CL et AdmProcess_CL sont mesurées comme n’importe quelle autre solution.
> 
> Quand ADM passera en version préliminaire publique, il sera disponible uniquement pour les clients payants et gratuits d’Insight & Analytics dans le plan tarifaire OMS.  Les comptes du niveau gratuit sont limités à 5 nœuds ADM.  Si vous participez à la version préliminaire privée et que vous n’êtes pas inscrit au plan tarifaire OMS quand ADM sera en version préliminaire publique, ADM sera désactivé à ce moment-là. 
> 
> 

## <a name="use-cases:-make-your-it-processes-dependency-aware"></a>Cas d’utilisation : Intégrer la dépendance dans les processus informatiques
### <a name="discovery"></a>Découverte
ADM crée automatiquement une carte de référence commune des dépendances entre vos serveurs, les processus et les services tiers.  Il découvre et mappe toutes les dépendances TCP, en identifiant les connexions inattendues, les systèmes tiers distants dont vous dépendez et les dépendances envers les zones sombres traditionnelles de votre réseau comme DNS et AD.  ADM détecte les connexions réseau ayant échoué que vos systèmes gérés tentent d’établir, en vous aidant à identifier les problèmes réseau, les interruptions de service et les configurations de serveur erronées.

### <a name="incident-management"></a>Gestion des incidents
ADM vous montre comment les systèmes sont connectés et ont une incidence les uns sur les autres, ce qui vous évide de devoir isoler vous-même le problème.  Outre les connexions qui ont échoué, les informations sur les clients connectés aident à identifier les équilibreurs de charge mal configurés, les charges étonnantes ou excessives sur les services critiques et les clients non autorisés tels que les ordinateurs des développeurs qui communiquent avec les systèmes de production.  Les flux de travail intégrés au suivi des modifications OMS permettent également de voir si un événement de modification sur un ordinateur ou service principal explique la cause racine d’un incident.

### <a name="migration-assurance"></a>Garantie d’une migration réussie
ADM vous permet de planifier, d’accélérer et de valider efficacement les migrations Azure, en vous assurant que rien n’est délaissé et qu’il n’existe aucune interruption inattendue.  Vous pouvez découvrir tous les systèmes interdépendants qui doivent migrer en même temps, évaluer la capacité et la configuration du système et déterminer si un système en cours d’exécution continue de servir les utilisateurs ou si une mise hors service peut être envisagée au lieu de la migration.  Une fois la migration terminée, vous pouvez vérifier la charge et l’identité du client pour contrôler que les systèmes de test et les clients se connectent.  Si vos définitions de pare-feu et de planification de sous-réseau rencontrent des problèmes, les connexions ayant échoué dans les mappages ADM vous dirigent vers les systèmes nécessitant une connectivité.

### <a name="business-continuity"></a>Continuité des activités
Si vous utilisez Azure Site Recovery et que vous avez besoin d’aide pour définir la séquence de récupération pour votre environnement d’application, ADM peut automatiquement vous indiquer comment les systèmes s’appuient les uns sur les autres pour s’assurer que votre plan de récupération est fiable.  En choisissant un serveur critique et en affichant ses clients, vous pouvez identifier les systèmes frontaux qui doivent être récupérés uniquement après que le serveur critique est restauré et disponible.  À l’inverse, en examinant les dépendances principales d’un serveur critique, vous pouvez identifier les systèmes qui doivent être récupérés avant la restauration de votre système principal.

### <a name="patch-management"></a>Gestion des correctifs
ADM améliore l’utilisation de l’évaluation des mises à jour du système OMS en vous montrant quels sont les autres équipes et serveurs qui dépendent de votre service. Vous pouvez ainsi notifier ces derniers à l’avance avant d’interrompre vos systèmes pour la mise à jour corrective.  ADM améliore également la gestion des correctifs dans OMS en vous montrant si vos services sont disponibles et connectés correctement une fois que la mise à jour corrective a été appliquée et qu’ils ont redémarré. 

## <a name="mapping-overview"></a>Vue d’ensemble du mappage
Les agents ADM rassemblent des informations sur tous les processus connectés via TCP sur le serveur où ils sont installés, ainsi que des détails sur les connexions entrantes et sortantes pour chaque processus.  À l’aide de la liste des ordinateurs à gauche de la solution ADM, les ordinateurs incluant des agents ADM peuvent être sélectionnés pour visualiser leurs dépendances sur une période donnée.  Les mappages de dépendances d’ordinateurs ciblent un ordinateur spécifique et affichent tous les ordinateurs qui sont des clients ou serveurs TCP directs de cet ordinateur.

![Vue d’ensemble d’ADM](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

Les ordinateurs peuvent être développés sur le mappage de manière à afficher les processus en cours d’exécution avec les connexions réseau actives pendant la période sélectionnée.  Lorsqu’un ordinateur distant avec un agent ADM est développé pour afficher les détails du processus, seuls les processus de qui communiquent avec l’ordinateur ciblé sont affichés.  Le nombre d’ordinateurs frontaux sans agent qui se connectent à l’ordinateur ciblé est indiqué à gauche des processus auxquels ils se connectent.  Si l’ordinateur ciblé établit une connexion avec un ordinateur principal sans agent, ce dernier est représenté par un nœud sur le mappage qui affiche son adresse IPv4. Le nœud peut être développé pour afficher les ports et services individuels avec lesquels l’ordinateur ciblé communique.

Par défaut, les mappages ADM affichent les 10 dernières minutes des informations de dépendance.  À l’aide des contrôles de temps en haut à gauche, les mappages peuvent être interrogés selon des plages horaires d’historique, jusqu’à une échelle d’une heure, pour montrer l’aspect passé des dépendances, par exemple, pendant un incident ou avant une modification.    Les données ADM sont stockées pendant 30 jours dans les espaces de travail payants et pendant 7 jours dans les espaces de travail gratuits.

![Mappage d’ordinateurs avec propriétés d’ordinateurs sélectionnées](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>Connexions ayant échoué
Les connexions ayant échoué sont affichées dans les mappages ADM des processus et ordinateurs, avec une ligne rouge en pointillés qui indique si un système client ne parvient pas à atteindre un processus ou un port.  Les connexions ayant échoué sont signalées par n’importe quel système ayant un agent ADM déployé si ce système est celui qui a tenté d’établir de telles connexions.  ADM mesure cela en observant les sockets TCP qui ne parviennent pas à établir une connexion.  Cela peut être dû à un pare-feu, à une configuration incorrecte du client ou serveur ou à un service distant non disponible. 

![Connexions ayant échoué](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

Le fait de comprendre les connexions ayant échoué peut faciliter le dépannage, la validation de la migration, l’analyse de la sécurité et la compréhension globale de l’architecture.  Il arrive parfois que les connexions ayant échoué soient sans incidence. Mais elles pointent souvent directement un problème, tel qu’un environnement de basculement subitement inaccessible ou deux couches d’application qui ne sont pas en mesure de communiquer après une migration vers le cloud.  Dans l’image ci-dessus, IIS et WebSphere fonctionnent, mais ne peuvent pas se connecter. 

## <a name="computer-and-process-properties"></a>Propriétés des ordinateurs et processus
Lorsque vous parcourez un mappage ADM, vous pouvez sélectionner des ordinateurs et des processus afin d’obtenir un contexte supplémentaire sur leurs propriétés.  Les ordinateurs fournissent des informations sur le nom DNS, les adresses IPv4, la capacité en termes de processeur et de mémoire, le type de machine virtuelle, la version du système d’exploitation, l’heure du dernier redémarrage et les ID de leurs agents OMS et ADM.

Les détails du processus sont collectés à partir des métadonnées du système d’exploitation sur l’exécution des processus, y compris le nom du processus, sa description, le nom d’utilisateur et le domaine (sous Windows), le nom de la société, le nom ou la version du produit, le répertoire de travail, la ligne de commande et l’heure de démarrage du processus.

![Propriétés des processus](media/operations-management-suite-application-dependency-monitor/process-properties.png)

Le volet Résumé du processus fournit des informations supplémentaires sur la connectivité de ce processus, y compris les ports liés, les connexions entrantes et sortantes et les connexions ayant échoué. 

![Résumé du processus](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>Intégration du suivi des modifications OMS
L’intégration d’ADM avec le suivi des modifications est automatique lorsque les deux solutions sont activées et configurées dans votre espace de travail OMS.

Le volet de résumé de l’ordinateur indique si les événements de suivi des modifications se sont produits sur l’ordinateur sélectionné au cours de la période sélectionnée.

![Volet de résumé de l’ordinateur](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

Le volet de suivi des modifications de l’ordinateur affiche une liste de toutes les modifications, les plus récentes étant affichées en premier, ainsi qu’un lien pour rechercher des informations supplémentaires dans la recherche de journal.
![Volet de suivi des modifications de l’ordinateur](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

Voici une vue détaillée de l’événement de modification de la configuration après la sélection de l’option **Afficher dans Log Analytics**.
![Événement de modification de la configuration](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)

## <a name="log-analytics-records"></a>Enregistrements Log Analytics
Les données d’inventaire des ordinateurs et processus ADM peuvent faire l’objet d’une [recherche](../log-analytics/log-analytics-log-searches.md) dans Log Analytics.  Cela peut s’appliquer à de nombreux scénarios, y compris la planification de la migration, l’analyse de la capacité, la détection et la résolution des problèmes de performances ad hoc. 

Un enregistrement est généré par heure pour chaque processus et ordinateur unique en plus des enregistrements générés quand ce même processus ou ordinateur démarre ou est intégré à ADM.  Les propriétés de ces enregistrements sont décrites dans les tableaux suivants. 

Il existe des propriétés générées en interne que vous pouvez utiliser pour identifier les ordinateurs et processus uniques :

* PersistentKey_s est défini de façon unique par la configuration de processus, par exemple, l’ID utilisateur et la ligne de commande.  Il est unique pour un ordinateur donné, mais peut être répété sur plusieurs ordinateurs.
* ProcessId_s et ComputerId_s sont globalement uniques dans le modèle ADM.

### <a name="admcomputer_cl-records"></a>Enregistrements AdmComputer_CL
Les enregistrements de type **AdmComputer_CL** ont des données d’inventaire pour les serveurs incluant des agents ADM.  Les propriétés de ces enregistrements sont décrites dans le tableau suivant.  

| Propriété | Description |
|:--- |:--- |
| Type |*AdmComputer_CL* |
| SourceSystem |*OpsManager* |
| ComputerName_s |Nom d’ordinateur Windows ou Linux |
| CPUSpeed_d |Vitesse du processeur en MHz |
| DnsNames_s |Liste de tous les noms DNS de cet ordinateur |
| IPv4s_s |Liste de toutes les adresses IPv4 de cet ordinateur |
| IPv6s_s |Liste de toutes les adresses IPv6 de cet ordinateur  (ADM identifie les adresses IPv6 mais ne détecte pas les dépendances IPv6). |
| Is64Bit_b |True ou False selon le type de système d’exploitation |
| MachineId_s |GUID interne unique dans un espace de travail OMS |
| OperatingSystemFamily_s |Windows ou Linux |
| OperatingSystemVersion_s |Longue chaîne de version du système d’exploitation |
| TimeGenerated |Date et heure de création de l’enregistrement. |
| TotalCPUs_d |Nombre de cœurs de processeur |
| TotalPhysicalMemory_d |Capacité de mémoire en Mo |
| VirtualMachine_b |True ou False selon que le système d’exploitation est ou non un invité de machine virtuelle |
| VirtualMachineID_g |ID de machine virtuelle Hyper-V |
| VirtualMachineName_g |Nom de machine virtuelle Hyper-V |
| VirtualMachineType_s |Hyperv, Vmware, Xen, Kvm, Ldom, Lpar, Virtualpc |

### <a name="admprocess_cl-type-records"></a>Enregistrements de type AdmProcess_CL
Les enregistrements de type **AdmProcess_CL** ont des données d’inventaire pour les processus connectés à TCP sur des serveurs ayant des agents ADM.  Les propriétés de ces enregistrements sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Type |*AdmProcess_CL* |
| SourceSystem |*OpsManager* |
| CommandLine_s |Ligne de commande complète du processus |
| CompanyName_s |Nom de l’entreprise (à partir de Windows PE ou Linux RPM) |
| Description_s |Longue description du processus (à partir de Windows PE ou Linux RPM) |
| FileVersion_s |Version du fichier exécutable (à partir de Windows PE, Windows uniquement) |
| FirstPid_d |ID du processus de système d’exploitation |
| InternalName_s |Nom interne du fichier exécutable (à partir de Windows PE, Windows uniquement) |
| MachineId_s |GUID interne unique dans un espace de travail OMS |
| Name_s |Nom de l’exécutable du processus |
| Path_s |Chemin d’accès du système de fichiers de l’exécutable du processus |
| PersistentKey_s |GUID interne unique au sein de cet ordinateur |
| PoolId_d |ID interne pour les processus d’agrégation basés sur des lignes de commande similaires. |
| ProcessId_s |GUID interne unique dans un espace de travail OMS |
| ProductName_s |Chaîne de nom du produit (à partir de Windows PE ou Linux RPM) |
| ProductVersion_s |Chaîne de version du produit (à partir de Windows PE ou Linux RPM) |
| StartTime_t |Heure de début du processus sur l’horloge de l’ordinateur local |
| TimeGenerated |Date et heure de création de l’enregistrement. |
| UserDomain_s |Domaine du propriétaire du processus (Windows uniquement) |
| UserName_s |Nom du propriétaire du processus (Windows uniquement) |
| WorkingDirectory_s |Répertoire de travail du processus |

## <a name="sample-log-searches"></a>Exemples de recherches de journaux
### <a name="list-the-physical-memory-capacity-of-all-managed-computers."></a>Répertorier la capacité de mémoire physique de tous les ordinateurs gérés
Type=AdmComputer_CL | select TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name,-dns,-ip,-and-os-version."></a>Répertorier le nom de l’ordinateur, le DNS, l’adresse IP et la version du système d’exploitation
Type=AdmComputer_CL | select ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s  | dedup ComputerName_s

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-"sql"-in-the-command-line"></a>Rechercher tous les processus contenant « sql » dans la ligne de commande
Type=AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process,-use-its-machine-id-to-retrieve-the-computer’s-name"></a>Après avoir affiché les données d’événement d’un processus spécifique, utiliser son ID d’ordinateur pour récupérer le nom de l’ordinateur
Type=AdmComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Distinct ComputerName_s

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>Répertorier tous les ordinateurs exécutant SQL
Type=AdmComputer_CL MachineId_s IN {Type=AdmProcess_CL \*sql\* | Distinct MachineId_s} | Distinct ComputerName_s

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Liste de toutes les versions de produit uniques CURL dans mon centre de données
Type=AdmProcess_CL Name_s=curl | Distinct ProductVersion_s

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Créer un groupe d’ordinateurs de tous les ordinateurs exécutant CentOS
![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)

## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation
Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Application Dependency Monitor. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service Application Dependency Monitor. Les données incluent des informations sur la configuration de votre logiciel (comme le système d’exploitation et la version) et également l’adresse IP, le nom DNS et le nom du poste de travail afin de fournir des fonctionnalités de dépannage précises et efficaces. Nous ne collectons pas votre nom, votre adresse, ni vos autres informations de contact.

Pour plus d’informations sur l’utilisation et la collecte de données, consultez la [déclaration de confidentialité Microsoft Online Services](hhttps://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [les recherches dans les journaux](../log-analytics/log-analytics-log-searches.md\] in Log Analytics to retrieve data collected by Application Dependency Monitor..md).

<!--HONumber=Oct16_HO2-->


