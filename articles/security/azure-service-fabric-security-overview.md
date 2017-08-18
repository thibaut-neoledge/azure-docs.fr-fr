---
title: "Vue d’ensemble de la sécurité Azure Service Fabric | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble de la sécurité Azure Service Fabric."
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
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 4cbd2791649c6d2dd005521cedb44c17aa874073
ms.contentlocale: fr-fr
ms.lasthandoff: 08/11/2017

---
# <a name="azure-service-fabric-security-overview"></a>Vue d’ensemble de la sécurité Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) est une plateforme de systèmes distribués qui permet d’empaqueter, de déployer et de gérer facilement des microservices scalables et fiables. Service Fabric résout les problèmes non négligeables du développement et de la gestion des applications cloud. Les développeurs et administrateurs sont en mesure d’éviter les problèmes d’infrastructure complexes et peuvent se concentrer sur l’implémentation de charges de travail stratégiques et exigeantes, évolutives, fiables et faciles à gérer.

Cet article de présentation de la sécurité Azure Service Fabric se concentre sur les points suivants :

-   Sécurisation de votre cluster
-   Surveillance et diagnostics
-   Sécurisation à l’aide de certificats
-   Contrôle d’accès en fonction du rôle
-   Sécurisation du cluster à l’aide de la sécurité de Windows
-   Configuration de la sécurité de l’application dans Service Fabric
-   Sécurisation des communications pour les services dans la sécurité Azure Service Fabric

## <a name="securing-your-cluster"></a>Sécurisation de votre cluster
Azure Service Fabric orchestre les services dans un cluster de machines. Les clusters doivent être sécurisés pour empêcher les utilisateurs non autorisés de se connecter à votre cluster, surtout quand des charges de travail sont en cours d’exécution sur ce dernier. Bien qu’il soit possible de créer un cluster non sécurisé, cela permet à tout utilisateur anonyme de s’y connecter si les points de terminaison de gestion sur l’Internet public.

Cette section fournit une vue d’ensemble des scénarios de sécurité pour les clusters autonomes ou s’exécutant sur Azure, et des différentes technologies utilisées pour implémenter ces scénarios. Les scénarios de sécurité des clusters sont les suivants :

-   Sécurité nœud à nœud
-   Sécurité client à nœud

### <a name="node-to-node-security"></a>Sécurité nœud à nœud
Sécurise la communication entre les machines virtuelles ou les ordinateurs du cluster. Cette sécurité garantit que seuls les ordinateurs qui sont autorisés à rejoindre le cluster peuvent participer à l’hébergement des applications et des services dans le cluster.

Les clusters qui s’exécutent sur Azure ou les clusters autonomes sur Windows peuvent utiliser la [Sécurité par certificat](https://msdn.microsoft.com/library/ff649801.aspx) ou la [Sécurité Windows](https://msdn.microsoft.com/library/ff649396.aspx) pour les ordinateurs Windows Server.

**Sécurité de certificat de nœud à nœud**

Service Fabric utilise des certificats de serveur X.509 que vous spécifiez dans le cadre des configurations du type de nœud, lorsque vous créez un cluster. Un rapide aperçu de ce que sont ces certificats et de la [façon dont vous pouvez les acquérir ou les créer est proposé dans cet article](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

La configuration de la sécurité par certificat s’effectue lors de la création du cluster par le biais du Portail Azure, de modèles Azure Resource Manager ou d’un modèle JSON autonome. Vous pouvez spécifier un certificat primaire et un certificat secondaire facultatif utilisé pour les renouvellements de certificats. Les certificats primaires et secondaires que vous spécifiez doivent être différents de ceux du client d’administration et du client en lecture seule que vous spécifiez pour la [sécurité de client à nœud](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Sécurité client à nœud
La sécurité client à nœud est configurée à l’aide d’identités client. Pour établir une approbation entre un client et le cluster, vous devez configurer le cluster de façon à ce qu’il sache quelles identités client sont fiables. Cette opération peut être réalisée de deux manières :

-   Spécifiez les utilisateurs du groupe de domaines qui peuvent se connecter ou
-   Spécifiez les utilisateurs du nœud de domaine qui peuvent se connecter.

Service Fabric prend en charge deux types de contrôle d’accès différents pour les clients qui sont connectés à un cluster Service Fabric :

-   Administrateur
-   Utilisateur

Le contrôle d'accès permet à l'administrateur du cluster de limiter l'accès à certains types d’opérations de cluster pour différents groupes d'utilisateurs, renforçant ainsi la sécurité du cluster. Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctionnalités de lecture/écriture). Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête) et la capacité à résoudre les applications et les services.

**Sécurité par certificat de client à nœud**

La configuration de la sécurité par certificat de client à nœud s’effectue durant la création du cluster (par le biais du portail Azure, des modèles Resource Manager ou d’un gabarit JSON autonome) en spécifiant un certificat client d’administration et/ou un certificat de client utilisateur. Les certificats client d’administration et client utilisateur que vous spécifiez doivent être différents des certificats primaires et secondaires que vous spécifiez pour la sécurité de nœud à nœud.

Les clients se connectant au cluster avec le certificat d’administration ont un accès complet aux fonctions de gestion. Les clients se connectant au cluster avec le certificat client en lecture seule ont uniquement un accès en lecture aux fonctions de gestion. En d’autres termes, ces certificats sont utilisés pour le contrôle d’accès en fonction du rôle (RBAC).

Pour Azure, consultez [Configurer un cluster à l’aide d’un modèle ARM](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pour découvrir comment configurer la sécurité par certificat dans un cluster.

**Sécurité Azure Active Directory (AAD) de client-à-nœud sur Azure**

Les clusters s’exécutant sur Azure peuvent également sécuriser l’accès aux points de terminaison de gestion à l’aide d’Azure Active Directory (AAD). Pour plus d’informations sur la façon de créer les artefacts AAD nécessaires, de les remplir pendant la création du cluster et de se connecter à ces clusters par la suite, consultez [Configurer un cluster en utilisant un modèle Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) .

AAD permet aux organisations (appelées locataires) de gérer l’accès utilisateur aux applications qui sont réparties entre les applications avec une interface utilisateur de connexion web et les applications avec une expérience client natif.

Un cluster Service Fabric offre différents points d’entrée pour leurs fonctionnalités de gestion, notamment les outils Service Fabric Explorer et Visual Studio. Par conséquent, vous allez créer deux applications AAD pour contrôler l’accès au cluster : une application web et une application native.
Pour les clusters Azure, nous vous recommandons d’utiliser la sécurité AAD pour authentifier les clients et les certificats pour la sécurité de nœud à nœud.

Pour les clusters Windows Server, nous vous recommandons d’utiliser la sécurité Windows avec des comptes gérés par des groupes (GMA) si vous avez Windows Server 2012 R2 et Active Directory. Sinon, continuez à utiliser la sécurité Windows avec les comptes Windows.

## <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Surveillance et diagnostics pour Azure Service Fabric
La [surveillance et les diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) sont essentiels au développement, au test et au déploiement d’applications et de services dans tout environnement. Les solutions Service Fabric conviennent mieux pour planifier et implémenter une surveillance et des diagnostics afin de s’assurer que les applications et services fonctionnent comme prévu dans un environnement de développement local ou de production.

Du point de vue de la sécurité, les principaux objectifs de la surveillance et des diagnostics sont de :

-   Détecter et diagnostiquer les problèmes de matériel et d’infrastructure pouvant être dus à un événement de sécurité.
-   Détecter les problèmes de logiciel et d’applications pouvant indiquer une compromission.
-   Comprendre la consommation des ressources afin d’empêcher les dénis de service faits par inadvertance.

Le flux de travail global de la surveillance et des diagnostics se compose de trois étapes :

-   **Génération des événements** : cela inclut les événements (journaux, traces, événements personnalisés) au niveau de l’infrastructure (cluster) et de l’application/du service. Pour comprendre ce qui est fourni et comment ajouter une instrumentation, vous pouvez poursuivre votre lecture sur les [événements de niveau infrastructure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) et les [événements de niveau application](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app).
-   **Agrégation d’événements** : les événements générés doivent être collectés et agrégés avant de pouvoir être affichés. En général, nous recommandons d’utiliser [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (procédure qui s’apparente davantage à la collecte de journaux basée sur un agent) ou [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (collecte de journaux In-process).
-   **Analyse** : les événements doivent être visualisés et accessibles dans un certain format, pour permettre l’analyse et l’affichage comme souhaité. Plusieurs plateformes formidables sont disponibles sur le marché pour l’analyse et la visualisation des données de surveillance et de diagnostic. Les deux que nous recommandons sont [OMS](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) et [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights), en raison de leur meilleure intégration à Service Fabric.

Vous pouvez également utiliser [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) pour surveiller un grand nombre des ressources Azure sur lesquelles repose un cluster Service Fabric.

Un agent de surveillance est un service distinct qui peut surveiller l’intégrité et la charge des services, et créer des rapports d’intégrité pour n’importe quel élément de la hiérarchie du modèle d’intégrité. Cela permet d’éviter des erreurs qui ne seraient pas détectées à partir de l’affichage d’un service unique. Les agents de surveillance sont également un bon emplacement pour héberger du code qui exécute des actions correctives sans intervention de l’utilisateur (par exemple, le nettoyage de fichiers journaux dans le stockage à intervalles réguliers). Vous trouverez un exemple d’implémentation de service d’agent de surveillance [ici](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-using-certificates"></a>Sécurisation à l’aide de certificats
En utilisant des certificats, vous savez comment sécuriser les communications entre les différents nœuds de votre cluster Windows autonome, et comment authentifier des clients se connectant à ce cluster à l’aide de certificats X.509. Cela garantit que seuls les utilisateurs autorisés puissent accéder au cluster et aux applications déployées, et effectuer des tâches de gestion. La sécurité par certificat doit être activée sur le cluster lors de sa création.

### <a name="x509-certificates-and-service-fabric"></a>Certificats X.509 et Service Fabric
Les certificats numériques X.509 sont couramment utilisés pour authentifier les clients et serveurs et pour chiffrer et signer numériquement les messages.

Le tableau suivant répertorie les certificats dont vous aurez besoin pour la configuration de votre cluster :

|Définition des informations sur le certificat |Description|
|-------------------------------|-----------|
|ClusterCertificate|    Ce certificat est requis pour sécuriser les communications entre les nœuds sur un cluster. Vous pouvez utiliser deux certificats différents : un certificat principal et un certificat secondaire pour la mise à niveau.|
|ServerCertificate| Ce certificat est présenté au client lorsqu’il tente de se connecter à ce cluster. Vous pouvez utiliser deux certificats de serveur différents : un certificat principal et un certificat secondaire pour la mise à niveau.|
|ClientCertificateThumbprints|  Il s’agit du jeu de certificats que vous souhaitez installer sur les clients authentifiés.|
|ClientCertificateCommonNames|  Définissez le nom commun du premier certificat client pour CertificateCommonName. L’élément CertificateIssuerThumbprint est l’empreinte numérique relative à l’émetteur de ce certificat.|
|ReverseProxyCertificate|   Il s’agit d’un certificat facultatif à spécifier si vous souhaitez sécuriser votre [Proxy inverse](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Pour plus d’informations sur la sécurisation à l’aide de certificats, [cliquez ici](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle
Le contrôle d'accès permet à l'administrateur du cluster de limiter l'accès à certaines opérations de cluster pour différents groupes d'utilisateurs, renforçant ainsi la sécurité du cluster. Deux types de contrôle d’accès différents sont pris en charge pour les clients se connectant à un cluster : le rôle Administrateur et le rôle Utilisateur.

Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctionnalités de lecture/écriture). Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête) et la capacité à résoudre les applications et les services.

Vous spécifiez les rôles client utilisateur et administrateur au moment de la création du cluster en fournissant des identités distinctes (certificats, AAD, etc.) pour chacun. Pour plus d’informations sur les paramètres de contrôle d’accès par défaut et sur la modification des paramètres par défaut, consultez [Contrôle d’accès en fonction du rôle pour les clients Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-using-windows-security"></a>Sécuriser un cluster autonome à l’aide de la sécurité de Windows
Pour empêcher tout accès non autorisé à un cluster Service Fabric, vous devez sécuriser le cluster. La sécurité est particulièrement importante lorsque le cluster exécute des charges de travail de production. Cette approche permet de configurer la sécurité de nœud à nœud et de client à nœud avec la sécurité Windows dans le fichier ClusterConfig.JSON.

**Configuration de la sécurité Windows à l’aide de gMSA**

La sécurité de nœud à nœud se configure en définissant [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) quand Service Fabric doit s’exécuter sous le compte gMSA. Pour créer des relations d’approbation entre les nœuds, ceux-ci doivent se connaître mutuellement.

La sécurité client à nœud est configurée à l’aide de ClientIdentities. Pour établir une approbation entre un client et le cluster, vous devez configurer le cluster de façon à ce qu’il sache quelles identités client sont fiables.

**Configuration de la sécurité Windows à l’aide d’un groupe de machines**

La sécurité nœud à nœud se configure en définissant l’utilisation de ClusterIdentity si vous souhaitez utiliser un groupe de machines dans un domaine Active Directory. Pour en savoir plus, consultez la rubrique traitant de la [création d’un groupe de machines dans Active Directory](https://msdn.microsoft.com/library/aa545347).

La sécurité de client à nœud est configurée avec ClientIdentities. Pour établir une approbation entre un client et le cluster, vous devez configurer le cluster de façon à ce qu’il connaisse les identités du client auxquelles il peut faire confiance. Vous pouvez établir cette approbation de deux manières différentes :

-   Spécifiez les utilisateurs du groupe de domaines qui peuvent se connecter.
-   Spécifiez les utilisateurs du nœud de domaine qui peuvent se connecter.

## <a name="configure-application-security-in-service-fabric"></a>Configuration de la sécurité de l’application dans Service Fabric
### <a name="managing-secrets-in-service-fabric-applications"></a>Gestion des secrets dans des applications Service Fabric
Cette méthode permet de gérer les secrets dans une application Service Fabric. Les secrets peuvent être des informations sensibles quelconques, notamment des chaînes de connexion de stockage, des mots de passe ou d’autres valeurs qui ne doivent pas être traitées en texte brut.

Cette approche utilise [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) pour gérer les clés et les secrets. Toutefois, l’utilisation de secrets dans une application cloud est indépendante de la plateforme et permet ainsi un déploiement d’applications dans un cluster hébergé à n’importe quel endroit. Ce flux comprend quatre étapes principales :

-   Obtenez un certificat de chiffrement de données.
-   Installez le certificat dans votre cluster.
-   Chiffrez les valeurs secrètes lors du déploiement d’une application avec le certificat et injectez-les dans le fichier de configuration Settings.xml d’un service.
-   Lisez les valeurs chiffrées dans Settings.xml en les déchiffrant avec le même certificat de chiffrement.

>[!Note]
>Découvrez-en plus sur la [gestion des secrets dans des applications Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Configurer les stratégies de sécurité de votre application
Grâce à la sécurité Azure Service Fabric, vous pouvez sécuriser les applications en cours d’exécution dans le cluster sous différents comptes utilisateurs. La sécurité Service Fabric permet également de sécuriser les ressources utilisées par des applications au moment du déploiement sous les comptes utilisateurs, par exemple les fichiers, les annuaires et les certificats. Ainsi, les applications en cours d’exécution sont plus sécurisées, même dans un environnement hébergé partagé.
Procédez comme suit :

-   Configurer la stratégie pour un point d’entrée de configuration de service.
-   Lancer des commandes PowerShell à partir d’un point d’entrée de configuration.
-   Utiliser la console de redirection pour le débogage local.
-   Configurer une stratégie pour les packages de code de service.
-   Assigner une stratégie d’accès de sécurité pour des points de terminaison HTTP et HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Sécuriser les communications pour les services dans la sécurité Azure Service Fabric
La sécurité est un des aspects les plus importants de la communication. Le framework d’application Reliable Services fournit quelques piles et outils de communication prédéfinis que vous pouvez utiliser pour améliorer la sécurité.

-   [Sécuriser un service quand vous utilisez la communication à distance des services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).
-   [Sécuriser un service quand vous utilisez une pile de communication basée sur WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack).

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir des informations conceptuelles sur la sécurité des clusters, consultez [Créer un cluster dans Azure à l’aide d’un modèle Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) et [Portail Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Pour en savoir plus, consultez [Sécurité du cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

