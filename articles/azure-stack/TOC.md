# Vue d'ensemble
## [Qu’est-ce qu’Azure Stack ?](azure-stack-poc.md)
## [Nouveautés](azure-stack-whats-new.md)
## [Principaux concepts et fonctionnalités](azure-stack-key-features.md)
## [Architecture POC](azure-stack-architecture.md)

# Évaluer et déployer
## Prise en main
### [Conditions préalables au déploiement](azure-stack-deploy.md)
### [Déployer](azure-stack-run-powershell-script.md)
### [Activer les portails](azure-stack-run-powershell-script.md#activate-the-administrator-and-tenant-portals)
### [S’inscrire](azure-stack-register.md)
## Procédures
### [Se connecter à Azure Stack POC](azure-stack-connect-azure-stack.md)
### [Ajouter une image par défaut](azure-stack-add-default-image.md)
### [Approvisionner une machine virtuelle](azure-stack-provision-vm.md)
### [Créer un compte de stockage](azure-stack-provision-storage-account.md)
### [Ajouter un locataire Azure Stack](azure-stack-add-new-user-aad.md)
### [Redéployer Azure Stack POC](azure-stack-redeploy.md)

# Gérer
## Vue d'ensemble
### [Gestion des régions](azure-stack-region-management.md)
### [Utilisation des portails](azure-stack-manage-portals.md)
## Prise en main
### [Se connecter à Azure Stack](azure-stack-connect-azure-stack.md)
### Configurer l’environnement de gestion
#### [Installer PowerShell](azure-stack-powershell-install.md)
#### [Télécharger les outils](azure-stack-powershell-download.md)
#### [Configurer PowerShell](azure-stack-powershell-configure.md)
## Procédures
### [Gérer les mises à jour](azure-stack-updates.md)
### [Surveiller l’intégrité et des alertes](azure-stack-monitor-health.md)
### [Gérer les ressources réseau](azure-stack-viewing-public-ip-address-consumption-in-tp2.md)
### [Gérer les ressources de stockage](azure-stack-manage-storage-accounts.md)
### [Gérer les machines virtuelles Windows Azure Pack](azure-stack-manage-windows-azure-pack.md)

# Security & Compliance
## Procédures
### [Gérer RBAC](azure-stack-manage-permissions.md)
### [Ajouter des utilisateurs pour AD FS](azure-stack-add-users-adfs.md)
### [Créer les principaux du service](Azure-stack-create-service-principals.md)

# Proposer des services
## Prise en main
### Créer des plans, des offres et des quotas
#### [Définir les quotas](azure-stack-setting-quotas.md)
#### [Créer un plan](azure-stack-create-plan.md)
#### [Créer une offre](azure-stack-create-offer.md)
#### [S’abonner à une offre](azure-stack-subscribe-plan-provision-vm.md)
#### [Déléguer des offres dans Azure Stack](azure-stack-delegated-provider.md)
## Procédures
### Proposer SQL ou MySQL en tant que PaaS
#### [Déployer un fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)
#### [Déployer un fournisseur de ressources SQL](azure-stack-sql-resource-provider-deploy.md)
### Offrir App Service en tant que PaaS
#### [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md)
#### [Avant de commencer](azure-stack-app-service-before-you-get-started.md)
#### [Déployer le fournisseur de ressources App Service](azure-stack-app-service-deploy.md)
#### [Déployer App Service hors connexion](azure-stack-app-service-deploy-offline.md)
#### [Ajouter de nouveaux rôles de travail web](azure-stack-app-service-add-worker-roles.md)
#### [Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md)
#### [Activer FTP dans App Service sur Azure Stack](azure-stack-app-service-enable-ftp.md)
### Renseigner le Marketplace
#### [Présentation du Marketplace](azure-stack-marketplace.md)
#### [Télécharger des articles du Marketplace](azure-stack-download-azure-marketplace-item.md)
#### [Créer et publier un article du Marketplace](azure-stack-create-and-publish-marketplace-item.md)
#### [Ajouter une image de machine virtuelle personnalisée](azure-stack-add-vm-image.md)
#### [Déployer des machines virtuelles Linux](azure-stack-linux.md)
### Utilisation et facturation
#### [Vue d'ensemble](azure-stack-billing-and-chargeback.md)
#### [Rapports de données d’utilisation](azure-stack-usage-reporting.md)
#### [API d’utilisation du fournisseur](azure-stack-provider-resource-api.md)
#### [API d’utilisation du locataire](azure-stack-tenant-resource-usage-api.md)
#### [Forum Aux Questions sur l’utilisation](azure-stack-usage-related-faq.md)

# Utiliser les services
## Calcul
### [Ajouter une image de machine virtuelle](azure-stack-add-vm-image.md)
### [Utiliser des hôtes Linux](azure-stack-linux.md)
## Storage
### [Vue d'ensemble](azure-stack-storage-overview.md)
### [Différences et considérations](azure-stack-acs-differences-tp2.md)
## Réseau
### [iDNS pour Azure Stack](azure-stack-understanding-dns.md)
### [DNS dans Azure Stack](azure-stack-dns.md)
### [Comprendre les connexions VPN de site à site](azure-stack-create-vpn-connection-one-node-tp2.md)
## Key Vault
### [Vue d'ensemble](azure-stack-kv-intro.md)
### Prise en main
#### [Gérer avec le portail](azure-stack-kv-manage-portal.md)
#### [Gérer avec PowerShell](azure-stack-kv-manage-powershell.md)
### Procédures
#### [Créer une machine virtuelle avec un certificat](azure-stack-kv-push-secret-into-vm.md)
#### [Exemple d’application Key Vault](azure-stack-kv-sample-app.md)

# Créer des applications
## Vue d'ensemble
### [Développer pour Azure Stack](azure-stack-developer.md)
## Prise en main
### [Se connecter à Azure Stack](azure-stack-connect-azure-stack.md)
### Configurer l’environnement de développement
#### [Installer PowerShell](azure-stack-powershell-install.md)
#### [Télécharger les outils](azure-stack-powershell-download.md)
#### [Configurer PowerShell](azure-stack-powershell-configure.md)
#### [Installation de Visual Studio](azure-stack-install-visual-studio.md)
## Procédures
### [Utiliser le module de stratégie](azure-stack-policy-module.md)
### Utiliser des modèles
#### [Présentation des modèles](azure-stack-arm-templates.md)
#### [Développer des modèles](azure-stack-develop-templates.md)
#### [Vérifier des modèles](azure-stack-validate-templates.md)
#### [Déployer avec le portail](azure-stack-deploy-template-portal.md)
#### [Déployer avec PowerShell](azure-stack-deploy-template-powershell.md)
#### [Déployer avec Visual Studio](azure-stack-deploy-template-visual-studio.md)

# Résolution des problèmes
## [Problèmes connus](azure-stack-troubleshooting.md)
## [Diagnostics dans Azure Stack](azure-stack-diagnostics.md)

# Référence
## [Gérer les profils de version des API](azure-stack-version-profiles.md)

# les ressources
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStack)  
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-stack)

