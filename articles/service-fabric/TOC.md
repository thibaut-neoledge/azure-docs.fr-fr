
# Vue d'ensemble
## [Découvrir Service Fabric](service-fabric-overview.md)
## [Comprendre les microservices](service-fabric-overview-microservices.md)
## [Scénarios d’application](service-fabric-application-scenarios.md)
## [Architecture](service-fabric-architecture.md)
## [Terminologie](service-fabric-technical-overview.md)
## [Primer](service-fabric-content-roadmap.md)

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
## [Créer votre premier cluster sur Azure](service-fabric-get-started-azure-cluster.md)
## [Créer votre premier cluster autonome](service-fabric-get-started-standalone-cluster.md)

# Procédure
## Générer une application
### [Schémas et scénarios](service-fabric-patterns-and-scenarios.md)
### Concepts de base
#### [Modèle d'application](service-fabric-application-model.md)
#### [Empaqueter une application](service-fabric-package-apps.md)
#### [Modèle de programmation pris en charge](service-fabric-choose-framework.md)
#### [État du service](service-fabric-concepts-state.md)
#### [Modèle de communication de service](service-fabric-connect-and-communicate-with-services.md)
#### [Ajouter un serveur web frontal](service-fabric-add-a-web-frontend.md)
#### [Ressources du manifeste de service](service-fabric-service-manifest-resources.md)
#### [Prise en main du plug-in Eclipse pour le développement Java](service-fabric-get-started-eclipse.md)
#### [Gérer les applications dans Visual Studio](service-fabric-manage-application-in-visual-studio.md)
#### [Configurer des connexions sécurisées dans Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
#### Déboguer
##### [Déboguer un service C# dans Visual Studio](service-fabric-debugging-your-application.md)
##### [Déboguer un service Java dans Eclipse](service-fabric-debugging-your-application-java.md)
#### Surveiller et diagnostiquer
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Gérer des secrets d’application](service-fabric-application-secret-management.md)  
#### [Configurer les stratégies de sécurité de votre application](service-fabric-application-runas-security.md)  
#### [Configurer votre application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md)  
#### [Erreurs et exceptions courantes](service-fabric-errors-and-exceptions.md) 

### Application exécutable invité
#### [Déployer un fichier exécutable invité](service-fabric-deploy-existing-app.md)
#### [Déploiement de plusieurs exécutables invités](service-fabric-deploy-multiple-apps.md)

### Application de conteneur
#### [Vue d'ensemble](service-fabric-containers-overview.md)
#### [Déployer un conteneur de Windows](service-fabric-deploy-container.md)
#### [Déployer un conteneur Docker](service-fabric-deploy-container-linux.md)

### Une application Reliable Service
#### [Vue d'ensemble](service-fabric-reliable-services-introduction.md)
#### Prise en main
##### [C# sur Windows](service-fabric-reliable-services-quick-start.md)
##### [Java sur Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Cycle de vie des Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
#### [Cycle de vie des Reliable Services - Java](service-fabric-reliable-services-lifecycle-java.md)
#### [Collections fiables](service-fabric-reliable-services-reliable-collections.md)
#### [Utiliser Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [Configurer](service-fabric-reliable-services-configuration.md)
#### [Notifications](service-fabric-reliable-services-notifications.md)
#### [Sauvegarde et restauration](service-fabric-reliable-services-backup-restore.md)
#### [Communiquer avec Reliable Services](service-fabric-reliable-services-communication.md)
#### [Communication sécurisée avec Reliable Services - C#](service-fabric-reliable-services-secure-communication.md)
#### [Communication sécurisée avec Reliable Services - Java](service-fabric-reliable-services-secure-communication-java.md)
##### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
##### [Communication à distance du service - C#](service-fabric-reliable-services-communication-remoting.md)
##### [Communication à distance du service - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Proxy inversé](service-fabric-reverseproxy.md)
#### [Utilisateurs avancés](service-fabric-reliable-services-advanced-usage.md)

### Une application Reliable Actor
#### [Vue d'ensemble](service-fabric-reliable-actors-introduction.md)
#### Prise en main
##### [C# sur Windows](service-fabric-reliable-actors-get-started.md)
##### [Java sur Linux](service-fabric-reliable-actors-get-started-java.md)
#### [Architecture](service-fabric-reliable-actors-platform.md)
#### [Cycle de vie et garbage collection](service-fabric-reliable-actors-lifecycle.md)
#### [Polymorphisme](service-fabric-reliable-actors-polymorphism.md)
#### [Réentrance](service-fabric-reliable-actors-reentrancy.md)
#### [Événements](service-fabric-reliable-actors-events.md) 
#### [Minuteries et rappels](service-fabric-reliable-actors-timers-reminders.md)
#### [Gestion des états](service-fabric-reliable-actors-state-management.md)
#### [Configurer KVSActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Sérialisation de type](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
#### [Configurer les paramètres de communication](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [Configurer ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

## Migrer à partir de Services cloud
### [Comparer Services cloud et Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrer vers le Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Pratiques recommandées](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Créer et gérer des clusters

### Concepts de base
#### [Vue d'ensemble](service-fabric-deploy-anywhere.md)
#### [Décrire un cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Planification de la capacité](service-fabric-cluster-capacity.md)
#### [Visualiser un cluster](service-fabric-visualizing-your-cluster.md)
#### [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md)
#### [Gérer un cluster à l’aide de l’interface de ligne de commande Azure](service-fabric-azure-cli.md) 
#### [Sécuriser un cluster](service-fabric-cluster-security.md)
#### [Récupération d’urgence](service-fabric-disaster-recovery.md)
#### [Différences de fonctionnalités entre Linux et Windows](service-fabric-linux-windows-differences.md)

### Clusters sur Azure
#### Créer un cluster sur Azure
##### [Portail Azure](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio et Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### [Modèles de mise en réseau de cluster](service-fabric-patterns-networking.md)
#### [Types de nœud et groupes de machines virtuelles identiques dans le cluster](service-fabric-cluster-nodetypes.md)
#### [Mise à l'échelle d'un cluster](service-fabric-cluster-scale-up-down.md)
#### [Mettre à l’échelle un cluster par programmation](service-fabric-cluster-programmatic-scaling.md)
#### [Mettre à niveau un cluster](service-fabric-cluster-upgrade.md)
#### [Supprimer un cluster](service-fabric-cluster-delete.md)
#### [Contrôle d’accès](service-fabric-cluster-security-roles.md)
#### [Configurer un cluster](service-fabric-cluster-fabric-settings.md)
#### [Ajouter ou substituer des certificats de cluster](service-fabric-cluster-security-update-certs-azure.md) 

### Clusters autonomes
#### [Planifier et préparer votre déploiement](service-fabric-cluster-standalone-deployment-preparation.md)
#### [Contenu du package autonome Service Fabric](service-fabric-cluster-standalone-package-contents.md)
#### [Créer un cluster autonome](service-fabric-cluster-creation-for-windows-server.md)
#### [Création d'un cluster autonome sur des machines virtuelles Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)
#### [Mise à l'échelle d'un cluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Mettre à niveau un cluster](service-fabric-cluster-upgrade-windows-server.md)
#### [Contrôle d’accès](service-fabric-cluster-security-roles.md)
#### [Configurer un cluster](service-fabric-cluster-manifest.md)
#### [Sécuriser un cluster à l’aide de certificats](service-fabric-windows-cluster-x509-security.md)  
#### [Sécuriser un cluster à l’aide de la sécurité de Windows](service-fabric-windows-cluster-windows-security.md) 

## Gérer le cycle de vie de l’application
### [Vue d'ensemble](service-fabric-application-lifecycle.md)
### [Configurer l’intégration continue avec VSTS](service-fabric-set-up-continuous-integration.md)
### [Déployer votre application Linux Java à l’aide de Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
### [Comprendre le paramètre ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Déployer ou supprimer des applications
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
### [Vue d’ensemble de la mise à niveau d’application](service-fabric-application-upgrade.md)
### [Configurer la mise à niveau d’une application](service-fabric-visualstudio-configure-upgrade.md)
### [Paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md)
### Mettre à niveau une application
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Résoudre les problèmes de mises à niveau d’application](service-fabric-application-upgrade-troubleshooting.md)
### [Sérialisation de données lors des mises à niveau de d’application](service-fabric-application-upgrade-data-serialization.md)
### [Rubriques avancées sur la mise à niveau d’application](service-fabric-application-upgrade-advanced.md)

## Intégrité du cluster et de l’application
### [Surveiller l’intégrité du Service Fabric](service-fabric-health-introduction.md)
### [Signaler et contrôler l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Ajouter des rapports de contrôle de l’état](service-fabric-report-health.md)
### [Résoudre les problèmes avec les rapports d’intégrité du système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Afficher des rapports d’intégrité](service-fabric-view-entities-aggregated-health.md)

## Surveiller et diagnostiquer
### [Surveiller et diagnostiquer des applications](service-fabric-diagnostics-overview.md)
### Surveiller et diagnostiquer les services localement
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Journaux de diagnostics Azure
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Collecter les journaux à partir d’un processus de service](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [Diagnostics des services Reliable Services avec état](service-fabric-reliable-services-diagnostics.md)
### [Diagnostics dans Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Résoudre les problèmes de votre cluster local](service-fabric-troubleshoot-local-cluster-setup.md)

## Mettre à l’échelle les applications
### [Partitionner Reliable Services](service-fabric-concepts-partitioning.md)
### [Disponibilité des services](service-fabric-availability-services.md)
### [Mettre à l’échelle les applications](service-fabric-concepts-scalability.md)
### [Planifier la capacité des applications](service-fabric-capacity-planning.md)

## Tester des applications et services
### [Présentation du service d’analyse des erreurs](service-fabric-testability-overview.md)
### [Tester la communication entre les services](service-fabric-testability-scenarios-service-communication.md)
### Simuler des défaillances
#### [Utilisation d’un chaos contrôlé](service-fabric-controlled-chaos.md)
#### [Utilisation d’actions de test](service-fabric-testability-actions.md)
#### [Au cours des charges de travail](service-fabric-testability-workload-tests.md)
#### [Utilisation de scénarios de test](service-fabric-testability-scenarios.md)
#### [Utilisation des API de transition de nœud](service-fabric-node-transition-apis.md)
### [Tester la charge de votre application](service-fabric-vso-load-test.md)

## Gérer et orchestrer les ressources de cluster
### [Présentation de Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
### [Architecture Cluster Resource Manager](service-fabric-cluster-resource-manager-architecture.md)
### [Décrire un cluster](service-fabric-cluster-resource-manager-cluster-description.md)
### [Vue d’ensemble des groupes d’applications](service-fabric-cluster-resource-manager-application-groups.md)
### [Configurer les paramètres du Gestionnaire de ressources de Cluster](service-fabric-cluster-resource-manager-configure-services.md)
### [Mesures de la consommation de ressources](service-fabric-cluster-resource-manager-metrics.md)
### [Utiliser l’affinité de service](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [Stratégies de positionnement de service](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [Gérer un cluster](service-fabric-cluster-resource-manager-management-integration.md)
### [Défragmentation de cluster](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [Équilibrer un cluster](service-fabric-cluster-resource-manager-balancing.md)
### [Limitation](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [Mouvement de service](service-fabric-cluster-resource-manager-movement-cost.md)

# Référence
## [PowerShell](/powershell/module/ServiceFabric/)
## [API Java](/java/api/)
## [.NET](/dotnet/api/)
## [REST](/rest/api/servicefabric)

# les ressources
## [Questions courantes sur Service Fabric](service-fabric-common-questions.md)
## [Options de support de Azure Service Fabric](service-fabric-support.md)
## [Exemple de code](http://aka.ms/servicefabricsamples)
## [Parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Tarification](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=service-fabric)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
