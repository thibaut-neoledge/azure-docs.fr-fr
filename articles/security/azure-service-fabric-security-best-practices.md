---
title: "Bonnes pratiques pour la sécurité Azure Service Fabric | Microsoft Docs"
description: "Cet article fournit un ensemble de bonnes pratiques pour la sécurité Azure Service Fabric."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3132e42aac72c0132c7526ac56d80bc5eec269e7
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-best-practices"></a>Bonnes pratiques pour la sécurité Azure Service Fabric
Le déploiement d’une application sur Azure est rapide, simple et rentable. Avant de déployer une application cloud en production, il peut être judicieux d’évaluer votre application par rapport à une liste de bonnes pratiques essentielles et recommandées.

Azure Service Fabric est une plateforme de systèmes distribués qui permet d’empaqueter, de déployer et de gérer facilement des microservices scalables et fiables. Service Fabric résout également les problèmes non négligeables du développement et de la gestion des applications cloud. Les développeurs et administrateurs sont en mesure d’éviter les problèmes d’infrastructure complexes et peuvent se concentrer sur l’implémentation de charges de travail stratégiques et exigeantes, évolutives, fiables et faciles à gérer. 

Pour chaque bonne pratique, nous détaillons les éléments suivants :

-   Nature de la bonne pratique
-   Raison pour laquelle activer cette bonne pratique
-   Conséquence possible en cas de non-utilisation de la bonne pratique
-   Comment apprendre à utiliser la bonne pratique

Nous disposons des bonnes pratiques suivantes pour la sécurité Azure Service Fabric :

-   Utiliser un modèle Azure Resource Manager (ARM) et le module Azure PowerShell Service Fabric pour créer le cluster sécurisé
-   Utiliser des certificats X.509
-   Configurer des stratégies de sécurité
-   Configurer la sécurité de Reliable Actors
-   Configurer SSL pour Azure Service Fabric
-   Sécurité/isolement réseau avec Azure Service Fabric
-   Configurer un coffre de clés pour la sécurité
-   Affecter des utilisateurs aux rôles


## <a name="best-practices-for-securing-your-cluster"></a>Bonnes pratiques pour sécuriser votre cluster

**Grandes lignes**

Utiliser toujours un cluster sécurisé
-   Sécurité du cluster : utiliser des certificats
-   Accès client (admin et en lecture seule) : utiliser AAD

Utiliser des déploiements automatisés
-   Utiliser des scripts pour générer, déployer et substituer les secrets
-   Conserver les secrets dans un coffre de clés, utiliser AD pour tous les autres accès client
-   Personne ne doit y avoir accès sans authentification.

En outre, tenez compte des points suivants :
-   Créer des zones DMZ à l’aide des groupes de sécurité réseau (NSG)
-   Utiliser des serveurs de rebond (« jump ») pour accéder au moyen du protocole RDP aux machines virtuelles du cluster ou pour gérer votre cluster

Les clusters doivent être sécurisés pour empêcher les utilisateurs non autorisés de se connecter à votre cluster, surtout quand des charges de travail sont en cours d’exécution sur ce dernier. Bien qu’il soit possible de créer un cluster non sécurisé, cela permet à tout utilisateur anonyme de s’y connecter si les points de terminaison de gestion sur l’Internet public.

Technologies utilisées pour implémenter ces scénarios. Les [scénarios de sécurité des clusters](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) sont les suivants :

-   Sécurité nœud à nœud : sécurise les communications entre les machines virtuelles et les ordinateurs du cluster. Cette sécurité garantit que seuls les ordinateurs qui sont autorisés à rejoindre le cluster peuvent participer à l’hébergement des applications et des services dans le cluster.
Les clusters qui s’exécutent sur Azure ou les clusters autonomes sur Windows peuvent utiliser la [Sécurité par certificat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) ou la [Sécurité Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security) pour les ordinateurs Windows Server.
-   Sécurité client à nœud : sécurise la communication entre un client Service Fabric et des nœuds individuels du cluster.
-   Contrôle d’accès en fonction du rôle (RBAC) : vous spécifiez les rôles client utilisateur et administrateur au moment de la création du cluster en fournissant des identités distinctes (certificats, AAD, etc.) pour chacun.
-   Recommandations pour sécurité : pour les clusters Azure, nous vous recommandons d’utiliser la sécurité AAD pour authentifier les clients et les certificats pour la sécurité nœud à nœud.

Pour configurer le cluster Windows autonome, consultez [Paramètres de configuration pour un cluster Windows autonome](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Utilisez des modèles Azure Resource Manager et le module Azure PowerShell Service Fabric pour créer le cluster sécurisé.
Un guide vous montrant pas à pas comment configurer un cluster Azure Service Fabric sécurisé dans Azure à l’aide d’Azure Resource Manager est disponible [ici](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Utiliser le modèle Azure Resource Manager pour personnaliser votre cluster
-   Stockage des VHD de machine virtuelle géré par le programme d’installation

Utiliser le modèle Azure Resource Manager pour piloter les changements dans votre groupe de ressources
-   Gestion des configurations aisée
-   Audit

Traiter la configuration du cluster comme du code
-   Vérifiez minutieusement les configurations que vous choisissez de déployer
-   Évitez d’utiliser des commandes implicites pour optimiser vos ressources directement

Plusieurs aspects du [cycle de vie des applications Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) peuvent être automatisés. Le [module Azure PowerShell Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatise les tâches courantes de déploiement, de mise à niveau, de suppression et de test des applications Service Fabric. Des API gérées et HTTP pour la gestion de l’application sont également disponibles.

## <a name="use-x509-certificates"></a>Utiliser des certificats X.509
Les clusters doivent toujours être sécurisés à l’aide de certificats X.509 ou de la sécurité Windows. La sécurité peut uniquement être configurée au moment de la création du cluster et il n’est pas possible d’activer la sécurité une fois le cluster créé.

Si vous spécifiez un [certificat de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), affectez à ClusterCredentialType la valeur X509. Si vous spécifiez un certificat de serveur pour des connexions externes, donnez à ServerCredentialType la valeur X509.

-   Les certificats utilisés dans les clusters qui exécutent des charges de travail de production doivent être créés en utilisant un service de certificats Windows Server correctement configuré ou obtenus auprès d’une autorité de certification approuvée.
-   En production, n’utilisez jamais de certificats temporaires ou de test créés avec des outils comme MakeCert.exe.
-   Vous pouvez utiliser un certificat auto-signé, mais seulement pour les clusters de test et non en production.

Si le cluster n’est pas sécurisé. N’importe qui peut se connecter anonymement et effectuer des opérations de gestion. Les clusters de production doivent donc toujours être sécurisés à l’aide de certificats X.509 ou via la sécurité Windows.

Pour plus d’informations sur la façon d’activer des certificats dans le cluster Service Fabric, consultez [Ajouter ou supprimer des certificats pour un cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Configurer des stratégies de sécurité
Service Fabric permet également de sécuriser les ressources utilisées par des applications au moment du déploiement sous les comptes utilisateurs, par exemple les fichiers, les annuaires et les certificats. Ainsi, les applications en cours d’exécution sont plus sécurisées, même dans un environnement hébergé partagé.

-   Utiliser un utilisateur ou un groupe de domaine Active Directory : vous pouvez exécuter le service sous les informations d’identification d’un compte d’utilisateur ou de groupe Active Directory. Il s’agit d’Active Directory en local au sein de votre domaine et non avec Azure Active Directory (Azure AD). En utilisant un groupe ou un utilisateur de domaine, vous pouvez accéder à d’autres ressources dans le domaine (par exemple, les partages de fichiers) qui disposent d’autorisations.

-   Assigner une stratégie d’accès de sécurité pour des points de terminaison HTTP et HTTPS : si vous appliquez une stratégie RunAs à un service et que le manifeste de service déclare des ressources de point de terminaison avec le protocole HTTP, vous devez spécifier une stratégie SecurityAccessPolicy pour vous assurer que les ports affectés à ces points de terminaison sont bien au format ACL pour le compte utilisateur RunAs sous lequel le service est exécuté. Sinon, http.sys n’a pas accès au service et les appels en provenance du client échouent.
Pour en savoir plus sur l’activation des stratégies de sécurité dans Service Fabric, consultez [Configurer les stratégies de sécurité de votre application](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="reliable-actors-security-configuration"></a>Configurer la sécurité de Reliable Actors
Service Fabric Reliable Actors est une implémentation du modèle de conception d’acteurs. Comme pour n’importe quel modèle de conception de logiciels, le choix de l’utilisation d’un modèle spécifique dépend de son degré d’adéquation avec le problème de conception du logiciel.

En règle générale, vous pouvez envisager d’utiliser le modèle d’acteur pour modéliser votre problème ou votre scénario si :
-   Votre espace de problème implique un nombre élevé (plusieurs milliers) de petites unités d’état et de logique indépendantes et isolées.
-   Vous souhaitez travailler avec des objets monothread ne nécessitant aucune interaction significative avec des composants externes, par exemple l’interrogation de l’état sur un ensemble d’acteurs.
-   Vos instances d’acteurs ne bloquent pas les appelants avec des retards inattendus en exécutant des opérations d’E/S.

Dans Service Fabric, les acteurs sont implémentés dans l’infrastructure Reliable Actors, une infrastructure d’application basée sur un modèle d’acteurs et s’appuyant sur [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Chaque service Reliable Actor que vous écrivez correspond en fait à une instance Reliable Service partitionnée avec état.
Chaque acteur se définit comme une instance d’un type d’acteur, de la même façon qu’un objet .NET est une instance d’un type .NET. Par exemple, un type d'acteur peut implémenter les fonctionnalités d'une calculatrice, et plusieurs acteurs de ce type peuvent être distribués sur différents nœuds d'un cluster. Chaque acteur de ce type est identifié de façon unique par un ID d'acteur.

Les [configurations de sécurité du réplicateur](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) sont utilisées pour sécuriser le canal de communication utilisé durant la réplication. Un service ne peut donc pas afficher le trafic de réplication d’un autre service, ce qui garantit la sécurité des données rendues hautement disponibles. Par défaut, une section de configuration de sécurité vide empêche de sécuriser la réplication.
Les configurations de réplicateur servent à configurer le réplicateur responsable de la haute fiabilité de l’état du fournisseur d’état d’acteur.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configurer SSL pour Azure Service Fabric

Authentification du serveur : [authentifie](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) les points de terminaison de gestion du cluster sur un client de gestion, afin que le client de gestion sache qu’il communique avec le véritable cluster. Ce certificat fournit également un certificat [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) pour l’API de gestion HTTPS et Service Fabric Explorer par le biais de HTTPS.
Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Pour configurer le chiffrement SSL pour une application, vous devez d’abord obtenir un certificat SSL signé par une autorité de certification, un tiers approuvé qui émet des certificats à cet effet. Si vous n’en possédez pas, vous devez en obtenir un auprès de la société qui vend des certificats SSL.

Le certificat SSL doit répondre aux prérequis suivants dans Azure :
-   Le certificat doit contenir une clé privée.
-   Le certificat doit être créé pour l'échange de clés et pouvoir faire l'objet d'un export au format Personal Information Exchange (.pfx).
-   Le nom d'objet du certificat doit correspondre au domaine servant à accéder au service cloud. Vous ne pouvez pas obtenir de certificat SSL d'une autorité de certification pour le domaine cloudapp.net. Vous devez acquérir un nom de domaine personnalisé à utiliser pour accéder à votre service. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom d’objet du certificat doit correspondre au nom de domaine personnalisé que vous utilisez pour accéder à votre application. Par exemple, si votre nom de domaine personnalisé est contoso.com, vous demandez un certificat auprès de votre autorité de certification pour **.contoso.com** ou **www.contoso.com**.
-   Le certificat doit utiliser au minimum un chiffrement à 2048 bits.

HTTP n’est pas sécurisé et est soumis à des attaques d’écoute clandestine, car les données transférées à partir du navigateur web sur le serveur web ou entre les autres points de terminaison sont transmises en texte en clair. Cela signifie que des attaquants peuvent intercepter et afficher des données sensibles, telles que les détails d’une carte de crédit et des informations de connexion de compte. Quand les données sont envoyées ou publiées par le biais d’un navigateur via HTTPS, SSL garantit que ces informations sont chiffrées et protégées contre une interception.

Pour plus d’informations, consultez [Configuration de SSL pour une application dans Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="network-isolationsecurity-with-azure-service-fabric"></a>Sécurité/isolement réseau avec Azure Service Fabric
Utilisez un [modèle Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) comme exemple pour définir un cluster sécurisé à trois types de nœud et pour contrôler le trafic réseau entrant et sortant à l’aide de groupes de sécurité réseau.

Le modèle a un groupe de sécurité réseau pour chacun des groupes de machines virtuelles identiques afin de contrôler le trafic vers et depuis chacun d’eux. Par défaut, les règles sont configurées pour autoriser tout le trafic requis par les services système et les ports d’application spécifiés dans le modèle. Passez en revue ces règles et apportez les modifications nécessaires, y compris en ajoutant de nouvelles règles pour vos applications.

Pour plus d’informations, consultez [Azure Service Fabric – scénarios de mise en réseau courants](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-a-key-vault-for-security"></a>Configurer un coffre de clés pour la sécurité
Les certificats sont utilisés dans Service Fabric à des fins d’authentification et de chiffrement pour sécuriser les divers aspects d’un cluster et de ses applications.

Service Fabric utilise des certificats X.509 pour sécuriser un cluster et fournir des fonctionnalités de sécurité d’applications. Key Vault sert à [gérer des certificats](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) pour des clusters Service Fabric dans Azure. Lorsqu’un cluster est déployé dans Azure, le fournisseur de ressources Azure chargé de la création des clusters Service Fabric extrait les certificats de Key Vault et les installe sur les machines virtuelles du cluster.

La relation entre [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), un cluster Service Fabric et le fournisseur de ressources Azure utilise des certificats stockés dans un coffre de clés quand il crée un cluster.

**Créer un groupe de ressources** La première étape consiste à créer un groupe de ressources dédié à votre coffre de clés. Nous vous recommandons de placer ce coffre de clés dans son propre groupe de ressources. Vous pouvez ainsi supprimer les groupes de ressources de calcul et de stockage, y compris le groupe de ressources contenant votre cluster Service Fabric, sans risquer de perdre vos clés et secrets. Le groupe de ressources qui contient votre coffre de clés doit se trouver dans la même région que le cluster qui l’utilise.

**Créer un coffre de clés dans le nouveau groupe de ressources** Le coffre de clés doit être activé pour le déploiement afin d’autoriser le fournisseur de ressources de calcul à obtenir des certificats à partir de ce coffre de clés et à les installer sur les instances de machines virtuelles.
Pour plus d’informations sur la configuration du coffre de clés Azure, consultez [Bien démarrer avec Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-roles"></a>Assigner des rôles aux utilisateurs
Une fois que vous avez créé les applications pour représenter votre cluster, affectez les utilisateurs aux rôles pris en charge par Service Fabric : lecture seule et administrateur. Vous pouvez assigner ces rôles à l’aide du portail Azure Classic.

>[!Note]
> Pour plus d’informations sur les rôles dans Service Fabric, consultez [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric prend en charge deux types de contrôle d’accès différents pour les clients qui sont connectés à un [cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) : administrateur et utilisateur. Le contrôle d'accès permet à l'administrateur du cluster de limiter l'accès à certaines opérations de cluster pour différents groupes d'utilisateurs, renforçant ainsi la sécurité du cluster.

## <a name="next-steps"></a>Étapes suivantes
- Configuration de votre [environnement de développement](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) Service Fabric.
- Découvrez les [options de support de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).


