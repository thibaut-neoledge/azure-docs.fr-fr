<properties
	pageTitle="RBAC : rôles intégrés | Microsoft Azure"
	description="Cette rubrique décrit les rôles intégrés pour le contrôle d’accès en fonction du rôle (RBAC)."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/04/2016"
	ms.author="inhenk"/>

#RBAC : rôles intégrés

## Rôles intégrés

Le contrôle d'accès basé sur un rôle inclut les trois rôles intégrés suivants qui peuvent être affectés aux utilisateurs, groupes et services. Vous ne pouvez pas modifier la définition des rôles intégrés. Dans une version ultérieure d'Azure RBAC, vous pourrez définir des rôles personnalisés en composant un ensemble d'actions à partir d'une liste des actions applicables aux ressources Azure.

Cliquez sur le lien correspondant pour voir les propriétés **actions** et **not actions** d'une définition de rôle. La propriété **actions** spécifie les actions autorisées sur les ressources Azure. Les chaînes d'action peuvent utiliser des caractères génériques. La propriété **not actions** d'une définition de rôle spécifie les actions qui doivent être exclues des actions autorisées.


| Nom de rôle | Description |
| --------- | ----------- |
| [Collaborateur du service de gestion des API](#api-management-service-contributor) | Gérer les services de gestion des API |
| [Collaborateur de composants Application Insights](#application-insights-component-contributor) | Gérer les composants Application Insights |
| [Opérateur Automation](#automation-operator) | Démarrer, arrêter, suspendre et reprendre les travaux |
| [Collaborateur BizTalk](#biztalk-contributor) | Gérer BizTalk Services |
| [Collaborateur de base de données ClearDB MySQL](#cleardb-mysql-db-contributor) | Gérer les bases de données ClearDB MySQL |
| [Collaborateur](#contributor) | Gérer tout sauf les accès |
| [Collaborateurs de fabrique de données](#data-factory-contributor) | Gérer les fabriques de données |
| [Utilisateur DevTest Lab](#devtest-lab-user) | Afficher tout et connecter, démarrer, redémarrer et arrêter les machines virtuelles |
| [Collaborateur de compte de base de données de document](#document-db-account-contributor) | Gérer les comptes DocumentDB |
| [Collaborateur de compte Intelligent Systems](#intelligent-systems-account-contributor) | Gérer les comptes Intelligent Systems |
| [Collaborateur de réseau](#network-contributor) | Gérer toutes les ressources réseau |
| [Collaborateur de compte NewRelic APM](#newrelic-apm-account-contributor) | Gérer les comptes et applications NewRelic Application Performance Management |
| [Propriétaire](#owner) | Les propriétaires peuvent tout gérer, y compris l’accès. |
| [Lecteur](#reader) | Les lecteurs peuvent tout afficher, mais pas apporter de modifications. |
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

### Collaborateur du service de gestion des API
Gérer les services de gestion des API

| **Actions** | |
| ------- | ------ |
| Microsoft.ApiManagement/Services/* | Créer et gérer les services de gestion des API |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur des composants Application Insights
Gérer les composants Application Insights

| **Actions** | |
| ------- | ------ |
| Microsoft.Insights/components/* | Créer et gérer les composants Insights |
| Microsoft.Insights/webtests/* | Créer et gérer les tests web |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Opérateur Automation
Les opérateurs Automation peuvent démarrer, arrêter, suspendre et reprendre les travaux.

| **Actions** ||
| ------- | ------ |
| Microsoft.Automation/automationAccounts/read | Lire les comptes Automation |
| Microsoft.Automation/automationAccounts/runbooks/read | Lire les Runbooks Automation |
| Microsoft.Automation/automationAccounts/schedules/read | Lire les planifications de compte Automation |
| Microsoft.Automation/automationAccounts/schedules/write | Écrire les planifications de compte Automation |
| Microsoft.Automation/automationAccounts/jobs/read | Lire les travaux des comptes Automation |
| Microsoft.Automation/automationAccounts/jobs/write | Écrire les travaux des comptes Automation |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Arrêter un travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspendre un travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reprendre un travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Lire une planification de travail de compte Automation |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Lire une planification de travail de compte Automation |

### Collaborateur BizTalk
Gérer BizTalk Services

| **Actions** ||
| ------- | ------ |
| Microsoft.BizTalkServices/BizTalk/* | Créer et gérer BizTalk Services |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de base de données ClearDB MySQL
Gérer les bases de données ClearDB MySQL

| **Actions** ||
| ------- | ------ |
| successbricks.cleardb/databases/* | Créer et gérer les bases de données ClearDB MySQL |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur
Les collaborateurs peuvent gérer tout sauf les accès

| **Actions** ||
| ------- | ------ |
| * | Créer et gérer les ressources de tous les types |
| ****Non-actions |  |
| Microsoft.Authorization/*/Write | Impossible de créer des rôles et des affectations de rôles|
| Microsoft.Authorization/*/Delete | Impossible de supprimer des rôles et des affectations de rôles |

### Collaborateurs de fabrique de données
Gérer les fabriques de données

| **Actions** ||
| ------- | ------ |
| Microsoft.DataFactory/dataFactories/* | Créer et gérer les fabriques de données |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Utilisateur DevTest Lab
Afficher tout et connecter, démarrer, redémarrer et arrêter les machines virtuelles

| **Actions** ||
| ------- | ------ |
| */read | Lire les ressources de tous les types | | Microsoft.DevTestLab/labs/labStats/action | Lire les statistiques des laboratoires | | Microsoft.DevTestLab/Environments/* | Créer et gérer des environnements |
| Microsoft.DevTestLab/labs/createEnvironment/action | Créer un environnement de laboratoire |
| Microsoft.Compute/virtualMachines/start/action | Démarrer les machines virtuelles |
| Microsoft.Compute/virtualMachines/restart/action | Redémarrer les machines virtuelles |
| Microsoft.Compute/virtualMachines/deallocate/action | Désallouer les machines virtuelles |
| Microsoft.Storage/storageAccounts/listKeys/action | Répertorier les clés de compte de stockage |
| Microsoft.Network/virtualNetworks/join/action | Rejoindre des réseaux virtuels |
| Microsoft.Network/loadBalancers/join/action | Rejoindre des équilibreurs de charge |
| Microsoft.Network/publicIPAddresses/link/action | Lier à des adresses IP publiques |
| Microsoft.Network/networkInterfaces/link/action | Lier à des interfaces réseau |
| Microsoft.Network/networkInterfaces/write | Écrire des interfaces réseau |

### Collaborateur de compte de base de données de document
Gérer les comptes DocumentDB

| **Actions** ||
| ------- | ------ |
| Microsoft.DocumentDb/databaseAccounts/* | Créer et gérer les comptes DocumentDB |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de compte Intelligent Systems
Gérer les comptes Intelligent Systems

| **Actions** ||
| ------- | ------ |
| Microsoft.IntelligentSystems/accounts/* | Créer et gérer les comptes Intelligent Systems |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de réseau
Gérer toutes les ressources réseau

| **Actions** ||
| ------- | ------ |
| Microsoft.Network/* | Créer et gérer des réseaux |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de compte NewRelic APM
Gérer les comptes et applications NewRelic Application Performance Management

| **Actions** ||
| ------- | ------ |
| NewRelic.APM/accounts/* | Créer et gérer les comptes de gestion des performances des applications NewRelic |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

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
| Microsoft.Cache/redis/* | Créer et gérer les caches Redis |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur des collections de travaux du planificateur
Gérer des collections de travaux du planificateur

| **Actions** ||
| ------- | ------ |
| Microsoft.Scheduler/jobcollections/* | Créer et gérer des collections de travaux |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur du service de recherche
Gérer les services de recherche

| **Actions** ||
| ------- | ------ |
| Microsoft.Search/searchServices/* | Créer et gérer les services de recherche |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Gestionnaire de sécurité
Gérer les composants de sécurité, les stratégies de sécurité et les machines virtuelles

| **Actions** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/*/read | Lire les informations de configuration relatives au réseau classique |
| Microsoft.ClassicCompute/*/read | Lire les informations de configuration relatives aux machines virtuelles de calcul standard |
| Microsoft.ClassicCompute/virtualMachines/*/write | Écrire la configuration des machines virtuelles |
| Microsoft.Security/* | Créer et gérer des stratégies et des composants de sécurité |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de base de données SQL
Gérer les bases de données SQL, mais pas leurs stratégies de sécurité

| **Actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | Lire les serveurs SQL |
| Microsoft.Sql/servers/databases/* | Créer et gérer les bases de données SQL |
| Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources du groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources |
| Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| **Non-actions** | |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier les stratégies de masquage des données |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité |

### Gestionnaire de sécurité SQL
Gérer les stratégies de sécurité des serveurs et bases de données SQL

| **Actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | Lire les serveurs SQL |
| Microsoft.Sql/servers/auditingPolicies/* | Créer et gérer les stratégies d’audit de serveur SQL |
| Microsoft.Sql/servers/databases/read | Lire les bases de données SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Créer et gérer les stratégies d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Créer et gérer les stratégies de connexion de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Créer et gérer les stratégies de masquage de données de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Créer et gérer les mesures de sécurité de base de données de serveur SQL |
| Microsoft.Authorization/*/read | Autorisation de lecture Microsoft |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources des groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources des abonnements |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.Sql/servers/databases/schemas/read | Lire les schémas de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/schemas/tables/read | Lire les tables de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Lire les colonnes de table de base de données de serveur SQL |

### Collaborateur SQL Server
Gérer les serveurs et bases de données SQL, mais pas leurs stratégies de sécurité

| **Actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/* | Créer et gérer les serveurs SQL |
| Microsoft.Authorization/*/read | Autorisation de lecture|
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources des groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources des abonnements |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| **Non-actions** | |
| Microsoft.Sql/servers/auditingPolicies/* | Impossible de modifier les stratégies d’audit de serveur SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier les stratégies de masquage de données de base de données de serveur SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité de base de données de serveur SQL |

### Collaborateur de compte de stockage classique
Gérer les comptes de stockage classiques

| **Actions** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/* | Créer et gérer les comptes de stockage |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Resources/subscriptions/resources/read | Lire les ressources de l'abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources des groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources de l’abonnement |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de compte de stockage
Gérer les comptes de stockage

| **Actions** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/* | Créer et gérer les comptes de stockage |
| Microsoft.Authorization/*/read | Autorisation de lecture totale |
| Microsoft.Resources/subscriptions/resources/read | Lire les ressources de l'abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources des groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources de l’abonnement |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
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
| Microsoft.ClassicStorage/storageAccounts/read | Lire les comptes de stockage classiques |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorier les clés de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Lire les disques de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/images/read | Lire les images de compte de stockage |
| Microsoft.ClassicNetwork/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.ClassicNetwork/reservedIps/read | Lire les adresses IP réservées |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Rejoindre des réseaux virtuels |
| Microsoft.ClassicNetwork/reservedIps/link/action | Lier les adresses IP réservées |
| Microsoft.ClassicCompute/domainNames/* | Créer et gérer des noms de domaine de calcul classique |
| Microsoft.ClassicCompute/virtualMachines/* | Créer et gérer les machines virtuelles |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources des groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources de l’abonnement |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de machine virtuelle
Gérer les machines virtuelles, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées

| **Actions** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/read | Lire les comptes de stockage |
| Microsoft.Storage/storageAccounts/listKeys/action | Répertorier les clés de compte de stockage |
| Microsoft.Network/virtualNetworks/read | Lire les réseaux virtuels |
| Microsoft.Network/virtualNetworks/subnets/join/action | Joindre des sous-réseaux de réseau virtuel |
| Microsoft.Network/loadBalancers/read | Lire les équilibreurs de charge |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joindre des pools d’adresses principales d’équilibreur de charge |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joindre des règles NAT entrantes d’équilibreur de charge |
| Microsoft.Network/publicIPAddresses/read | Lire des adresses IP publiques réseau |
| Microsoft.Network/publicIPAddresses/join/action | Joindre des adresses IP publiques réseau |
| Microsoft.Network/networkSecurityGroups/read | Lire les groupes de sécurité réseau |
| Microsoft.Network/networkSecurityGroups/join/action | Joindre des groupes de sécurité réseau |
| Microsoft.Network/networkInterfaces/* | Créer et gérer des interfaces réseau |
| Microsoft.Network/locations/* | Créer et gérer des emplacements réseau |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joindre des pools d’adresses principales de passerelle d’application réseau |
| Microsoft.Compute/virtualMachines/* | Créer et gérer les machines virtuelles |
| Microsoft.Compute/availabilitySets/* | Créer et gérer des groupes à haute disponibilité de calcul |
| Microsoft.Compute/locations/* | Créer et gérer des emplacements de calcul |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources des groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources de l’abonnement |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de réseau classique
Gérer les réseaux virtuels classiques et les adresses IP réservées

| **Actions** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/* | Créer et gérer des réseaux classiques |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources des groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources de l’abonnement |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de plan web
Gérer les plans web

| **Actions** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/* | Créer et gérer des batteries de serveurs |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources des groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources de l’abonnement |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Support/* | Créer et gérer les tickets de support |

### Collaborateur de site web
Gérer les sites web, mais pas les plans web auxquels ils sont connectés

| **Actions** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/read | Lire des batteries de serveurs |
| Microsoft.Web/serverFarms/join/action | Joindre des batteries de serveurs |
| Microsoft.Web/sites/* | Créer et gérer les sites web |
| Microsoft.Web/certificates/* | Créer et gérer les certificats de site web |
| Microsoft.Web/listSitesAssignedToHostName/read | Lire les sites affectés à un nom d’hôte |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lire les ressources des groupes de ressources de l’abonnement |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Créer et gérer les déploiements de groupes de ressources de l’abonnement |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
| Microsoft.Support/* | Créer et gérer les tickets de support |
| Microsoft.Insights/components/* | Créer et gérer les composants Insights |

## Rubriques RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0107_2016-->