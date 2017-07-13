


# Vue d'ensemble


## [Découvrir Service Fabric](service-fabric-overview.md)


## [Comprendre les microservices](service-fabric-overview-microservices.md)


## [Grandes lignes](service-fabric-content-roadmap.md)


## [Scénarios d’application](service-fabric-application-scenarios.md)


## [Schémas et scénarios](service-fabric-patterns-and-scenarios.md)


## [Architecture](service-fabric-architecture.md)


## [Terminologie](service-fabric-technical-overview.md)



# Prise en main


## Configuration de l'environnement de développement


### [Windows](service-fabric-get-started.md)


### [Linux](service-fabric-get-started-linux.md)


### [Mac OS](service-fabric-get-started-mac.md)


## Créer votre première application


### [C# sur Windows](service-fabric-create-your-first-application-in-visual-studio.md)


### [Java sur Linux](service-fabric-create-your-first-linux-application-with-java.md)


### [C# sur Linux](service-fabric-create-your-first-linux-application-with-csharp.md)


## [Déployer des applications sur un cluster local](service-fabric-get-started-with-a-local-cluster.md)


## [Déployer des applications .NET dans un conteneur](service-fabric-host-app-in-a-container.md)


## [Créer votre premier cluster sur Azure](service-fabric-get-started-azure-cluster.md)


## [Créer votre premier cluster autonome](service-fabric-get-started-standalone-cluster.md)


## [Créer votre première application de conteneur](service-fabric-get-started-containers.md)



# Procédure


## Générer une application
  


### Concepts


#### [Modèles de programmation pris en charge](service-fabric-choose-framework.md)


#### [Modèle d'application](service-fabric-application-model.md)


#### [Modèle d’hébergement](service-fabric-hosting-model.md)


#### [Ressources du manifeste de service](service-fabric-service-manifest-resources.md)


#### [État du service](service-fabric-concepts-state.md)


#### [Partitionnement du service](service-fabric-concepts-partitioning.md)


#### [Disponibilité des services](service-fabric-availability-services.md)


#### [Extensibilité des applications](service-fabric-concepts-scalability.md)


#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)



### [Planifier la capacité de l’application](service-fabric-capacity-planning.md)



### Générer un service invité exécutable


#### [Déployer un fichier exécutable invité](service-fabric-deploy-existing-app.md)


#### [Déploiement de plusieurs exécutables invités](service-fabric-deploy-multiple-apps.md)



### Générer un service de conteneur


#### [Vue d'ensemble](service-fabric-containers-overview.md)


#### [Déployer un conteneur de Windows](service-fabric-deploy-container.md)


#### [Déployer un conteneur Linux](service-fabric-deploy-container-linux.md)


#### [Docker Compose (version préliminaire)](service-fabric-docker-compose.md)


#### [Gestion des ressources pour les conteneurs et les services](service-fabric-resource-governance.md)


#### [Pilotes de journalisation et de volume](service-fabric-containers-volume-logging-drivers.md)



### Générer un service Reliable Service


#### [Vue d'ensemble](service-fabric-reliable-services-introduction.md)


#### Concepts


##### [Cycle de vie des Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)


##### [Cycle de vie des Reliable Services - Java](service-fabric-reliable-services-lifecycle-java.md)



#### Collections fiables


##### [Collections fiables](service-fabric-reliable-services-reliable-collections.md)


##### [Instructions et recommandations relatives aux collections fiables](service-fabric-reliable-services-reliable-collections-guidelines.md)


##### [Utilisation des collections fiables](service-fabric-work-with-reliable-collections.md)


##### [Transactions et verrous](service-fabric-reliable-services-reliable-collections-transactions-locks.md)


##### [File d’attente simultanée fiable](service-fabric-reliable-services-reliable-concurrent-queue.md)


##### [Sérialisation de Collection fiable](service-fabric-reliable-services-reliable-collections-serialization.md)


##### [Gestionnaire d’état fiable et éléments internes de collections fiables](service-fabric-reliable-services-reliable-collections-internals.md)



#### Prise en main


##### [C# sur Windows](service-fabric-reliable-services-quick-start.md)


##### [Java sur Linux](service-fabric-reliable-services-quick-start-java.md)



#### [Configurer](service-fabric-reliable-services-configuration.md)


#### [Envoi de notifications](service-fabric-reliable-services-notifications.md)


#### [Sauvegarde et restauration](service-fabric-reliable-services-backup-restore.md)



#### Communiquer avec les services


##### [Communiquer avec Reliable Services](service-fabric-reliable-services-communication.md)


##### [Communication à distance du service - C#](service-fabric-reliable-services-communication-remoting.md)


##### [Communication à distance du service - Java](service-fabric-reliable-services-communication-remoting-java.md)


##### [WCF](service-fabric-reliable-services-communication-wcf.md)


##### [Communications sécurisées - C#](service-fabric-reliable-services-secure-communication.md)


##### [Communications sécurisées - Java](service-fabric-reliable-services-secure-communication-java.md)



#### [Utilisateurs avancés](service-fabric-reliable-services-advanced-usage.md)



### Générer un service Reliable Actor


#### [Vue d'ensemble](service-fabric-reliable-actors-introduction.md)


#### Concepts


##### [Architecture](service-fabric-reliable-actors-platform.md)


##### [Cycle de vie et garbage collection](service-fabric-reliable-actors-lifecycle.md)


##### [Gestion des états](service-fabric-reliable-actors-state-management.md)


##### [Polymorphisme](service-fabric-reliable-actors-polymorphism.md)


##### [Réentrance](service-fabric-reliable-actors-reentrancy.md)


##### [Sérialisation de type](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)



#### Prise en main


##### [C# sur Windows](service-fabric-reliable-actors-get-started.md)


##### [Java sur Linux](service-fabric-reliable-actors-get-started-java.md)



#### [Envoi de notifications](service-fabric-reliable-actors-events.md) 


#### [Définir des minuteries et des rappels](service-fabric-reliable-actors-timers-reminders.md)


#### [Configurer KVSActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)


#### [Configurer les paramètres de communication](service-fabric-reliable-actors-fabrictransportsettings.md) 


#### [Configurer ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)



### Communiquer avec les services


#### [Modèle de communication de service](service-fabric-connect-and-communicate-with-services.md)


#### [Service DNS](service-fabric-dnsservice.md)


#### [Proxy inversé](service-fabric-reverseproxy.md)


#### [Configurer le proxy inverse pour une communication sécurisée](service-fabric-reverseproxy-configure-secure-communication.md)



### [Ajouter un serveur web frontal](service-fabric-add-a-web-frontend.md)



### Travailler dans un IDE


#### [Prise en main du plug-in Eclipse pour le développement Java](service-fabric-get-started-eclipse.md)


#### [Gérer les applications dans Visual Studio](service-fabric-manage-application-in-visual-studio.md)


#### [Configurer des connexions sécurisées dans Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)


#### [Configurer votre application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md)



### Configurer la sécurité


#### [Gérer des secrets d’application](service-fabric-application-secret-management.md)  


#### [Configurer les stratégies de sécurité de votre application](service-fabric-application-runas-security.md)



### Déboguer


#### [Déboguer un service C# dans Visual Studio](service-fabric-debugging-your-application.md)


#### [Déboguer un service Java dans Eclipse](service-fabric-debugging-your-application-java.md)


#### [Erreurs et exceptions courantes](service-fabric-errors-and-exceptions.md)



### Surveiller et diagnostiquer localement


#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)


#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)



### Intégration avec la gestion des API


#### [Vue d'ensemble](service-fabric-api-management-overview.md)


#### [Démarrage rapide](service-fabric-api-management-quick-start.md)



### Migrer à partir de Services cloud


#### [Comparer Services cloud et Service Fabric](service-fabric-cloud-services-migration-differences.md)


#### [Migrer vers le Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)


#### [Pratiques recommandées](/azure/architecture/service-fabric/migrate-from-cloud-services)



## Gérer le cycle de vie de l’application


### [Vue d'ensemble](service-fabric-application-lifecycle.md)


### [Empaqueter une application](service-fabric-package-apps.md)


### [Comprendre le paramètre ImageStoreConnectionString](service-fabric-image-store-connection-string.md)


### Déployer ou supprimer des applications


#### [PowerShell](service-fabric-deploy-remove-applications.md)


#### [Azure CLI 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md)


#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)


#### [API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)


### Mettre à niveau une application


#### [Vue d'ensemble](service-fabric-application-upgrade.md)


#### [Configurer la mise à niveau d’une application](service-fabric-visualstudio-configure-upgrade.md)


#### [Paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md)


#### Mise à niveau


##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)


##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)


#### [Résoudre les problèmes de mises à niveau d’application](service-fabric-application-upgrade-troubleshooting.md)


#### [Sérialisation de données lors des mises à niveau de d’application](service-fabric-application-upgrade-data-serialization.md)


#### [Rubriques avancées sur la mise à niveau d’application](service-fabric-application-upgrade-advanced.md)



### Tester des applications et services


#### [Présentation du service d’analyse des erreurs](service-fabric-testability-overview.md)


#### [Tester la communication entre les services](service-fabric-testability-scenarios-service-communication.md)


#### Simuler des défaillances


##### [Utilisation d’un chaos contrôlé](service-fabric-controlled-chaos.md)


##### [Utilisation d’actions de test](service-fabric-testability-actions.md)


##### [Au cours des charges de travail](service-fabric-testability-workload-tests.md)


##### [Utilisation de scénarios de test](service-fabric-testability-scenarios.md)


##### [Utilisation des API de transition de nœud](service-fabric-node-transition-apis.md)


#### [Tester la charge de votre application](service-fabric-vso-load-test.md)



### Configurer l’intégration continue


#### [Configurer l’intégration continue avec VSTS](service-fabric-set-up-continuous-integration.md)


#### [Déployer votre application Linux Java à l’aide de Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)



## Créer et gérer des clusters



### [Vue d'ensemble](service-fabric-deploy-anywhere.md)


### Concepts


#### [Décrire un cluster](service-fabric-cluster-resource-manager-cluster-description.md)


#### [Sécurité des clusters](service-fabric-cluster-security.md)


#### [Différences de fonctionnalités entre Linux et Windows](service-fabric-linux-windows-differences.md)



### Planifier et préparer


#### [Planification de la capacité](service-fabric-cluster-capacity.md)


#### [Récupération d’urgence](service-fabric-disaster-recovery.md)



### Clusters sur Azure


#### Concepts


##### [Types de nœud et groupes de machines virtuelles identiques dans le cluster](service-fabric-cluster-nodetypes.md)


##### [Modèles de mise en réseau de cluster](service-fabric-patterns-networking.md)


#### Créer 


##### [Portail Azure](service-fabric-cluster-creation-via-portal.md)


##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)


##### [Visual Studio et Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)


#### Mettre à l'échelle 


##### [Manuellement](service-fabric-cluster-scale-up-down.md)


##### [Par programmation](service-fabric-cluster-programmatic-scaling.md)


#### [Mettre à niveau](service-fabric-cluster-upgrade.md)


#### [Définir un contrôle d’accès](service-fabric-cluster-security-roles.md)


#### [Configurer](service-fabric-cluster-fabric-settings.md)


#### [Gérer les certificats de cluster](service-fabric-cluster-security-update-certs-azure.md) 


#### [Supprimer](service-fabric-cluster-delete.md)



### Clusters autonomes


#### [Planifier et préparer votre déploiement](service-fabric-cluster-standalone-deployment-preparation.md)


#### Créer


##### [Créer localement](service-fabric-cluster-creation-for-windows-server.md)


##### [Créer sur des machines virtuelles Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)


##### [Sécuriser à l’aide de certificats](service-fabric-windows-cluster-x509-security.md)  


##### [Sécuriser à l’aide de la sécurité de Windows](service-fabric-windows-cluster-windows-security.md)


##### [Contenu du package autonome](service-fabric-cluster-standalone-package-contents.md)


#### [Mettre à l'échelle](service-fabric-cluster-windows-server-add-remove-nodes.md)


#### [Définir un contrôle d’accès](service-fabric-cluster-security-roles.md)


#### [Configurer](service-fabric-cluster-manifest.md)


#### [Mettre à niveau](service-fabric-cluster-upgrade-windows-server.md) 



### [Visualiser un cluster](service-fabric-visualizing-your-cluster.md)


### [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md)



### [Gérer un cluster à l’aide de l’interface de ligne de commande XPlat](service-fabric-azure-cli.md)


### [Gérer un cluster à l’aide des commandes 2.0 de l’interface de ligne de commande Azure](service-fabric-azure-cli-2-0.md)


### [Corriger des nœuds de cluster](service-fabric-patch-orchestration-application.md)



### Gérer et orchestrer les ressources de cluster


#### [Présentation de Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)


#### [Architecture Cluster Resource Manager](service-fabric-cluster-resource-manager-architecture.md)


#### [Décrire un cluster](service-fabric-cluster-resource-manager-cluster-description.md)


#### [Vue d’ensemble des groupes d’applications](service-fabric-cluster-resource-manager-application-groups.md)


#### [Configurer les paramètres du Gestionnaire de ressources de Cluster](service-fabric-cluster-resource-manager-configure-services.md)


#### [Mesures de la consommation de ressources](service-fabric-cluster-resource-manager-metrics.md)


#### [Utiliser l’affinité de service](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)


#### [Stratégies de positionnement de service](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)


#### [Gérer un cluster](service-fabric-cluster-resource-manager-management-integration.md)


#### [Défragmentation de cluster](service-fabric-cluster-resource-manager-defragmentation-metrics.md)


#### [Équilibrer un cluster](service-fabric-cluster-resource-manager-balancing.md)


#### [Limitation](service-fabric-cluster-resource-manager-advanced-throttling.md)


#### [Mouvement de service](service-fabric-cluster-resource-manager-movement-cost.md)



## Surveiller et diagnostiquer


### [Surveiller et diagnostiquer des applications](service-fabric-diagnostics-overview.md)


### Générer des événements


#### [Générer des événements de niveau infrastructure](service-fabric-diagnostics-event-generation-infra.md)


##### [Événements Reliable Services](service-fabric-reliable-services-diagnostics.md)


##### [Événements Reliable Actors](service-fabric-reliable-actors-diagnostics.md)


#### [Générer des événements de niveau application](service-fabric-diagnostics-event-generation-app.md)


### Intégrité du cluster et de l’application


#### [Surveiller l’intégrité du Service Fabric](service-fabric-health-introduction.md)


#### [Signaler et contrôler l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)


#### [Ajouter des rapports de contrôle de l’état](service-fabric-report-health.md)


#### [Résoudre les problèmes avec les rapports d’intégrité du système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)


#### [Afficher des rapports d’intégrité](service-fabric-view-entities-aggregated-health.md)


### Agréger des événements


#### [Agréger des événements avec EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)


#### Agréger des événements avec Azure Diagnostics


##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)


##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)


### Analyser des événements


#### [Analyser des événements avec Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)


#### [Analyser des événements avec OMS](service-fabric-diagnostics-event-analysis-oms.md)


### [Résoudre les problèmes de votre cluster local](service-fabric-troubleshoot-local-cluster-setup.md)



# Référence


## [PowerShell (Azure)](/powershell/module/azurerm.servicefabric/)


## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)


## [Interface de ligne de commande Azure](/cli/azure/sf)


## [API Java](/java/api/overview/azure/servicefabric)


## [.NET](/dotnet/api/overview/azure/servicefabric?view=azure-dotnet)


## [REST](/rest/api/servicefabric)



# Ressources


## [Feuille de route Azure](https://azure.microsoft.com/roadmap/)


## [Questions courantes sur Service Fabric](service-fabric-common-questions.md)


## [Parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)


## [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)


## [Tarification](https://azure.microsoft.com/pricing/details/service-fabric/)


## [Exemple de code](http://aka.ms/servicefabricsamples)


## [Options de support de Azure Service Fabric](service-fabric-support.md)


## [Mises à jour de service](https://azure.microsoft.com/updates/?product=service-fabric)


## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
