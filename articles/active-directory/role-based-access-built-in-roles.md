<properties
	pageTitle="RBAC : rôles intégrés | Microsoft Azure"
	description="Cette rubrique décrit les rôles intégrés pour le contrôle d’accès en fonction du rôle (RBAC)."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/20/2016"
	ms.author="kgremban"/>

#RBAC : rôles intégrés

Le contrôle d’accès basé sur un rôle (RBAC) inclut les trois rôles intégrés suivants qui peuvent être affectés à des utilisateurs, des groupes et des services. Vous ne pouvez pas modifier les définitions des rôles intégrés. Toutefois, vous pouvez créer des [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md) en fonction des besoins spécifiques de votre entreprise.

## Rôles dans Azure

Le tableau ci-dessous fournit de brèves descriptions des rôles intégrés. Cliquez sur le nom de rôle pour afficher la liste détaillée des propriétés **actions** et **notactions** du rôle. La propriété **actions** spécifie les actions autorisées sur les ressources Azure. Les chaînes d'action peuvent utiliser des caractères génériques. La propriété **notactions** spécifie les actions qui sont exclues des actions autorisées.

>[AZURE.NOTE] Les définitions de rôle Azure sont en constante évolution. Cet article est actualisé aussi régulièrement que possible, mais vous pouvez toujours trouver les dernières définitions de rôles dans Azure PowerShell. Utilisez les applets de commande `(get-azurermroledefinition "<role name>").actions` ou `(get-azurermroledefinition "<role name>").notactions` selon le cas.

| Nom de rôle | Description |
| --------- | ----------- |
| [Collaborateur du service de gestion des API](#api-management-service-contributor) | Gérer les services de gestion des API |
| [Collaborateur de composants Application Insights](#application-insights-component-contributor) | Gérer les composants Application Insights |
| [Opérateur Automation](#automation-operator) | Démarrer, arrêter, suspendre et reprendre les travaux |
| [Collaborateur BizTalk](#biztalk-contributor) | Gérer BizTalk Services |
| [Collaborateur de base de données ClearDB MySQL](#cleardb-mysql-db-contributor) | Gérer les bases de données ClearDB MySQL |
| [Collaborateur](#contributor) | Gérer tout sauf les accès |
| [Collaborateurs de fabrique de données](#data-factory-contributor) | Gérer les fabriques de données |
| [Utilisateur de DevTest Labs](#devtest-labs-user) | Afficher tout et connecter, démarrer, redémarrer et arrêter les machines virtuelles |
| [Collaborateur de compte DocumentDB](#documentdb-account-contributor) | Peut gérer les comptes DocumentDB |
| [Collaborateur de compte Intelligent Systems](#intelligent-systems-account-contributor) | Gérer les comptes Intelligent Systems |
| [Collaborateur de réseau](#network-contributor) | Gérer toutes les ressources réseau |
| [Collaborateur de compte NewRelic APM](#new-relic-apm-account-contributor) | Peut gérer les comptes et applications NewRelic Application Performance Management |
| [Propriétaire](#owner) | Gérer tout, y compris l’accès |
| [Lecteur](#reader) | Afficher tout, mais ne pas apporter de modifications |
| [Collaborateur Cache Redis](#redis-cache-contributor]) | Gérer les caches Redis |
| [Collaborateur des collections de travaux du planificateur](#scheduler-job-collections-contributor) | Gérer des collections de travaux du planificateur |
| [Collaborateur du service de recherche](#search-service-contributor) | Gérer les services de recherche |
| [Gestionnaire de sécurité](#security-manager) | Gérer les composants de sécurité, les stratégies de sécurité et les machines virtuelles |
| [Collaborateur de base de données SQL](#sql-db-contributor) | Gérer les bases de données SQL, mais pas leurs stratégies de sécurité |
| [Gestionnaire de sécurité SQL](#sql-security-manager) | Gérer les stratégies de sécurité des serveurs et bases de données SQL |
| [Collaborateur SQL Server](#sql-server-contributor) | Gérer les serveurs et bases de données SQL, mais pas leurs stratégies de sécurité |
| [Collaborateur de compte de stockage classique](#classic-storage-account-contributor) | Gérer les comptes de stockage classiques |
| [Collaborateur de compte de stockage](#storage-account-contributor) | Gérer les comptes de stockage |
| [Administrateur de l'accès utilisateur](#user-access-administrator) | Gérer l’accès utilisateur aux ressources Azure |
| [Collaborateur de machine virtuelle classique](#classic-virtual-machine-contributor) | Gérer les machines virtuelles classiques, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées |
| [Collaborateur de machine virtuelle](#virtual-machine-contributor) | Gérer les machines virtuelles, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées |
| [Collaborateur de réseau classique](#classic-network-contributor) | Gérer les réseaux virtuels classiques et les adresses IP réservées |
| [Collaborateur de plan web](#web-plan-contributor) | Gérer les plans web |
| [Collaborateur de site web](#website-contributor) | Gérer les sites web, mais pas les plans web auxquels ils sont connectés |

## Autorisations des rôles
Les tableaux suivants décrivent les autorisations spécifiques à chaque rôle. Cela peut inclure des propriétés **Actions** qui accordent des autorisations et **NotActions** qui restreignent les autorisations.

### Collaborateur du service de gestion des API
Gérer les services de gestion des API

| **Actions** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Créer et gérer les services de gestion des API |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les rôles et les affectations de rôles |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur des composants Application Insights
Gérer les composants Application Insights

| **Actions** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Insights/components/* | Créer et gérer les composants Insights |
| Microsoft.Insights/webtests/* | Créer et gérer les tests web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Opérateur Automation
Démarrer, arrêter, suspendre et reprendre les travaux

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Automation/automationAccounts/jobs/read | Lire les travaux des comptes Automation |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reprendre un travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Arrêter un travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Lire les flux de travail des comptes Automation |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspendre un travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobs/write | Écrire les travaux des comptes Automation |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Lire une planification de travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Lire une planification de travail de compte Automation |
| Microsoft.Automation/automationAccounts/read | Lire les comptes Automation |
| Microsoft.Automation/automationAccounts/runbooks/read | Lire les Runbooks Automation |
| Microsoft.Automation/automationAccounts/schedules/read | Lire les planifications de compte Automation |
| Microsoft.Automation/automationAccounts/schedules/write | Écrire les planifications de compte Automation |
| Microsoft.Insights/components/* | Créer et gérer les composants Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur BizTalk
Gérer BizTalk Services

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.BizTalkServices/BizTalk/* | Créer et gérer BizTalk Services |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de base de données ClearDB MySQL
Gérer les bases de données ClearDB MySQL

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| successbricks.cleardb/databases/* | Créer et gérer les bases de données ClearDB MySQL |

### Collaborateur
Gérer tout sauf les accès

| **Actions** ||
| ------- | ------ |
| * | Créer et gérer les ressources de tous les types |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Write | Impossible de créer des rôles et des affectations de rôles |
| Microsoft.Authorization/*/Delete | Impossible de supprimer des rôles et des affectations de rôles |

### Collaborateurs de fabrique de données
Gérer les fabriques de données

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.DataFactory/dataFactories/* | Gérer les fabriques de données |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Utilisateur de DevTest Labs
Afficher tout et connecter, démarrer, redémarrer et arrêter les machines virtuelles

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Compute/availabilitySets/read | Lire les propriétés des groupes à haute disponibilité |
| Microsoft.Compute/virtualMachines/*/read | Lire les propriétés d’une machine virtuelle (tailles de machine virtuelle, état de l’exécution, extensions de machine virtuelle, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Désallouer les machines virtuelles |
| Microsoft.Compute/virtualMachines/read | Lire les propriétés d’une machine virtuelle |
| Microsoft.Compute/virtualMachines/restart/action | Redémarrer les machines virtuelles |
| Microsoft.Compute/virtualMachines/start/action | Démarrer les machines virtuelles |
| Microsoft.DevTestLab/*/read | Lire les propriétés d’un laboratoire |
| Microsoft.DevTestLab/labs/createEnvironment/action | Créer un environnement de laboratoire |
| Microsoft.DevTestLab/labs/formulas/delete | Supprimer des formules |
| Microsoft.DevTestLab/labs/formulas/read | Lire des formules |
| Microsoft.DevTestLab/labs/formulas/write | Ajouter ou modifier des formules |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Évaluer des stratégies de laboratoire |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joindre un pool d’adresses principales d’équilibreur de charge |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joindre une règle NAT entrante d’équilibreur de charge |
| Microsoft.Network/networkInterfaces/*/read | Lire les propriétés d’une interface réseau (par ex. tous les équilibreurs de charge dont l’interface réseau fait partie) |
| Microsoft.Network/networkInterfaces/join/action | Joindre une machine virtuelle à une interface réseau |
| Microsoft.Network/networkInterfaces/read | Lire des interfaces réseau |
| Microsoft.Network/networkInterfaces/write | Écrire des interfaces réseau |
| Microsoft.Network/publicIPAddresses/*/read | Lire les propriétés d’une adresse IP publique |
| Microsoft.Network/publicIPAddresses/join/action | Joindre une adresse IP publique |
| Microsoft.Network/publicIPAddresses/read | Lire des adresses IP publiques réseau |
| Microsoft.Network/virtualNetworks/subnets/join/action | Joindre un réseau virtuel |
| Microsoft.Resources/deployments/operations/read | Lire des opérations de déploiement |
| Microsoft.Resources/deployments/read | Lire des déploiements |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/listKeys/action | Répertorier les clés de compte de stockage |

### Collaborateur de compte DocumentDB
Peut gérer les comptes DocumentDB

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.DocumentDb/databaseAccounts/* | Créer et gérer les comptes DocumentDB |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de compte Intelligent Systems
Gérer les comptes Intelligent Systems

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.IntelligentSystems/accounts/* | Créer et gérer les comptes Intelligent Systems |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de réseau
Gérer toutes les ressources réseau

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Network/* | Créer et gérer des réseaux |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de compte NewRelic APM
Peut gérer les comptes et applications NewRelic Application Performance Management

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| NewRelic.APM/accounts/* | Créer et gérer les comptes de gestion des performances des applications NewRelic |

### Propriétaire
Gérer tout, y compris l’accès

| **Actions** ||
| ------- | ------ |
| * | Créer et gérer les ressources de tous les types |

### Lecteur
Afficher tout, mais ne pas apporter de modifications

| **Actions** ||
| ------- | ------ |
| **/read | Lire les ressources de tous les types, à l’exception des secrets. |

### Collaborateur Redis Cache
Gérer les caches Redis

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Cache/redis/* | Créer et gérer les caches Redis |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur des collections de travaux du planificateur
Gérer des collections de travaux du planificateur

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Scheduler/jobcollections/* | Créer et gérer des collections de travaux |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur du service de recherche
Gérer les services de recherche

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Search/searchServices/* | Créer et gérer les services de recherche |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Gestionnaire de sécurité
Gérer les composants de sécurité, les stratégies de sécurité et les machines virtuelles

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.ClassicCompute/*/read | Lire les informations de configuration relatives aux machines virtuelles de calcul standard |
| Microsoft.ClassicCompute/virtualMachines/*/write | Écrire la configuration des machines virtuelles |
| Microsoft.ClassicNetwork/*/read | Lire les informations de configuration relatives au réseau classique |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Security/* | Créer et gérer des stratégies et des composants de sécurité |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de base de données SQL
Gérer les bases de données SQL, mais pas leurs stratégies de sécurité

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Sql/servers/databases/* | Créer et gérer les bases de données SQL |
| Microsoft.Sql/servers/read | Lire les serveurs SQL |
| Microsoft.Support/* | Créer et gérer les tickets de support |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit |
| Microsoft.Sql/servers/databases/auditingSettings/* | Impossible de modifier les paramètres d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier les stratégies de masquage des données |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossible de modifier les stratégies d’alerte de sécurité |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité |

### Gestionnaire de sécurité SQL
Gérer les stratégies de sécurité des serveurs et bases de données SQL

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture Microsoft |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Sql/servers/auditingPolicies/* | Créer et gérer les stratégies d’audit de serveur SQL |
| Microsoft.Sql/servers/auditingSettings/* | Créer et gérer les paramètres d’audit de serveur SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Créer et gérer les stratégies d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Créer et gérer les paramètres d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Créer et gérer les stratégies de connexion de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Créer et gérer les stratégies de masquage de données de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/read | Lire les bases de données SQL |
| Microsoft.Sql/servers/databases/schemas/read | Lire les schémas de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Lire les colonnes de table de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/schemas/tables/read | Lire les tables de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Créer et gérer les mesures de sécurité de base de données de serveur SQL |
| Microsoft.Sql/servers/read | Lire les serveurs SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de serveur SQL |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur SQL Server
Gérer les serveurs et bases de données SQL, mais pas leurs stratégies de sécurité

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture|
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Sql/servers/* | Créer et gérer les serveurs SQL |
| Microsoft.Support/* | Créer et gérer les tickets de support |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Impossible de modifier les stratégies d’audit de serveur SQL |
| Microsoft.Sql/servers/auditingSettings/* | Impossible de modifier les paramètres d’audit de serveur SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Impossible de modifier les paramètres d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier les stratégies de masquage de données de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossible de modifier les stratégies d’alerte de sécurité de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité de base de données de serveur SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* | Impossible de modifier les stratégies d’alerte de sécurité de serveur SQL |

### Collaborateur de compte de stockage classique
Gérer les comptes de stockage classiques

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicStorage/storageAccounts/* | Créer et gérer les comptes de stockage |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de compte de stockage
Peut gérer les comptes de stockage, mais pas y accéder.

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture totale |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Insights/diagnosticSettings/* | Gérer les paramètres de diagnostic |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Storage/storageAccounts/* | Créer et gérer les comptes de stockage |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Administrateur de l'accès utilisateur
Gérer l’accès utilisateur aux ressources Azure

| **Actions** ||
| ------- | ------ |
| */read | Lire les ressources de tous les types, à l’exception des secrets. |
| Microsoft.Authorization/* | Autorisation de lecture |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de machine virtuelle classique
Gérer les machines virtuelles classiques, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicCompute/domainNames/* | Créer et gérer des noms de domaine de calcul classique |
| Microsoft.ClassicCompute/virtualMachines/* | Créer et gérer les machines virtuelles |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Joindre des groupes de sécurité réseau |
| Microsoft.ClassicNetwork/reservedIps/link/action | Lier les adresses IP réservées |
| Microsoft.ClassicNetwork/reservedIps/read | Lire les adresses IP réservées |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Rejoindre des réseaux virtuels |
| Microsoft.ClassicNetwork/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Lire les disques de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/images/read | Lire les images de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorier les clés de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/read | Lire les comptes de stockage classiques |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de machine virtuelle
Gérer les machines virtuelles, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Compute/availabilitySets/* | Créer et gérer des groupes à haute disponibilité de calcul |
| Microsoft.Compute/locations/* | Créer et gérer des emplacements de calcul |
| Microsoft.Compute/virtualMachines/* | Créer et gérer les machines virtuelles |
| Microsoft.Compute/virtualMachineScaleSets/* | Créez et gérez des jeux de mise à l’échelle des machines virtuelles |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joindre des pools d’adresses principales de passerelle d’application réseau |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joindre des pools d’adresses principales d’équilibreur de charge |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joindre des pools NAT entrants d’équilibreur de charge |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joindre des règles NAT entrantes d’équilibreur de charge |
| Microsoft.Network/loadBalancers/read | Lire les équilibreurs de charge |
| Microsoft.Network/locations/* | Créer et gérer des emplacements réseau |
| Microsoft.Network/networkInterfaces/* | Créer et gérer des interfaces réseau |
| Microsoft.Network/networkSecurityGroups/join/action | Joindre des groupes de sécurité réseau |
| Microsoft.Network/networkSecurityGroups/read | Lire les groupes de sécurité réseau |
| Microsoft.Network/publicIPAddresses/join/action | Joindre des adresses IP publiques réseau |
| Microsoft.Network/publicIPAddresses/read | Lire des adresses IP publiques réseau |
| Microsoft.Network/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.Network/virtualNetworks/subnets/join/action | Joindre des sous-réseaux de réseau virtuel |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Storage/storageAccounts/listKeys/action | Répertorier les clés de compte de stockage |
| Microsoft.Storage/storageAccounts/read | Lire les comptes de stockage |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de réseau classique
Gérer les réseaux virtuels classiques et les adresses IP réservées

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicNetwork/* | Créer et gérer des réseaux classiques |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de plan web
Gérer les plans web

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.Web/serverFarms/* | Créer et gérer des batteries de serveurs |

### Collaborateur de site web
Gérer les sites web, mais pas les plans web auxquels ils sont connectés

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Insights/components/* | Créer et gérer les composants Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’intégrité des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources | Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.Web/certificates/* | Créer et gérer les certificats de site web |
| Microsoft.Web/listSitesAssignedToHostName/read | Lire les sites affectés à un nom d’hôte |
| Microsoft.Web/serverFarms/join/action | Joindre des batteries de serveurs |
| Microsoft.Web/serverFarms/read | Lire des batteries de serveurs |
| Microsoft.Web/sites/* | Créer et gérer les sites web |

## Voir aussi
- [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md) : découvrez le Contrôle d’accès en fonction du rôle Azure dans le portail Azure.
- [Rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure](role-based-access-control-custom-roles.md) : découvrez comment créer des rôles personnalisés selon vos besoins en matière d’accès.
- [Créer un rapport d’historique des modifications d’accès](role-based-access-control-access-change-history-report.md) : effectuez le suivi des changements d’affection de rôle dans RBAC.
- [Résolution des problèmes de contrôle d’accès en fonction du rôle](role-based-access-control-troubleshooting.md) : obtenez des suggestions pour résoudre les problèmes courants.

<!---HONumber=AcomDC_0525_2016-->