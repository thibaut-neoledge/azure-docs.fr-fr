---
title: "Considérations relatives à la mise en réseau avec un environnement Azure App Service Environment"
description: "Cet article présente le trafic réseau d’un environnement App Service Environment (ASE) et explique comment définir des groupes de sécurité réseau et des itinéraires définis par l’utilisateur (UDR) avec votre ASE."
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: d19590c23da43e08ea42cd278347e01d87433a58
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considérations relatives à la mise en réseau pour un environnement App Service Environment #

## <a name="overview"></a>Vue d'ensemble ##

L’environnement [App Service Environment][Intro] (ASE) constitue un déploiement d’Azure App Service dans un sous-réseau de votre réseau virtuel Azure.  Il existe deux types de déploiement pour l’ASE :

- _ASE externe_ : expose les applications hébergées dans l’ASE sur une adresse IP accessible via Internet. Pour plus d’informations, consultez [Créer un environnement App Service externe][MakeExternalASE].
- _ASE ILB_ : expose les applications hébergées dans l’ASE sur une adresse IP à l’intérieur de votre réseau virtuel Azure.  Le point de terminaison interne est un équilibreur de charge interne (Internal Load Balancer, ou ILB), d’où cette appellation d’ASE ILB. Pour plus d’informations, consultez [Create and use an ILB ASE][MakeILBASE] (Créer et utiliser un ASE ILB).

Il existe aujourd’hui deux versions de l’ASE. La version initiale de l’ASE est appelée ASEv1 et la version plus récente ASEv2. Pour plus d’informations sur ASEv1, consultez la [présentation de l’environnement App Service Environment v1][ASEv1Intro]. Un ASEv1 peut être déployé dans un réseau virtuel classique ou Resource Manager. Un ASEv2 peut uniquement être déployé dans un réseau virtuel Resource Manager.

Tous les appels d’un ASE à destination d’Internet quittent le réseau virtuel via une adresse IP virtuelle assignée à l’ASE. L’adresse IP publique de cette adresse IP virtuelle constitue ensuite l’adresse IP source de tous les appels de l’ASE à destination d’Internet.  Si les applications hébergées dans votre ASE appellent des ressources de votre réseau virtuel ou hébergées dans un VPN, l’adresse IP source sera l’une des adresses IP du sous-réseau utilisé par votre ASE.  Comme l’ASE se trouve à l’intérieur du réseau virtuel, il peut également accéder aux ressources de celui-ci sans configuration supplémentaire. Si le réseau virtuel est connecté à votre réseau local, il a également accès aux ressources qui y sont hébergées sans configuration supplémentaire de l’ASE ou de votre application.

![][1] 

Si vous possédez un ASE externe, l’adresse IP virtuelle publique constitue également le point de terminaison vers lequel les applications de votre ASE seront résolues pour les protocoles HTTP/S et FTP/S, le déploiement web et le débogage à distance.

![][2]

Si vous possédez un ASE ILB, c’est l’adresse IP de l’équilibreur de charge interne qui est le point de terminaison pour les protocoles HTTP/S et FTP/S, le déploiement web et le débogage à distance.

Les ports d’accès normaux pour les applications sont les suivants :

| Utilisation | À partir | À |
|----------|---------|-------------|
|  HTTP/HTTPS  | Configurable par l’utilisateur |  80, 443 |
|  FTP/FTPS    | Configurable par l’utilisateur |  21, 990, 10001-10020 |
|  Débogage distant de Visual Studio  |  Configurable par l’utilisateur |  4016, 4018, 4020, 4022 |

Ces ports s’appliquent aussi bien pour un ASE externe que pour un ASE ILB. Si vous vous trouvez dans un ASE externe, il s’agit des ports que vous atteignez sur l’adresse IP virtuelle publique. Si vous vous trouvez dans un ASE externe, vous atteignez ces ports sur l’équilibreur de charge interne. Il convient de noter que si vous verrouillez le port 443, certaines fonctionnalités exposées dans le portail peuvent être affectées. Pour plus d’informations, consultez la section [Dépendances du portail](#portaldep).

## <a name="ase-dependencies"></a>Dépendances d’un ASE ##

Un ASE présente la dépendance d’accès entrant suivante :

| Utilisation | À partir | À |
|-----|------|----|
| Gestion | Internet | Sous-réseau de l’ASE : 454, 455 |
|  Communications internes de l’ASE | Sous-réseau de l’ASE : tous les ports | Sous-réseau de l’ASE : tous les ports
|  Autoriser le trafic entrant provenant d’Azure Load Balancer | Azure Load Balancer | Quelconque

Le trafic entrant fournit la commande et le contrôle de l’ASE en plus de la surveillance du système. Les adresses IP ne sont pas constantes pour ce trafic. Par conséquent, la configuration de la sécurité réseau doit autoriser l’accès sur les ports 454 et 455 à partir de toutes les adresses IP.

Pour l’accès sortant, un ASE dépend de plusieurs systèmes externes. Ces dépendances système sont définies avec des noms DNS et ne sont pas mappées à un ensemble fixe d’adresses IP. Par conséquent, l’ASE requiert un accès sortant vers toutes les adresses IP sur divers ports à partir de son sous-réseau. Un ASE présente les dépendances d’accès sortant suivantes :

| Utilisation | À partir | À |
|-----|------|----|
| Azure Storage | Sous-réseau de l’ASE | table.core.windows.net, blob.core.windows.net, queue.core.windows.net, file.core.windows.net : 80, 443, 445 (le port 445 est requis uniquement pour ASEv1) |
| Base de données SQL | Sous-réseau de l’ASE | database.windows.net : 1433, 11000-11999, 14000-14999 (pour plus d’informations, consultez [SQL Database V12 port usage](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md) [Ports utilisés par SQL Database V12])|
| Gestion d’Azure | Sous-réseau de l’ASE | management.core.windows.net, management.azure.com : 443 
| Vérification du certificat SSL |  Sous-réseau de l’ASE            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com : 443
| Azure Active Directory        | Sous-réseau de l’ASE            |  Internet : 443
| Gestion d’App Service        | Sous-réseau de l’ASE            |  Internet : 443
| DNS Azure                     | Sous-réseau de l’ASE            | Internet : 53
| Communications internes de l’ASE    | Sous-réseau de l’ASE : tous les ports | Sous-réseau de l’ASE : tous les ports

Si l’ASE n’a plus accès à ces dépendances, il cesse de fonctionner. Si cela dure plus d’un certain temps, l’ASE est alors suspendu.

**DNS client**

Si le réseau virtuel est configuré avec un serveur DNS défini par un client, les charges de travail du locataire l’utiliseront. L’ASE a toujours besoin de communiquer avec le DNS Azure à des fins de gestion. 

Si le réseau virtuel est configuré avec un DNS client de l’autre côté d’un réseau VPN, le serveur DNS doit être accessible à partir du sous-réseau contenant l’ASE.

<a name="portaldep"></a>
## <a name="portal-dependencies"></a>Dépendances du portail ##

Outre les dépendances fonctionnelles que présente un ASE, il existe quelques éléments supplémentaires liés à l’utilisation du portail. Certaines des fonctionnalités du portail Azure dépendent d’un accès direct au _site du Gestionnaire de contrôle des services (SCM)_. Pour chaque application hébergée dans Azure App Service, il existe deux URL. La première URL sert à accéder à votre application. La seconde permet d’accéder au site SCM, également désigné sous le nom de _console Kudu_. Voici quelques-unes des fonctionnalités qui utilisent le site SCM :

-   Tâches web
-   Fonctions
-   Flux de journaux
-   Kudu
-   Extensions
-   Process Explorer
-   Console

Cela pose problème dans le cas d’un ASE ILB, car le site SCM n’est pas accessible via Internet en dehors du réseau virtuel. Par conséquent, les fonctionnalités qui dépendent de l’accès au site SCM sont grisées dans le portail Azure lorsque votre application est hébergée dans un ASE ILB.

La plupart des fonctionnalités qui dépendent du site SCM sont également disponibles dans la console Kudu. Vous pouvez vous y connecter directement au lieu d’utiliser le portail. Si votre application est hébergée dans un ASE ILB, vous devez vous connecter à l’aide de vos informations d’identification de publication. L’URL d’une application hébergée dans un ASE ILB permettant d’accéder au site SCM présente le format suivant : 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Par conséquent, si votre ASE ILB est le nom de domaine *contoso.net* et le nom de votre application est *testapp*, l’application sera accessible via *testapp.contoso.net* et le site SCM associé via *testapp.scm.contoso.net*.

## <a name="ase-ip-addresses"></a>Adresses IP d’un ASE ##

Un ASE présente plusieurs adresses IP qu’il est important de connaître. Il s'agit de :

- Adresse IP publique entrante : utilisée pour le trafic d’application dans un ASE externe et pour le trafic de gestion aussi bien dans un ASE externe que dans un ASE ILB.
- Adresse IP publique sortante : utilisée en tant qu’adresse IP source pour les connexions sortantes de l’ASE quittant le réseau virtuel, qui ne sont pas acheminées via un VPN.
- Adresse IP d’ILB : si vous utilisez un ASE ILB.
- Adresses SSL basées sur IP assignées à des applications : uniquement possibles avec un ASE externe et lorsque le mode SSL basé sur IP est configuré.

Toutes ces adresses IP sont facilement visibles pour un ASEv2 à partir de l’interface utilisateur de l’ASE dans le portail Azure. Si vous possédez un ASE ILB, l’adresse IP de l’ILB est répertoriée.

![][3]

**Adresses IP assignées à des applications**

Avec un ASE externe, vous pouvez assigner des adresses IP à des applications individuelles. Ce n’est pas possible avec un ASE ILB. Pour savoir comment configurer votre application de sorte qu’elle possède sa propre adresse IP, consultez [Lier un certificat SSL existant à des applications web Azure](../../app-service-web/app-service-web-tutorial-custom-ssl.md).

Lorsqu’une application possède sa propre adresse SSL basée sur IP, l’ASE réserve deux ports pour le mappage à cette adresse IP. Un port est destiné au trafic HTTP et l’autre au trafic HTTPS. Ces ports sont répertoriés dans l’interface utilisateur de l’ASE, dans la section des adresses IP. Le trafic doit pouvoir atteindre ces ports à partir de l’adresse IP virtuelle. Sinon, les applications ne seront pas accessibles. C’est un point important à garder à l’esprit lors de la configuration de groupes de sécurité réseau.

## <a name="network-security-groups"></a>Groupes de sécurité réseau ##

Les [groupes de sécurité réseau] [ NSGs] permettent de contrôler l’accès réseau au sein d’un réseau virtuel. Lorsque vous utilisez le portail, il existe une règle de refus implicite au niveau de priorité le plus bas qui fait que tout accès est refusé. Par conséquent, ce que vous créez correspond à vos règles d’autorisation.

Dans un ASE, vous n’avez pas accès aux machines virtuelles utilisées pour héberger l’ASE lui-même. Elles se trouvent dans un abonnement géré par Microsoft. Si vous souhaitez restreindre l’accès aux applications dans l’ASE, vous devez définir les groupes de sécurité réseau sur le sous-réseau de l’ASE. Ce faisant, vous devez prêter une attention particulière aux dépendances de l’ASE. Si vous bloquez certaines dépendances, l’ASE cessera de fonctionner.

Les groupes de sécurité réseau peuvent être configurés à l’aide du portail Azure ou via PowerShell. Par souci de simplicité, seul le portail Azure est illustré ici. Les groupes de sécurité réseau sont créés et gérés en tant que ressources de niveau supérieur dans la section **Mise en réseau** du portail.

En tenant compte des exigences liées au trafic entrant et sortant, les groupes de sécurité réseau doivent se présenter comme dans la capture d’écran ci-dessous. Dans cet exemple, la plage d’adresses du réseau virtuel est _192.168.250.0/16_ et le sous-réseau dans lequel l’ASE se trouve est _192.168.251.128/25_.

Les deux premières exigences liées au trafic entrant pour l’ASE figurent en haut de cet exemple. Elles permettent la gestion de l’ASE et autorisent l’ASE à communiquer avec lui-même. Les autres entrées sont toutes configurables par le locataire et peuvent régir l’accès réseau aux applications hébergées dans l’ASE.   

![][4]

En plus d’une règle par défaut qui autorise les adresses IP du réseau virtuel à communiquer avec le sous-réseau de l’ASE, il existe une règle par défaut qui autorise l’équilibreur de charge, c’est-à-dire l’adresse IP virtuelle, à communiquer avec l’ASE.  Vous pouvez afficher les règles par défaut en cliquant sur *Règles par défaut* en regard de l’icône *Ajouter*. Si vous placez une règle de refus pour toute autre communication après les règles de groupes de sécurité réseau illustrées, vous empêcherez le trafic entre l’adresse IP virtuelle et l’ASE. Si vous souhaitez empêcher le trafic provenant de l’intérieur du réseau virtuel, veillez à ajouter votre propre règle autorisant le trafic entrant avec la source AzureLoadBalancer, la destination Any (Tout) et la plage de ports \*.  Comme la règle de groupe de sécurité réseau est simplement appliquée au sous-réseau de l’ASE, vous n’avez pas besoin de définir une destination spécifique.

Si vous avez assigné une adresse IP à votre application, vous devez également veiller à conserver les ports utilisés avec cette application ouverts. Vous pouvez consulter les ports utilisés dans l’interface utilisateur **App Service Environment** > **Adresses IP**.  

À l’exception du dernier, tous les éléments illustrés dans les règles de trafic sortant ci-dessous sont requis. Ils autorisent l’accès réseau aux dépendances de l’ASE décrites plus haut dans ce document. Si vous bloquez l’un d’entre eux, votre ASE cessera de fonctionner. Le dernier élément de la liste autorise votre ASE à communiquer avec les autres ressources de votre réseau virtuel.

![][5]

Une fois vos groupes de sécurité réseau définis, vous devez les assigner au sous-réseau dans lequel se trouve votre ASE. Si vous ne connaissez pas le réseau virtuel ou le sous-réseau de l’ASE, vous pouvez l’afficher dans le portail de gestion de l’ASE. Pour assigner le groupe de sécurité réseau à votre sous-réseau, accédez à l’interface utilisateur du sous-réseau et sélectionnez le groupe de sécurité réseau.

## <a name="routes"></a>Itinéraires ##

Les itinéraires posent le plus souvent problème lorsque vous configurez votre réseau virtuel avec ExpressRoute. Il existe trois types d’itinéraires dans un réseau virtuel Azure, à savoir :

-   Itinéraires système
-   Itinéraires BGP
-   Itinéraires définis par l’utilisateur (UDR)

Les itinéraires BGP prennent le pas sur les itinéraires système. Les UDR prennent le pas sur les itinéraires BGP. Pour plus d’informations sur les itinéraires dans les réseaux virtuels Azure, consultez [Présentation des itinéraires définis par l’utilisateur][UDRs].

La base de données SQL utilisée par l’ASE pour gérer le système présente une liste verte et exige que les communications proviennent de l’adresse IP virtuelle publique de l’ASE. Si les réponses aux requêtes de gestion entrantes sont envoyées via le circuit ExpressRoute, l’adresse de réponse est différente de celle de destination, ce qui interrompt la communication TCP.

Par conséquent, pour que votre ASE fonctionne lorsque votre réseau virtuel est configuré avec un circuit ExpressRoute, le plus simple consiste à :

-   Configurer ExpressRoute de sorte qu’il annonce _0.0.0.0/0_, ce qui, par défaut, tunnelise de force tout le trafic sortant en local.
-   Créer un UDR et l’appliquer au sous-réseau qui contient l’ASE, avec le préfixe d’adresse _0.0.0.0/0_ et le type de tronçon suivant _Internet_.

Si vous apportez ces deux modifications, le trafic à destination d’Internet provenant du sous-réseau de l’ASE ne sera plus acheminé de force via le circuit ExpressRoute et l’ASE pourra fonctionner. 

> [!IMPORTANT]
> Les itinéraires définis dans un UDR doivent être suffisamment spécifiques pour avoir la priorité sur les itinéraires annoncés par la configuration ExpressRoute. L’exemple précédent utilise la plage d’adresses 0.0.0.0/0 étendue, qui peut potentiellement être remplacée accidentellement par des annonces de routage utilisant des plages d’adresses plus spécifiques.
>

Les ASE ne sont pas pris en charge avec les configurations ExpressRoute qui annoncent de façon croisée des itinéraires à partir du chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée. Les configurations ExpressRoute ayant une homologation publique configurée reçoivent les annonces de routage de Microsoft pour un large ensemble de plages d’adresses IP Microsoft Azure. Si ces plages d’adresses sont publiées de façon croisée sur le chemin d’accès d’homologation privée, il en résulte que tous les paquets réseau sortants du sous-réseau de l’environnement App Service seront acheminés de force vers l’infrastructure réseau sur site d’un client. Ce flux de réseau n’est actuellement pas pris en charge par les environnements App Service. L’une des solutions à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée.

Pour créer un UDR tel que décrit plus haut :

1.  Accédez au portail Azure > **Mise en réseau** > **Tables d’itinéraires**.
2.  Créez une table d’itinéraires dans la même région que votre réseau virtuel.
3.  À partir de l’interface utilisateur de votre table d’itinéraires, sélectionnez **Itinéraires** > **Ajouter**.
4.  Définissez **Type de tronçon suivant** sur **Internet** et **Préfixe de l’adresse** sur _0.0.0.0/0_, puis cliquez sur **Enregistrer**.

Un résultat semblable à celui-ci doit s’afficher :

![][6]

Après avoir créé la table d’itinéraires, accédez au sous-réseau contenant votre ASE. Dans la liste du portail, sélectionnez votre table d’itinéraires. Une fois la modification enregistrée, les groupes de sécurité réseau et les itinéraires devraient apparaître dans les informations de votre sous-réseau.

![][7]

### <a name="deploying-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>Déploiement dans des réseaux virtuels Azure existants intégrés avec ExpressRoute ###

Si vous souhaitez déployer votre ASE dans un réseau virtuel qui est déjà intégré avec ExpressRoute, vous devez préconfigurer le sous-réseau à utiliser pour le déploiement de l’ASE, puis effectuer le déploiement à l’aide d’un modèle Resource Manager. Pour créer un ASE dans un réseau virtuel pour lequel ExpressRoute est déjà configuré :

- Créez un sous-réseau pour héberger l’ASE.

    > [!NOTE]
    > Seul l’ASE peut se trouver dans le sous-réseau. Veillez également à sélectionner un espace d’adressage suffisamment important pour prendre en charge la croissance future, car vous ne pourrez pas le modifier ultérieurement. Une taille de `/25` avec 128 adresses est recommandée.
- Créez des UDR (c’est-à-dire des tables d’itinéraires) en suivant la procédure décrite précédemment et définissez-les sur le sous-réseau.
- Créez l’ASE à l’aide d’un modèle Resource Manager en suivant la procédure décrite ici : [Creating an ASE using an ARM Template][MakeASEfromTemplate] (Création d’un ASE à l’aide d’un modèle ARM).

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

