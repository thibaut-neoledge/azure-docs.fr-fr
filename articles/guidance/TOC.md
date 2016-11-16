# Architecture

## Principes de base du cloud

### [Conception d’applications résilientes pour Azure](guidance-resiliency-overview.md)
#### [Liste de contrôle de résilience](guidance-resiliency-checklist.md)
#### [Analyse du mode d’échec](guidance-resiliency-failure-mode-analysis.md)

#### [Récupération d’urgence des applications développées sur Microsoft Azure](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Récupération d’urgence et haute disponibilité des applications développées sur Microsoft Azure](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Haute disponibilité des applications développées sur Microsoft Azure](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Liste de contrôle de haute disponibilité](..\resiliency\resiliency-high-availability-checklist.md)
#### [Conseils sur la résilience des services spécifiques Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Récupération suite à une corruption de données ou à une suppression accidentelle](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Récupération des défaillances en local](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Récupération d’une interruption de service à l’échelle de la région](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Récupération de l’environnement local vers Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Guide technique de la résilience Azure](..\resiliency\resiliency-technical-guidance.md)


## Architectures de référence

### Architecture de référence de calcul
#### [Exécution d’une machine virtuelle Linux sur Azure](guidance-compute-single-vm-linux.md)
#### [Exécution d’une machine virtuelle Windows sur Azure](guidance-compute-single-vm.md)
#### [Exécution de plusieurs machines virtuelles sur Azure pour l’évolutivité et la disponibilité](guidance-compute-multi-vm.md)
#### [Exécution de machines virtuelles Linux pour une architecture à plusieurs niveaux dans Azure](guidance-compute-n-tier-vm-linux.md)
#### [Exécution de machines virtuelles Windows pour une architecture à plusieurs niveaux dans Azure](guidance-compute-n-tier-vm.md)
#### [Exécution des machines virtuelles Linux dans plusieurs régions pour la haute disponibilité](guidance-compute-multiple-datacenters-linux.md)
#### [Exécution des machines virtuelles Windows dans plusieurs régions pour la haute disponibilité](guidance-compute-multiple-datacenters.md)

### [Connexion de votre réseau local à Azure](guidance-connecting-your-on-premises-network-to-azure.md)
#### [Implémentation d’une architecture réseau hybride avec Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Implémentation d’une architecture réseau hybride avec Azure et un VPN local](guidance-hybrid-network-vpn.md)
#### [Implémentation d’une architecture réseau hybride hautement disponible](guidance-hybrid-network-expressroute-vpn-failover.md)

### Protection de la limite du cloud dans Azure
#### [Implémentation d’une architecture réseau hybride sécurisée dans Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Implémentation d’une zone DMZ entre Azure et Internet](guidance-iaas-ra-secure-vnet-dmz.md)

### [Gestion des identités dans Azure](guidance-ra-identity.md)
#### [Implémentation d’Azure Active Directory](guidance-identity-aad.md)
#### [Extension des services Active Directory Directory Services (ADDS) pour Azure](guidance-identity-adds-extend-domain.md)
#### [Création d’une forêt de ressources Active Directory Directory Services (ADDS) dans Azure](guidance-identity-adds-resource-forest.md)
#### [Implémentation des services Active Directory Federation Services (ADFS) dans Azure](guidance-identity-adfs.md)

### Architecture de référence d’application web PaaS
#### [Architecture de référence Azure : application web de base](guidance-web-apps-basic.md)
#### [Architecture de référence Azure : application Web à haute disponibilité](guidance-web-apps-multi-region.md)
#### [Amélioration de l’évolutivité dans une application web](guidance-web-apps-scalability.md)


## Modèles de conception de cloud

## Meilleures pratiques pour les applications cloud

### [Guide de conception d’API](..\best-practices-api-design.md)
### [Recommandations relatives à l’implémentation de l’API](..\best-practices-api-implementation.md)
### [Recommandations en matière de mise à l’échelle automatique](..\best-practices-auto-scaling.md)
### [Liste de contrôle de disponibilité](..\best-practices-availability-checklist.md)
### [Conseils sur les travaux en arrière-plan](..\best-practices-background-jobs.md)
### [Continuité des activités et récupération d’urgence (BCDR) : régions jumelées d’Azure](..\best-practices-availability-paired-regions.md)
### [Conseils sur la mise en cache](..\best-practices-caching.md)
### [Aide relative au réseau de distribution de contenu (CDN)](..\best-practices-cdn.md)
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
#### [Exécution d’Elasticsearch sur Azure](guidance-elasticsearch-running-on-azure.md)
#### [Adaptation des performances d’ingestion de données pour Elasticsearch dans Azure](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Adaptation des performances de requête et d’agrégation des données avec Elasticsearch sur Azure](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Configuration de la résilience et de la récupération d’Elasticsearch sur Azure](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Création d’un environnement de test de performances Elasticsearch sur Azure](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implémentation d’un plan de test JMeter pour Elasticsearch](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Déploiement d’un échantillonneur JUnit JMeter pour tester les performances d’Elasticsearch](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Exécution de tests de résilience Elasticsearch automatisés](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Exécution de tests de performances Elasticsearch automatisés](guidance-elasticsearch-running-automated-performance-tests.md)

### [Gestion des identités pour les applications multi-locataires dans Microsoft Azure](guidance-multitenant-identity.md)
#### [Introduction à la gestion des identités pour les applications multi-locataires dans Microsoft Azure](guidance-multitenant-identity-intro.md)
#### [À propos de l’application Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Authentification dans des applications multi-locataires avec Azure AD et OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Utilisation d’identités basées sur les revendications dans les applications multi-locataires](guidance-multitenant-identity-claims.md)
#### [Inscription et intégration de locataire dans une application multi-locataire](guidance-multitenant-identity-signup.md)
#### [Rôles d’application dans les applications multi-locataires](guidance-multitenant-identity-app-roles.md)
#### [Autorisation basée sur les ressources et les rôles dans les applications multi-locataires](guidance-multitenant-identity-authorize.md)
#### [Sécurisation d’une API web de serveur principal dans une application multi-locataire](guidance-multitenant-identity-web-api.md)
#### [Mise en cache de jetons d’accès dans une application multi-locataire](guidance-multitenant-identity-token-cache.md)
#### [Fédération avec les services AD FS d’un client pour les applications multilocataires dans Azure](guidance-multitenant-identity-adfs.md)
#### [Utilisation d’une assertion du client pour obtenir des jetons d’accès d’Azure AD](guidance-multitenant-identity-client-assertion.md)
#### [Utilisation d’Azure Key Vault pour protéger la confidentialité des secrets d’application](guidance-multitenant-identity-keyvault.md)

#### [Déploiement d’appliances virtuelles dans un environnement à haute disponibilité](guidance-nva-ha.md)


<!--HONumber=Nov16_HO2-->


