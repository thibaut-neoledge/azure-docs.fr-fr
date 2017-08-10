---
title: "Création et utilisation d’un équilibreur de charge interne avec un environnement App Service | Microsoft Docs"
description: "Création et utilisation d’un ASE avec un ILB"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 3b15d6645b988f69f1f05b27aff6f726f34786fc
ms.openlocfilehash: 9e5a40f18eb9eaf60579af21afc6f05c2d87f4c1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Utilisation d’un équilibreur de charge interne avec un environnement App Service

> [!NOTE] 
> Cet article traite de l’environnement App Service Environment v1. Il existe une version plus récente de l’environnement App Service, plus facile à utiliser et qui s’exécute sur des infrastructures plus puissantes. Pour en savoir plus sur la nouvelle version, commencez par la [Présentation de l’environnement App Service Environment](../app-service/app-service-environment/intro.md).
>

La fonctionnalité ASE (App Service Environment) est une option de service Premium d’Azure App Service offrant une fonction de configuration améliorée qui n’est pas disponible dans les clusters mutualisés. La fonctionnalité ASE déploie essentiellement Azure App Service sur votre réseau virtuel (VNet) Azure. Pour mieux comprendre les possibilités offertes par les environnements App Service, lisez la documentation [Qu'est-ce qu'un environnement App Service ?][WhatisASE]. Si vous ne connaissez pas les avantages de l’utilisation d’un réseau virtuel, consultez la [FAQ sur le réseau virtuel Azure][virtualnetwork]. 

## <a name="overview"></a>Vue d'ensemble
Un environnement App Service peut être déployé avec un point de terminaison accessible via Internet ou avec une adresse IP sur votre réseau virtuel. Pour définir l’adresse IP sur une adresse de réseau virtuel, vous devez déployer votre ASE avec un équilibreur de charge interne (ILB). Lorsque votre ASE est configuré avec un équilibrage de charge interne, vous fournissez :

* votre propre domaine ou sous-domaine. Pour simplifier, ce document suppose que vous utilisez un sous-domaine, mais vous pouvez choisir le scénario qui vous convient. 
* le certificat utilisé pour le protocole HTTPS
* Gestion du service DNS pour votre sous-domaine. 

En retour, vous pouvez effectuer des tâches telles que :

* héberger des applications intranet, comme des applications métier, en toute sécurité dans le cloud auquel vous accédez via un VPN Site à Site ou ExpressRoute
* héberger des applications dans le cloud qui ne figurent pas dans les serveurs DNS publics
* créer des applications de serveur principal internet isolées auxquelles vos applications frontales peuvent s’intégrer en toute sécurité

#### <a name="disabled-functionality"></a>Fonctionnalités désactivées
Lorsque vous utilisez un ASE ILB, certaines opérations ne sont pas autorisées. Ces opérations sont :

* utilisation du protocole IPSSL
* attribution d’adresses IP à des applications spécifiques
* achat et utilisation d’un certificat avec une application via le portail. Vous pouvez bien entendu toujours obtenir des certificats directement auprès d’une autorité de certification et l’utiliser avec vos applications, mais pas via le portail Azure.

## <a name="creating-an-ilb-ase"></a>Création d’un environnement App Service d’équilibrage de charge interne (ILB ASE)
La création d’un ILB ASE n’est pas très différente de la création d’un ASE normalement. Pour plus d’informations sur la création d’un ASE, consultez [Comment créer un environnement App Service][HowtoCreateASE]. Le processus de création d’un environnement ASE LIB est le même que la création d’un réseau virtuel lors de la création de l’ASE ou de la sélection d’un réseau virtuel existant. Pour créer un ILB ASE : 

1. Dans le portail Azure, sélectionnez **Nouveau -> Web + Mobile -> App Service Environment**
2. Sélectionnez votre abonnement
3. Sélectionnez ou créez un groupe de ressources
4. Sélectionnez ou créez un réseau virtuel
5. Créez un sous-réseau si vous sélectionnez un réseau virtuel
6. Sélectionnez **Réseau virtuel/Emplacement -> Configuration du réseau virtuel** et définissez le type d’adresse VIP sur Interne
7. Indiquez le nom du sous-domaine (il s’agit du sous-domaine utilisé pour les applications créées dans cet ASE)
8. Sélectionnez Ok puis Créer

![][1]

Le panneau Réseau virtuel contient une option Configuration du réseau virtuel. Elle vous permet de sélectionner une adresse VIP externe ou interne. La valeur par défaut est Externe. Si vous avez défini la valeur sur Externe, votre ASE utilisera une adresse VIP accessible via Internet. Si vous sélectionnez Interne, votre ASE sera configuré avec un équilibrage de charge sur une adresse IP au sein de votre réseau virtuel interne. 

Après avoir sélectionné la valeur Interne, vous ne pouvez plus ajouter d’adresses IP à votre ASE et vous devez alors spécifier le sous-domaine de l’ASE. Dans un ASE avec une adresse VIP externe, le nom de l’ASE est utilisé dans le sous-domaine pour les applications créées dans cet ASE. Si votre ASE s’appelait ***contosotest*** et que votre application dans cet ASE s’appelait ***mytest***, le sous-domaine serait au format ***contosotest.p.azurewebsites.net*** et l’URL de cette application serait ***mytest.contosotest.p.azurewebsites.net***. Si vous définissez le type d’adresse VIP sur Interne, le nom de votre ASE n’est pas utilisé dans le sous-domaine pour cet ASE. Vous spécifiez explicitement le sous-domaine. Si votre sous-domaine était ***contoso.corp.net*** et que vous avez créé une application dans cet ASE nommé ***timereporting***, l’URL de cette application serait ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Applications d’un ILB ASE
La création d’une application dans un ILB ASE est identique à la création d’une application dans un ASE standard. 

1. Dans le portail Azure, sélectionnez **Nouveau -> Web + Mobile -> Web** ou **Mobile** or **API App**
2. Entrez le nom de l’application
3. Sélectionnez un abonnement
4. Sélectionnez ou créez un groupe de ressources
5. Sélectionnez ou créez un plan App Service (ASP). Si vous créez un nouvel ASP, sélectionnez votre ASE comme emplacement puis choisissez le pool de travail dans lequel vous souhaitez créer votre ASP. Lorsque vous créez l’ASP, vous sélectionnez votre ASE comme emplacement et le pool de travail. Lorsque vous spécifiez le nom de l’application, vous verrez que le sous-domaine sous le nom de votre application est remplacé par le sous-domaine de votre ASE. 
6. Sélectionnez Créer. Vous devriez cocher la case **Épingler au tableau de bord** si vous souhaitez que l’application s’affiche sur votre tableau de bord. 

![][2]

Sous le nom de l’application, le nom du sous-domaine est mis à jour pour refléter le sous-domaine de votre ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Validation après la création de l’ILB ASE
Un ILB ASE est légèrement différent d’un ASE non-ILB. Comme indiqué précédemment, vous devez gérer votre propre DNS et fournir votre propre certificat pour les connexions HTTPS. 

Après avoir créé votre ASE, vous remarquerez que le sous-domaine affiche le sous-domaine que vous avez spécifié, et un nouvel élément apparaît dans le **Paramètre**, appelé **Certificat ILB**. L’ASE est créé avec un certificat auto-signé qui facilite le test de HTTPS. Le portail vous indiquera que vous devez fournir votre propre certificat pour le protocole HTTPS, mais c’est pour vous inciter à avoir un certificat qui accompagne votre sous-domaine. 

![][3]

Si vous effectuez simplement des tests et ignorez comment créer un certificat, vous pouvez utiliser l’application console MMC IIS pour créer un certificat auto-signé. Une fois le certificat créé, vous pouvez l’exporter sous forme de fichier .pfx puis le télécharger dans l’interface utilisateur du certificat ILB. Lorsque vous accédez à un site sécurisé avec un certificat auto-signé, votre navigateur vous avertit que le site auquel vous accédez n’est pas sécurisé en raison de l’impossibilité de valider le certificat. Pour éviter cet avertissement, vous devez utiliser un certificat dûment signé correspondant à votre sous-domaine et contenant une chaîne de confiance reconnue par votre navigateur.

![][6]

Si vous souhaitez essayer le flux avec vos propres certificats et tester l’accès HTTP et HTTPS à votre ASE :

1. Accédez à l’interface utilisateur ASE après la création de l’ASE **ASE -> Paramètres -> Certificats ILB**
2. Définissez le certificat ILB en sélectionnant le fichier .pfx du certificat et fournissez un mot de passe. Cette étape prend un certain temps et un message indiquant qu’une opération de mise à l’échelle est en cours apparaît.
3. Obtenez l’adresse ILB pour votre ASE (**ASE -> Propriétés -> Adresse IP virtuelle**)
4. Création d’une application web dans ASE après sa création 
5. Créez une machine virtuelle si vous n’avez pas de réseau virtuel (pas dans le même sous-réseau que l’ASE pour éviter tout dysfonctionnement)
6. Configurez le DNS de votre sous-domaine. Vous pouvez utiliser un caractère générique avec votre sous-domaine dans votre DNS ou, si vous voulez faire de simples tests, modifier le fichier hosts sur votre machine virtuelle pour définir le nom de l’application web sur l’adresse IP virtuelle. Si votre ASE avait le sous-domaine .ilbase.com et que vous avez utilisé mytestapp pour votre application web afin de l’adresser vers mytestapp.ilbase.com, définissez cette valeur dans votre fichier hosts. (Dans Windows, le fichier hosts se situe sous C:\Windows\System32\drivers\etc\)
7. Utilisez un navigateur sur cette machine virtuelle et accédez à http://mytestapp.ilbase.com (le nom de votre application web avec votre sous-domaine)
8. Utilisez un navigateur sur cette machine virtuelle et accédez à https://mytestapp.ilbase.com (vous devez accepter le manque de sécurité si vous utilisez un certificat auto-signé). 

L’adresse IP de votre ILB est répertoriée dans vos propriétés comme l’adresse IP virtuelle

![][4]

## <a name="using-an-ilb-ase"></a>Utilisation d’un ILB ASE
#### <a name="network-security-groups"></a>Groupes de sécurité réseau
Un ILB ASE permet l’isolement réseau de vos applications car les applications ne sont pas accessibles ni même reconnues par Internet. Cette méthode est excellente pour l’hébergement de sites intranet comme les applications métier. Si vous avez besoin de restreindre davantage l’accès, vous pouvez toujours utiliser des groupes de sécurité réseau (NSG) pour contrôler l’accès au niveau du réseau. 

Si vous souhaitez utiliser des NSG pour restreindre davantage l’accès, vous devez vous assurer de ne pas interrompre la communication nécessaire au fonctionnement de l’ASE. Bien que l’accès HTTP/HTTPS s’effectue uniquement par le biais de l’ILB utilisé par l’ASE, cet ASE dépend toujours de ressources situées en dehors du réseau virtuel. Pour savoir quel accès réseau est toujours requis, consultez les informations du document [Contrôle du trafic entrant vers un environnement App Service][ControlInbound] et du document [Détails de la configuration réseau pour les environnements App Service avec ExpressRoute][ExpressRoute]. 

Pour configurer vos NSG, vous devez connaître l’adresse IP utilisée par Azure pour gérer votre ASE. Cette adresse IP est également l’adresse IP sortante de votre ASE s’il effectue des demandes Internet. L’adresse IP sortante pour votre ASE reste statique pendant toute la durée de vie de votre ASE. Si vous supprimez et recréez votre ASE, vous obtenez une nouvelle adresse IP. Pour trouver cette adresse IP, sélectionnez **Paramètres -> Propriétés** puis **Adresse IP sortante**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Gestion générale de l’ILB ASE
La gestion d’un ILB ASE est largement identique à la gestion d’un ASE standard. Vous devez faire évoluer vos pools de travail pour héberger plusieurs instances ASP et faire évoluer vos serveurs frontaux pour gérer la hausse du trafic HTTP/HTTPS. Pour obtenir des informations générales sur la gestion de la configuration d’un ASE, consultez le document [Configuration d’un environnement App Service][ASEConfig]. 

Les éléments d’administration supplémentaires sont la gestion des certificats et la gestion DNS. Vous devez obtenir et télécharger le certificat utilisé pour le protocole HTTPS après la création de l’ILB ASE et le remplacer avant son expiration. Comme Azure possède le domaine de base, nous pouvons fournir des certificats pour les ASE avec une adresse IP externe. Étant donné que le sous-domaine utilisé par un ILB ASE peut être quelconque, vous devez fournir votre propre certificat pour le protocole HTTPS. 

#### <a name="dns-configuration"></a>Configuration DNS
Lorsque vous utilisez une adresse IP virtuelle externe, le DNS est géré par Azure. Toute application créée dans votre ASE est automatiquement ajoutée à Azure DNS, qui est un DNS public. Dans un ILB ASE, vous devrez gérer vos propres DNS. Pour un sous-domaine spécifique comme contoso.corp.net, vous devez créer des enregistrements DNS A qui pointent vers votre adresse ILB :

    * 
    *.scm ftp publish 


## <a name="getting-started"></a>Prise en main
Tous les articles et procédures concernant les environnements App Service sont disponibles dans le [fichier Lisez-moi des environnements App Service](../app-service/app-service-app-service-environments-readme.md).

Pour prendre en main les environnements App Service, consultez [la présentation des environnements App Service][WhatisASE].

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/

