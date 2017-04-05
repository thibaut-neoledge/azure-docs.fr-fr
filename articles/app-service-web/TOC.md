# Vue d'ensemble    
## [À propos de Web Apps](app-service-web-overview.md)
## [Comparer les options d’hébergement](choose-web-site-cloud-service-vm.md)

# Démarrage rapide    
## [Créer un site HTML statique](app-service-web-get-started-html.md)
## [Créer une application ASP.NET](app-service-web-get-started-dotnet.md)        
## [Créer une application PHP](app-service-web-get-started-php.md) 
## [Créer une application Node.js](app-service-web-get-started-nodejs.md) 
## [Créer une application Java](app-service-web-get-started-java.md)        
## [Créer une application Python](app-service-web-get-started-python.md)    

# Exemples
## [Interface de ligne de commande Azure](app-service-cli-samples.md) 
## [PowerShell](app-service-powershell-samples.md) 

# Didacticiels

# Concepts
## [Fonctionnement d’App Service](../app-service/app-service-how-works-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [Plans App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [environnements App Service](app-service-app-service-environment-intro.md)
## [Authentification et autorisation](../app-service/app-service-authentication-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
## [Authentification avec AD local](web-sites-authentication-authorization.md)


# Procédures
## Développer votre application    
### ASP.NET
#### [Application ASP.NET avec SQL Database](web-sites-dotnet-get-started.md)
#### [Développer une application ASP.NET Core avec VS Code](web-sites-create-web-app-using-vscode.md)
### PHP
#### [Application Laravel déployée avec MySQL](app-service-web-php-get-started.md)
#### [Configurer votre projet PHP](web-sites-php-configure.md)
#### [Configurer votre multisite WordPress](web-sites-php-convert-wordpress-multisite.md) 
### Node.js
#### [Application Sails.js avec une base de données NOSQL](app-service-web-nodejs-sails.md)
#### [Utiliser io.js](web-sites-nodejs-iojs.md)
#### [Déboguer une application Node.js](web-sites-nodejs-debug.md)
### Java
#### [Application Java avec Eclipse](app-service-web-eclipse-create-hello-world-web-app.md)
#### [Application Java avec IntelliJ](app-service-web-intellij-create-hello-world-web-app.md)
#### [Utiliser le kit de développement logiciel (SDK) Azure pour Java](java-create-azure-website-using-java-sdk.md)
#### [Charger une application existante](web-sites-java-add-app.md)
#### [Débogage distant d’Eclipse](app-service-web-debug-java-web-app-in-eclipse.md)
#### [Débogage distant d’IntelliJ](app-service-web-debug-java-web-app-in-intellij.md)
### Python
#### [Application Django avec MySQL](web-sites-python-ptvs-django-mysql.md)

### [Envoyer des courriers électroniques avec SendGrid](sendgrid-dotnet-how-to-send-email.md)

### Configurer le runtime    
#### [PHP sur Windows](web-sites-php-configure.md)

#### [Java](web-sites-java-add-app.md)
#### [Node.js sur Linux](app-service-linux-using-nodejs-pm2.md)
#### [Python](web-sites-python-configure.md)
            
### Configurer une application
#### [Utiliser les paramètres de l’application](web-sites-configure.md)
            
## [Déployer dans Azure](web-sites-deploy.md)
### [Déployer via FTP](app-service-deploy-content-sync.md)
### [Déployer via la synchronisation avec le cloud](web-sites-deploy.md)
### [Déployer en continu](app-service-continuous-deployment.md)
### [Déployer vers un environnement intermédiaire](web-sites-staged-publishing.md)
### [Déployer à partir de Git local](app-service-deploy-local-git.md)
### [Déployer avec un modèle](app-service-deploy-complex-application-predictably.md)
### [Déploiement agile](app-service-agile-software-development.md)
### [Tests bêta](app-service-web-test-in-production-controlled-test-flight.md)

### [Définir les informations d’identification de déploiement](app-service-deployment-credentials.md)

### Mapper un domaine personnalisé
#### [Acheter un domaine](custom-dns-web-site-buydomains-web-app.md)
#### [Mapper un domaine tiers](web-sites-custom-domain-name.md)
#### [Mapper des domaines avec Traffic Manager](web-sites-traffic-manager-custom-domain-name.md)
#### [Mapper des domaines GoDaddy](web-sites-godaddy-custom-domain-name.md)
#### [Migrer un domaine actif](app-service-custom-domain-name-migrate.md)

### [Migrer à partir d’IIS](web-sites-migration-from-iis-server.md)
### [Tester en production](app-service-web-test-in-production-get-start.md)
### [Ajouter des fonctionnalités à une application web](app-service-web-get-started-2.md)

## Se connecter à une base de données/des ressources        

### [Se connecter à des données locales](web-sites-hybrid-connection-get-started.md) 
### [Se connecter à un réseau virtuel Azure](web-sites-integrate-with-vnet.md)
### [Se connecter à un réseau virtuel Azure avec PowerShell](app-service-vnet-integration-powershell.md)
### [Se connecter à MongoDB sur une machine virtuelle Azure](web-sites-dotnet-store-data-mongodb-vm.md)
            
##Sécuriser une application
### Authentification des utilisateurs        
#### [S’authentifier avec Azure AD](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [S’authentifier avec Facebook](../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [S’authentifier avec Google](../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [S’authentifier avec un compte Microsoft](../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [S’authentifier avec Twitter](../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [S’authentifier avec AD local](web-sites-authentication-authorization.md)
#### [Application avec une base de données mutualisée](web-sites-dotnet-entity-framework-row-level-security.md)
### Attribuer un certificat SSL personnalisé
#### [Acheter un certificat SSL](web-sites-purchase-ssl-web-site.md)
#### [Configurer un certificat SSL tiers](web-sites-configure-ssl-certificate.md)
### [Appliquer le protocole HTTPS](web-sites-configure-ssl-certificate.md#enforce-https-on-your-app)
### [Configurer l’authentification mutuelle TLS](app-service-web-configure-tls-mutual-auth.md)
            
##Mettre à l’échelle une application        
### [Monter en puissance](web-sites-scale.md)
### [Augmenter la taille des instances](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Équilibrer la charge avec Traffic Manager](web-sites-traffic-manager.md)
### [Mettre à grande échelle avec les environnements App Service](../app-service/app-service-app-service-environments-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Utiliser Azure CDN pour la portée générale](cdn-websites-with-cdn.md)
### [Se connecter au cache Redis via Memcache](web-sites-connect-to-redis-using-memcache-protocol.md)
### [Créer un cache Redis](../redis-cache/cache-redis-cache-arm-provision.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Gérer l’état de session avec le cache Azure Redis](web-sites-dotnet-session-state-caching.md)

## Surveiller 
### [Surveiller les applications](web-sites-monitor.md)
### [Activer les journaux](web-sites-enable-diagnostic-log.md)
### [Diffuser les journaux en continu](web-sites-streaming-logs-and-console.md)

## Sauvegarder le contenu        
### [Sauvegarder votre application](web-sites-backup.md)
### [Restaurer votre application à partir d’une sauvegarde](web-sites-restore.md)
### [Sauvegarder avec l’API REST](websites-csm-backup.md)

## Gérer les ressources d’application
### [Cloner une application avec PowerShell](app-service-web-app-cloning.md)
### [Cloner une application via le portail](app-service-web-app-cloning-portal.md)
### [Déplacer des ressources](app-service-move-resources.md)
### [Utiliser Azure Resource Manager avec PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
### [Gérer des applications à l’aide d’Azure Automation](automation-manage-web-app.md)



# Référence    
## [CLI 2.0](/cli/azure/appservice)
## [PowerShell](/powershell)
## [API REST](/rest/api/appservice/) 
        
# les ressources    
## Résolution des problèmes        
### [Résoudre les problèmes à l’aide de Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
### [Résoudre les problèmes des applications Node.js](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
### [Résoudre les problèmes des protocoles HTTP 502 et 503](app-service-web-troubleshoot-http-502-http-503.md)
### [Résoudre les problèmes de performances](app-service-web-troubleshoot-performance-degradation.md)
## [Tarification](https://azure.microsoft.com/pricing/details/app-service/)     
## [Informations sur les quotas](../azure-subscription-service-limits.md#app-service-limits)    
## [Mises à jour de service et notes de publication](https://azure.microsoft.com/updates/?product=app-service)    
## [meilleures pratiques](app-service-best-practices.md)
## [Exemples](https://azure.microsoft.com/resources/samples/?service=app-service)    
## [Vidéos](https://azure.microsoft.com/resources/videos/index/?services=app-service)
## Livres de recettes    
### [Architectures de référence](../guidance/guidance-ra-app-service.md)    
### [Scripts de déploiement](https://azure.microsoft.com/documentation/scripts/)    
