# Vue d'ensemble
## [Découvrir Service Fabric](service-fabric-overview.md)
## [Comprendre les microservices](service-fabric-overview-microservices.md)
## [Scénarios d’application](service-fabric-application-scenarios.md)
## [Architecture](service-fabric-architecture.md)

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

# Procédure
## Générer une application
### Concepts de base
#### [Modèle de programmation simple](service-fabric-choose-framework.md)
#### [Modèle d'application](service-fabric-application-model.md)
#### [Modèle de communication de service](service-fabric-connect-and-communicate-with-services.md)
#### [outils](service-fabric-manage-application-in-visual-studio.md)
#### [Debug](service-fabric-debugging-your-application.md)
#### Surveiller et diagnostiquer
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Configurer les stratégies de sécurité de votre application](service-fabric-application-runas-security.md)
#### [Configurer votre application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md)

### Une application Reliable Service
#### [Vue d'ensemble](service-fabric-reliable-services-introduction.md)
#### Prise en main
##### [C# sur Windows](service-fabric-reliable-services-quick-start.md)
##### [Java sur Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Architecture](service-fabric-reliable-services-platform-architecture.md)
#### [Collections fiables](service-fabric-reliable-services-reliable-collections.md)
#### [Utiliser Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [Configurer](service-fabric-reliable-services-configuration.md)
#### [Notifications](service-fabric-reliable-services-notifications.md)
#### [Sauvegarde et restauration](service-fabric-reliable-services-backup-restore.md)
#### [Communiquer avec Reliable Services](service-fabric-reliable-services-communication.md)
##### [ASP.NET](service-fabric-reliable-services-communication-webapi.md)
##### [Accès distant au service](service-fabric-reliable-services-communication-remoting.md)
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
#### [Minuteries et rappels](service-fabric-reliable-actors-timers-reminders.md)
#### [Événements](service-fabric-reliable-actors-events.md)
#### [Gestion des états](service-fabric-reliable-actors-state-management.md)
#### [Configurer le fournisseur d’état](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Sérialisation de type](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### Application exécutable invité
#### [Déployer un fichier exécutable invité](service-fabric-deploy-existing-app.md)
#### [Déploiement de plusieurs exécutables invités](service-fabric-deploy-multiple-apps.md)

### Application de conteneur
#### [Vue d'ensemble](service-fabric-containers-overview.md)
#### [Déployer un conteneur de Windows](service-fabric-deploy-container.md)
#### [Déployer un conteneur Docker](service-fabric-deploy-container-linux.md)

## Migrer à partir de Services cloud
### [Comparer Services cloud et Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrer vers le Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

## Créer et gérer des clusters

### Concepts de base
#### [Vue d'ensemble](service-fabric-deploy-anywhere.md)
#### [Décrire un cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Planification de la capacité](service-fabric-cluster-capacity.md)
#### [Visualiser un cluster](service-fabric-visualizing-your-cluster.md)
#### [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md)
#### [Sécurité](service-fabric-cluster-security.md)
#### [Récupération d’urgence](service-fabric-disaster-recovery.md)

### Clusters sur Azure
#### Créer un cluster sur Azure
##### [Portail Azure](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### [Types de nœud et groupes de machines virtuelles identiques dans le cluster](service-fabric-cluster-nodetypes.md)
#### [Mise à l'échelle d'un cluster](service-fabric-cluster-scale-up-down.md)
#### [Mettre à niveau un cluster](service-fabric-cluster-upgrade.md)
#### [Supprimer un cluster](service-fabric-cluster-delete.md)
#### [Contrôle d’accès](service-fabric-cluster-security-roles.md)
#### [Configurer un cluster](service-fabric-cluster-fabric-settings.md)
#### [Essayer gratuitement un Cluster de tiers](http://aka.ms/tryservicefabric)

### Clusters autonomes
#### [Créer un cluster autonome](service-fabric-cluster-creation-for-windows-server.md)
#### [Mise à l'échelle d'un cluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Mettre à niveau un cluster](service-fabric-cluster-upgrade-windows-server.md)
#### [Sécuriser un cluster](service-fabric-windows-cluster-x509-security.md)
#### [Contrôle d’accès](service-fabric-cluster-security-roles.md)
#### [Configurer un cluster](service-fabric-cluster-manifest.md)

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

## Gérer le cycle de vie de l’application
### [Vue d'ensemble](service-fabric-application-lifecycle.md)
### [Configurer l’intégration continue](service-fabric-set-up-continuous-integration.md)
### Déployer ou supprimer des applications
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
### [Vue d’ensemble de la mise à niveau d’application](service-fabric-application-upgrade.md)
### [Configurer la mise à niveau d’une application](service-fabric-visualstudio-configure-upgrade.md)
### [Paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md)
### Mettre à niveau une application
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Résoudre les problèmes de mises à niveau d’application](service-fabric-application-upgrade-troubleshooting.md)
### [Sérialisation de données lors des mises à niveau de d’application](service-fabric-application-upgrade-data-serialization.md)
### [Rubriques avancées sur la mise à niveau d’application](service-fabric-application-upgrade-advanced.md)
### [Échantillon de cycle de vie des applications basé sur REST](service-fabric-rest-based-application-lifecycle-sample.md)

## Intégrité du cluster et de l’application
### [Surveiller l’intégrité du Service Fabric](service-fabric-health-introduction.md)
### [Signaler et contrôler l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Ajouter des rapports de contrôle de l’état](service-fabric-report-health.md)
### [Résoudre les problèmes avec les rapports d’intégrité du système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Afficher des rapports d’intégrité](service-fabric-view-entities-aggregated-health.md)

## Surveiller et diagnostiquer
### Surveiller et diagnostiquer les services localement
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Journaux de diagnostics Azure
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Trace d’applications Service Fabric](service-fabric-diagnostic-how-to-use-elasticsearch.md)
### [Diagnostics dans Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Diagnostics des services Reliable Services avec état](service-fabric-reliable-services-diagnostics.md)
### [Résoudre les problèmes de votre cluster local](service-fabric-troubleshoot-local-cluster-setup.md)
### [Résolution des problèmes courants](service-fabric-diagnostics-troubleshoot-common-scenarios.md)

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
#### [En appelant une perte de données](service-fabric-use-data-loss-api.md)
#### [Utilisation de scénarios de test](service-fabric-testability-scenarios.md)
### [Tester la charge de votre application](service-fabric-vso-load-test.md)

# Référence
## [Terminologie](service-fabric-technical-overview.md)
## [Acteurs fiables (Reliable Actors)](https://go.microsoft.com/fwlink/p/?linkid=833398)
## [Acteurs fiables WCF](https://go.microsoft.com/fwlink/p/?linkid=833401)
## [Services fiables (Reliable Services)](https://go.microsoft.com/fwlink/p/?linkid=833402)
## [Services fiables WCF](https://go.microsoft.com/fwlink/p/?linkid=833403)
## [Données](https://go.microsoft.com/fwlink/p/?linkid=833404)
## [Interfaces de données](https://go.microsoft.com/fwlink/p/?linkid=833406)
## [Système](https://go.microsoft.com/fwlink/p/?linkid=833407)
## [Exemple de code](http://aka.ms/servicefabricsamples)
## [PowerShell](/powershell/servicefabric/vlatest/servicefabric)
## [API Java](https://go.microsoft.com/fwlink/p/?linkid=833410)
## [.NET](/dotnet/api)
## [REST](/rest/api/servicefabric/)

# les ressources
## [Parcours de formation](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Tarification](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=service-fabric)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)


<!--HONumber=Nov16_HO2-->


