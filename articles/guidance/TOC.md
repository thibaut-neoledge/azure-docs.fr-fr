# Architecture

## Principes de base du cloud

### [Concevoir des applications résilientes](guidance-resiliency-overview.md)
#### [Liste de contrôle de résilience](guidance-resiliency-checklist.md)
#### [Analyse du mode d’échec](guidance-resiliency-failure-mode-analysis.md)
#### [Récupération d’urgence](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Récupération d’urgence et haute disponibilité](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Haute disponibilité](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Liste de contrôle de haute disponibilité](..\resiliency\resiliency-high-availability-checklist.md)
#### [Conseils sur la résilience des services spécifiques Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Récupération suite à une corruption de données ou à une suppression accidentelle](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Récupération des défaillances en local](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Récupération d’une interruption de service à l’échelle de la région](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Récupération de l’environnement local vers Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Guide technique de la résilience Azure](..\resiliency\resiliency-technical-guidance.md)


## Architectures de référence

### [Exécuter des charges de travail de machine virtuelle dans Azure](guidance-ra-compute.md)
#### [Exécuter une machine virtuelle Linux dans Azure](guidance-compute-single-vm-linux.md)
#### [Exécuter une machine virtuelle Windows dans Azure](guidance-compute-single-vm.md)
#### [Exécuter plusieurs machines virtuelles dans Azure à des fins d’évolutivité et de disponibilité](guidance-compute-multi-vm.md)
#### [Exécuter des machines virtuelles Linux pour une architecture à plusieurs niveaux](guidance-compute-n-tier-vm-linux.md)
#### [Exécuter des machines virtuelles Windows pour une architecture à plusieurs niveaux](guidance-compute-n-tier-vm.md)
#### [Exécuter des machines virtuelles Linux dans plusieurs régions à des fins de haute disponibilité](guidance-compute-multiple-datacenters-linux.md)
#### [Exécuter des machines virtuelles Windows dans plusieurs régions à des fins de haute disponibilité](guidance-compute-multiple-datacenters.md)

### [Connecter votre réseau local à Azure](guidance-ra-hybrid-networking.md)
#### [Architecture réseau hybride avec Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Architecture réseau hybride avec Azure et un VPN local](guidance-hybrid-network-vpn.md)
#### [Architecture réseau hybride hautement disponible](guidance-hybrid-network-expressroute-vpn-failover.md)

### [Protéger la limite du cloud dans Azure](guidance-ra-network-security.md)
#### [Architecture réseau hybride sécurisée dans Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Zone DMZ entre Azure et Internet](guidance-iaas-ra-secure-vnet-dmz.md)

### [Gérer les identités dans Azure](guidance-ra-identity.md)
#### [Implémenter Azure Active Directory](guidance-identity-aad.md)
#### [Étendre ADDS à Azure](guidance-identity-adds-extend-domain.md)
#### [Créer une forêt de ressources ADDS dans Azure](guidance-identity-adds-resource-forest.md)
#### [Implémenter ADFS dans Azure](guidance-identity-adfs.md)

### [Architectures d’application web pour Azure App Service](guidance-ra-app-service.md)
#### [Application web de base](guidance-web-apps-basic.md)
#### [Application web avec haute disponibilité](guidance-web-apps-multi-region.md)
#### [Améliorer l’évolutivité dans une application web](guidance-web-apps-scalability.md)


## Meilleures pratiques pour les applications cloud

### [Guide de conception d’API](..\best-practices-api-design.md)
### [Recommandations relatives à l’implémentation de l’API](..\best-practices-api-implementation.md)
### [Recommandations en matière de mise à l’échelle automatique](..\best-practices-auto-scaling.md)
### [Liste de contrôle de disponibilité](..\best-practices-availability-checklist.md)
### [Conseils sur les travaux en arrière-plan](..\best-practices-background-jobs.md)
### [Régions jumelées Azure](..\best-practices-availability-paired-regions.md)
### [Conseils sur la mise en cache](..\best-practices-caching.md)
### [Aide relative au réseau de distribution de contenu](..\best-practices-cdn.md)
### [Conseils sur le partitionnement des données](..\best-practices-data-partitioning.md)
### [Guide de surveillance et de diagnostic](..\best-practices-monitoring.md)
### [Services cloud et sécurité réseau Microsoft](..\best-practices-network-security.md)
### [Schémas de conception des modèles Azure Resource Manager](..\best-practices-resource-manager-design-templates.md)
### [Conventions d’affectation de noms recommandées pour les ressources Azure](guidance-naming-conventions.md)
### [Questions de sécurité relatives à Azure Resource Manager](..\best-practices-resource-manager-security.md)
### [Partage d’état dans les modèles Azure Resource Manager](..\best-practices-resource-manager-state.md)
### [Conseils généraux sur les nouvelles tentatives](..\best-practices-retry-general.md)
### [Guide spécifique relatif au service de nouvelle tentative](..\best-practices-retry-service-specific.md)
### [Liste de contrôle d’évolutivité](..\best-practices-scalability-checklist.md)


## Guides de scénario

### [Conseils Elasticsearch sur Azure](guidance-elasticsearch.md)
#### [Exécuter Elasticsearch sur Azure](guidance-elasticsearch-running-on-azure.md)
#### [Régler les performances d’ingestion de données](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Régler les performances de requête et d’agrégation de données](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Configurer la résilience et la récupération](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Créer un environnement de test de performances](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implémenter un plan de test JMeter](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Déployer un échantillonneur JUnit JMeter](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Exécuter les tests de résilience automatisés](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Exécuter les tests de performance automatisés](guidance-elasticsearch-running-automated-performance-tests.md)

### [Gestion des identités pour les applications multi-locataires](guidance-multitenant-identity.md)
#### [Vue d'ensemble](guidance-multitenant-identity-intro.md)
#### [Application Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Authentification avec Azure AD et OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Identités basées sur les revendications](guidance-multitenant-identity-claims.md)
#### [Inscription et intégration du client](guidance-multitenant-identity-signup.md)
#### [Rôles d’application](guidance-multitenant-identity-app-roles.md)
#### [Autorisation basée sur les ressources et les rôles](guidance-multitenant-identity-authorize.md)
#### [Sécuriser une API web principale](guidance-multitenant-identity-web-api.md)
#### [Mettre en cache des jetons d’accès](guidance-multitenant-identity-token-cache.md)
#### [Fédération avec les services AD FS d’un client](guidance-multitenant-identity-adfs.md)
#### [Utiliser une assertion du client pour obtenir des jetons d’accès](guidance-multitenant-identity-client-assertion.md)
#### [Utiliser Azure Key Vault pour protéger la confidentialité des secrets d’application](guidance-multitenant-identity-keyvault.md)
#### [Déploiement d’appliances virtuelles dans un environnement à haute disponibilité](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->


