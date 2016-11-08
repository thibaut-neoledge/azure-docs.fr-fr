


## Avantages de l’intégration du calcul, du réseau et du stockage dans le modèle de déploiement Azure Resource Manager
Le modèle de déploiement Azure Resource Manager permet de facilement tirer parti des modèles d’application prédéfinis ou de construire un modèle d’application pour déployer et gérer des ressources de calcul, de réseau et de stockage sur Azure. Dans cette section, nous découvrirons les avantages du déploiement de ressources via le modèle de déploiement Azure Resource Manager.

* La complexité en toute simplicité : créez, intégrez et collaborez sur des applications complexes qui peuvent reprendre toute la gamme des ressources Azure (Sites Web, Bases de données SQL, machines virtuelles ou réseaux virtuels) à partir d’un fichier de modèle partageable
* Flexibilité des déploiements renouvelables pour le développement, les opérations de développement et les administrateurs système lorsque vous utilisez le même fichier de modèle
* Intégration en profondeur des extensions de machine virtuelle (scripts personnalisés, DSC, Chef, Puppet, etc.) avec le Gestionnaire de ressources Azure dans un fichier de modèle permettant une orchestration simple de la configuration de l’installation sur les machines virtuelles
* Définition des balises et propagation de ces balises pour les ressources de calcul, de réseau et de stockage
* Gestion simple et précise de l’accès aux ressources organisationnelles avec l’aide du contrôle d’accès en fonction du rôle Azure (RBAC)
* Mise à niveau ou mise à jour simplifiée en modifiant le modèle d’origine, puis en procédant à un redéploiement

## Améliorations des API de calcul, de réseau et de stockage avec Azure Resource Manager
Outre les avantages mentionnés ci-dessus, bien d’autres améliorations de performances significatives se retrouvent dans les API publiées :

* Déploiement massif de machines virtuelles en parallèle
* Prise en charge de 3 domaines d’erreur dans les groupes à haute disponibilité
* Extension de Script personnalisé améliorée qui permet de spécifier des scripts à partir de n’importe quelle URL personnalisée accessible publiquement
* Intégration des machines virtuelles avec le coffre de clés Azure pour un stockage hautement sécurisé et le déploiement privé de contenu secret de [Modules de sécurité matériels](http://wikipedia.org/wiki/Hardware_security_module) [validés FIPS](http://wikipedia.org/wiki/FIPS_140-2)
* Fournit les bases de la mise en réseau via des API, pour permettre aux clients de concevoir des applications complexes qui comprennent des interfaces réseau, des éléments d’équilibrage de la charge et des réseaux virtuels
* Les interfaces réseau, en tant que nouvel objet, permettent de réutiliser une configuration réseau complexe pour les machines virtuelles
* L’équilibrage de la charge en tant que ressource de première classe permet l’attribution d’adresses IP
* Les API de réseau virtuel granulaires permettent de simplifier la gestion des différents réseaux virtuels

## Différences conceptuelles avec l’introduction des nouvelles API
Dans cette section, nous allons découvrir certaines des principales différences entre les API XML disponibles actuellement et les API JSON disponibles via le Gestionnaire de ressources Azure pour le calcul, le réseau et le stockage.

| Élément | Azure Service Management (XML) : | Fournisseurs de calcul, réseau et stockage (JSON) |
| --- | --- | --- |
| Service cloud pour machines virtuelles |Le service cloud était un conteneur pour les machines virtuelles nécessitant la disponibilité de la plateforme et l’équilibrage de charge. |Le service cloud n’est plus un objet nécessaire à la création des machines virtuelles avec le nouveau modèle. |
| Groupes à haute disponibilité |La disponibilité de la plateforme était indiquée en configurant le même « AvailabilitySetName » sur les machines virtuelles. Le nombre maximal de domaines d’erreur était de 2. |Le groupe à haute disponibilité est une ressource exposée par le fournisseur Microsoft.Compute. Les machines virtuelles qui nécessitent une haute disponibilité doivent faire partie du groupe à haute disponibilité. Le nombre maximal de domaines d’erreur est maintenant de 3. |
| Groupe d'affinités |Les groupes d’affinités étaient nécessaires pour créer des réseaux virtuels. Toutefois, avec l’introduction des réseaux virtuels régionaux, ils ne sont plus nécessaires. |Pour simplifier, le concept de groupe d’affinités n’existe pas dans les API exposées par le Gestionnaire de ressources Azure. |
| Équilibrage de la charge. |La création d’un service cloud fournit un équilibrage de la charge implicite pour les machines virtuelles déployées. |L’équilibrage de la charge est une ressource exposée par le fournisseur Microsoft.Network. La principale interface réseau des machines virtuelles dont la charge doit être équilibrée doit faire référence à l’équilibrage de la charge. Ces éléments d’équilibrage de la charge peuvent être internes ou externes. [En savoir plus.](../articles/virtual-network/resource-groups-networking.md) |
| Adresse IP virtuelle |Les services cloud obtiennent une adresse IP virtuelle par défaut lorsqu’une machine virtuelle est ajoutée à un service cloud. L’adresse IP virtuelle est l’adresse associée à l’équilibrage de charge implicite. |L’adresse IP est une ressource exposée par le fournisseur Microsoft.Network. L’adresse IP publique peut être statique (réservée) ou dynamique. Les adresses IP publiques dynamiques peuvent être attribuées à un équilibrage de charge. Les adresses IP publiques peuvent être sécurisées à l’aide de groupes de sécurité. |
| Adresses IP réservées |Vous pouvez réserver une adresse IP dans Azure et l’associer à un service cloud pour vous assurer que l’adresse IP est permanente. |L’adresse IP publique peut être créée en mode « Statique ». Elle offre les mêmes fonctionnalités qu’une « Adresse IP réservée ». Les adresses IP publiques statiques peuvent être attribuées dès maintenant à un équilibrage de la charge. |
| Adresse IP publique par machine virtuelle |Les adresses IP publiques peuvent également être associées directement à une machine virtuelle. |L’adresse IP est une ressource exposée par le fournisseur Microsoft.Network. L’adresse IP publique peut être statique (réservée) ou dynamique. Toutefois, seules les adresses IP dynamiques publiques peuvent être affectées à une interface réseau pour obtenir une adresse IP publique par machine virtuelle. |
| Points de terminaison |Les points de terminaison d’entrée doivent être configurés sur la machine virtuelle pour pouvoir ouvrir la connectivité pour certains ports. Un des modes les plus communs de connexion aux machines virtuelles se fait en définissant des points de terminaison d’entrée. |Les règles NAT entrantes peuvent être configurées sur l’équilibrage de la charge pour obtenir les mêmes fonctionnalités d’activation des points de terminaison sur certains ports spécifiques pour se connecter aux machines virtuelles. |
| Nom DNS |Les services cloud obtiennent un nom DNS global unique et implicite. Par exemple : `mycoffeeshop.cloudapp.net`. |Les noms DNS sont des paramètres facultatifs qui peuvent être spécifiés sur une ressource d’adresse IP publique. Le nom de domaine complet se présente au format suivant - `<domainlabel>.<region>.cloudapp.azure.com`. |
| Interfaces réseau |L’interface réseau principale et secondaire et ses propriétés ont été définies en tant que configuration du réseau d’une machine virtuelle. |L’interface réseau est une ressource exposée par le fournisseur Microsoft.Network. Le cycle de vie de l’interface réseau n’est pas lié à une machine virtuelle. |

## Mise en route des modèles Azure pour les machines virtuelles
Vous pouvez débuter dans l’utilisation des modèles Azure en exploitant les différents outils de développement et de déploiement de la plateforme.

### Portail Azure
Le portail Azure continuera à bénéficier de l'option de déploiement de machines virtuelles avec le modèle de déploiement classique et de déploiement de machines virtuelles avec le modèle de déploiement Resource Manager simultanément. Le portail Azure permettra aussi le déploiement de modèles personnalisés.

### Azure PowerShell
Azure PowerShell aura deux modes de déploiement : le mode **AzureServiceManagement** et le mode **AzureResourceManager**. Le mode AzureResourceManager contiendra désormais également les applets de commande nécessaires à la gestion des machines virtuelles, des réseaux virtuels et des comptes de stockage. Pour plus d'informations à ce sujet, cliquez [ici](../articles/powershell-azure-resource-manager.md).

### Interface de ligne de commande Azure
L’interface de ligne de commande Azure (Azure CLI) aura deux modes de déploiement : le mode **AzureServiceManagement** et le mode **AzureResourceManager**. Le mode AzureResourceManager contiendra désormais également les commandes nécessaires à la gestion des machines virtuelles, des réseaux virtuels et des comptes de stockage. Pour plus d'informations à ce sujet, cliquez [ici](../articles/xplat-cli-azure-resource-manager.md).

### Visual Studio
Avec la dernière version du Kit de développement logiciel (SDK) Microsoft Azure pour Visual Studio, vous pouvez créer et déployer des machines virtuelles et des applications complexes à partir de Visual Studio. Visual Studio permet d’effectuer le déploiement à partir d’une liste de modèles prédéfinie ou à partir d’un modèle vide.

### API REST
Vous trouverez la documentation API REST détaillée pour les fournisseurs de ressources de calcul, de réseau et de stockage [ici](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Forum Aux Questions (FAQ)
**Puis-je créer une machine virtuelle à l’aide du nouveau Gestionnaire de ressources Azure et la déployer dans un réseau virtuel ou un compte de stockage créé à l’aide des API de gestion des services Azure ?**

Cette opération n’est pas possible pour le moment. Vous ne pouvez pas effectuer le déploiement à l’aide des nouvelles API Gestionnaire de ressources Azure pour déployer une machine virtuelle dans un réseau virtuel créé à l’aide des API de gestion des services Azure.

**Puis-je créer une machine virtuelle à l’aide des nouvelles API Gestionnaire de ressources Azure à partir d’une image utilisateur créée à l’aide des API de gestion des services Azure ?**

Cette opération n’est pas possible pour le moment. Toutefois, vous pouvez copier les fichiers VHD (de disque dur virtuel) à partir d’un compte de stockage créé à l’aide des API de gestion des services et les coller dans un nouveau compte créé à l’aide des nouvelles API Gestionnaire de ressources Azure.

**Quel est l’impact sur le quota pour mon abonnement ?**

Les quotas pour les machines virtuelles, les réseaux virtuels et les comptes de stockage créés via les nouvelles API Gestionnaire de ressources Azure sont distincts des quotas dont vous disposez. Chaque abonnement obtient de nouveaux quotas pour créer les ressources avec les nouvelles API. Plus d’informations sur les quotas supplémentaires sont disponibles [ici](../articles/azure-subscription-service-limits.md).

**Puis-je continuer à utiliser mes scripts automatisés pour approvisionner des machines virtuelles, des réseaux virtuels, des comptes de stockage, etc. avec les nouvelles API Gestionnaire de ressources Azure ?**

Toutes les automatisations et tous les scripts que vous avez créés continueront à fonctionner pour les machines virtuelles et les réseaux virtuels existants créés en mode Gestion des services Azure. Cependant, les scripts doivent être mis à jour afin qu’ils utilisent le nouveau schéma pour créer les mêmes ressources via le nouveau mode Gestionnaire de ressources Azure.

**Où puis-je trouver des exemples de modèles Azure Resource Manager ?**

Vous trouverez un ensemble complet de modèles de démarrage sur [Modèles de démarrage rapide Azure Resource Manager](https://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_0629_2016-->