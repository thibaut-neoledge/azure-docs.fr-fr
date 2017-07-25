---
title: "Utilisation d’un environnement Azure App Service"
description: "Comment créer, publier et mettre à l’échelle des applications dans un environnement Azure App Service"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: c24f716d58f534d1439377234fa1263269961db7
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---
# <a name="using-an-app-service-environment"></a>Utilisation d’un environnement App Service #

## <a name="overview"></a>Vue d'ensemble ##

Un environnement ASE (App Service Environment) est un déploiement d’Azure App Service dans un sous-réseau dans le réseau virtuel Azure d’un client. Elle comprend :

- Serveurs frontaux : c’est le point de terminaison HTTP/HTTPS dans un environnement ASE.
- Workers : il s’agit des ressources qui hébergent vos applications.
- Base de données : celle-ci contient des informations qui définissent l’environnement.
- Stockage : le stockage est utilisé pour héberger les applications publiées client.

> [!NOTE]
> Il existe deux versions de l’environnement App Service : ASEv1 et ASEv2. Dans un environnement ASEv1, vous devez gérer les ressources avant de pouvoir les utiliser. Pour savoir comment configurer et gérer un environnement ASEv1, consultez [Configuration d’un environnement App Service][ConfigureASEv1]. Le reste de ce document est consacré à l’environnement ASEv2.
>
>

Vous pouvez déployer un environnement ASE (ASEv1 et ASEv2) avec une adresse IP virtuelle externe ou interne pour accéder à l’application. Le déploiement avec une adresse IP externe est généralement appelé environnement ASE externe, et la version interne est appelée environnement ASE de l’équilibrage de charge interne, car elle utilise un équilibrage de charge interne. Pour plus d’informations sur l’environnement ASE d’équilibrage de charge interne, consultez [Create and Use an Internal Load Balancer with an App Service Environment][MakeILBASE] (Créer et utiliser un équilibreur de charge interne avec un environnement App Service).

## <a name="create-a-web-app-in-an-ase"></a>Créer une application web dans un environnement ASE ##

La création d’une application web dans un environnement ASE est le même processus que celui habituellement utilisé pour créer une application web, à quelques petites différences près. Lorsque vous créez un plan App Service :

- Au lieu de choisir un emplacement géographique dans lequel déployer votre application, vous choisissez un environnement ASE comme emplacement.
- Tous les plans App Service créés dans un environnement ASE doivent être dans un niveau tarifaire Isolé.

Si vous ne disposez pas d’un environnement ASE, vous pouvez en créer un en suivant les instructions de l’article [Create an external App Service Environment][MakeExternalASE] (Créer un environnement App Service externe).

Pour créer une application web dans un environnement ASE :

1. Cliquez sur **Nouveau &gt; Web and Mobile** (Web et mobile), puis sélectionnez **Application web**.
2. Donnez un nom à l’application web. Si vous avez déjà sélectionné un plan App Service dans un environnement ASE, le nom de domaine pour l’application reflètera le nom de domaine de l’environnement ASE.

    ![][1]

1. Sélectionnez un abonnement.
2. Donnez un nom à un nouveau groupe de ressources ou sélectionnez **Use existing** (Utiliser existant) et choisissez-en un dans la liste déroulante.
3. Sélectionnez un plan App Service existant dans votre environnement ASE ou créez-en un en procédant comme suit :
    1. Sélectionnez **Créer**.
    2. Donnez un nom à votre plan App Service.
    3. Sélectionnez votre environnement ASE dans la liste déroulante **Emplacement**.
    4. Sélectionnez un niveau tarifaire **Isolé**. Cliquez sur **Sélectionner**.
    5. Cliquez sur **OK**.
    
    ![][2]
1. Cliquez sur **Create**.

## <a name="how-scale-works"></a>Fonctionnement de la mise à l’échelle ##

Chaque application App Service s’exécute dans un plan App Service. Le modèle de conteneur est le suivant : les environnements contiennent les plans App Service et les plans App Service contiennent les applications. Lorsque vous mettez à l’échelle une application, vous mettez à l’échelle le plan App Service, et donc toutes les applications contenues dans le même plan.

Dans un environnement ASEv2, lorsque vous mettez à l’échelle un plan App Service, l’infrastructure nécessaire est automatiquement ajoutée. Cela est différent de l’environnement ASEv1, dans lequel l’infrastructure nécessaire doit être ajoutée avant que vous puissiez créer ou augmenter la taille des instances de votre plan App Service. Dans l’environnement ASEv2, cela signifie qu’il existe un délai pour les opérations de mise à l’échelle, car l’infrastructure est ajoutée.

Dans l’App Service multilocataire, la mise à l’échelle est généralement immédiate, car il existe un pool de ressources pouvant être instantanément utilisé pour prendre en charge l’augmentation de la taille des instances. Dans un environnement ASE, il n’existe pas de mémoire tampon et les ressources peuvent être allouées en fonction des besoins.

Dans un environnement ASE, vous pouvez monter en puissance 100 instances maximum. Ces 100 instances peuvent toutes se trouver dans un seul plan App Service ou être distribuées dans plusieurs plans App Service.

## <a name="ip-addresses"></a>Adresses IP ##

App Service a la possibilité d’allouer une adresse IP dédiée à une application. Cette fonctionnalité est disponible en configurant un certificat SSL basé sur IP, comme indiqué ici : [Attribuer un certificat à une application App Service][ConfigureSSL]. Toutefois, dans un environnement ASE, une exception notable existe : vous ne pouvez pas ajouter d’adresses IP destinées à être utilisées pour un certificat basé sur IP dans un environnement ASE d’équilibrage de charge interne.

Dans un environnement ASEv1, vous devez allouer les adresses IP en tant que ressources avant de pouvoir les utiliser. Dans un environnement ASEv2, vous les utilisez simplement à partir de votre application comme vous le feriez dans l’App Service multilocataire. Il existe toujours une adresse de secours dans un environnement ASEv2, dans la limite de 30 adresses IP. Chaque fois que vous en utilisez une, une autre est ajoutée afin qu’une adresse soit toujours disponible si besoin. Un délai est nécessaire pour allouer une autre adresse IP, ce qui empêche d’ajouter des adresses IP de façon rapprochée.

## <a name="front-end-scaling"></a>Mise à l’échelle du serveur frontal ##

Dans un environnement ASEv2, lorsque vous augmentez la taille des instances de vos plans App Service, les workers sont automatiquement ajoutés pour les prendre en charge. Outre les deux serveurs frontaux avec lesquels chaque environnement ASE est créé, la taille des instances des serveurs frontaux est aussi automatiquement augmentée à une fréquence d’un serveur frontal toutes les 15 instances dans vos plans App Service. Cela signifie que si vous avez 15 instances, vous avez trois serveurs frontaux. Si vous effectuez une mise à l’échelle pour 30 instances, vous avez quatre serveurs frontaux, et ainsi de suite.

Cela doit être plus que suffisant pour la plupart des scénarios, mais s’il est nécessaire d’augmenter la taille des instances à une fréquence plus élevée, vous pouvez définir le ratio sur une valeur de un serveur frontal toutes les 5 instances dans vos plans App Service. La modification du ratio est facturée, comme décrit dans [Tarification de App Service][Pricing].

Les ressources du serveur frontal sont le point de terminaison HTTP/HTTPS pour l’environnement ASE. Avec la configuration de serveur frontal par défaut, l’utilisation de mémoire par serveur frontal se situe en permanence autour de 60 %. Les charges de travail client ne s’exécutent pas sur un serveur frontal. Le facteur clé pour un serveur frontal par rapport à la mise à l’échelle est l’UC, qui dépend principalement du trafic HTTPS.

## <a name="app-access"></a>Accès de l’application ##

Dans un environnement ASE externe, le domaine utilisé lors de la création d’applications est différent de l’App Service multilocataire et inclut le nom de l’environnement ASE. Pour plus d’informations sur la création d’un environnement ASE externe, consultez [Create an external App Service Environment][MakeExternalASE] (Créer un environnement App Service externe). Le nom de domaine dans un environnement ASE externe ressemble à *.&lt;asename&gt;.p.azurewebsites.net*. Cela signifie que si votre ASE s’appelle _external-ase_, et si vous hébergez une application appelée _contoso_ dans cet environnement ASE, vous devez l’atteindre aux URL suivantes :

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

L’URL *contoso.scm.external-ase.p.azurewebsites.net* est utilisée pour accéder à la console Kudu ou pour publier votre application à l’aide de web deploy. Pour obtenir des informations sur la console Kudu, visionnez [Exploring the Super Secret Kudu Debug Console - with David Ebbo][Kudu] (Découverte de la console de débogage Kudu super secrète avec David Ebbo). La console Kudu offre une interface utilisateur web pour le débogage, le téléchargement et la modification des fichiers, etc.

Dans un environnement ASE d’équilibrage de charge interne, vous déterminez le domaine au moment du déploiement. Pour plus d’informations sur la création d’un environnement ASE d’équilibrage de charge interne, consultez [Create and Use an Internal Load Balancer with an App Service Environment][MakeILBASE] (Créer et utiliser un équilibreur de charge interne avec un environnement App Service). Si vous spécifiez le nom de domaine _ilb-ase.info_, les applications se trouvant dans cet environnement ASE utilisent ce domaine lors de la création d’applications. Pour l’application nommée _contoso_, les URL seraient :

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publication ##

Tout comme vous le feriez avec l’App Service multilocataire, dans un environnement ASE, vous pouvez publier avec :

- Web deploy
- FTP
- Intégration continue
- Opération de glisser-déplacer dans la console Kudu
- Un IDE tel que Visual Studio, Eclipse ou Intellij IDEA

Avec un environnement ASE externe, tout ceci est identique. Pour plus d’informations, consultez [Déploiement de votre application dans Azure App Service][AppDeploy]. 

La grande différence avec la publication concerne un environnement ASE d’équilibreur de charge interne. Avec un environnement ASE d’équilibreur de charge interne, les points de terminaison de publication sont tous uniquement disponibles via l’équilibreur de charge interne. L’équilibreur de charge interne se trouve sur une adresse IP privée dans le sous-réseau de l’environnement ASE du réseau virtuel. Si vous n’avez pas d’accès réseau à l’équilibreur de charge interne, vous ne pouvez pas publier d’applications dans cet environnement ASE. Comme indiqué dans [Create and Use an Internal Load Balancer with an App Service Environment][MakeILBASE] (Créer et utiliser un équilibreur de charge interne avec un environnement App Service), vous devez configurer le DNS pour les applications du système. Cela inclut le point de terminaison GCL. Si ces éléments ne sont pas définis correctement, vous ne pourrez effectuer aucune publication. Vos IDE doivent également avoir un accès réseau à l’équilibreur de charge interne pour publier directement dans celui-ci.

Les systèmes d’intégration continue basés sur Internet, comme Github et VSTS (Visual Studio Team Services) ne fonctionnent pas avec un environnement ASE d’équilibreur de charge interne, car le point de terminaison de publication n’est pas accessible via Internet. À la place, vous devez utiliser un système d’intégration continue utilisant un modèle d’extraction, par exemple Dropbox.

Les points de terminaison de publication pour les applications d’un environnement ASE d’équilibreur de charge interne utilisent le domaine avec lequel l’environnement ASE d’équilibreur de charge interne a été créé. Ce peut être observé dans le profil de publication de l’application et dans le panneau du portail de l’application (dans **Vue d’ensemble** > **Bases** et également dans **Propriétés**). 

## <a name="pricing"></a>Tarification ##

Avec l’environnement ASEv2, il existe une nouvelle référence SKU de tarification qui est utilisée uniquement avec cet environnement et qui s’appelle **Isolé**. Tous les plans App Service qui sont hébergés dans un environnement ASEv2 se trouvent dans la référence SKU de tarification Isolé. En plus de vos plans App Service, il existe un tarif fixe pour l’environnement ASE en lui-même. Ce prix ne change pas avec la taille de votre environnement ASE. 

Les autres frais potentiels concernent l’ajustement du ratio de mise à l’échelle des serveurs frontaux ou la taille des serveurs frontaux. Si vous ajustez le ratio de mise à l’échelle afin que les serveurs frontaux soient ajoutés plus rapidement, vous paierez pour tous les autres cœurs qui n’auraient pas été automatiquement ajoutés au système. De même, si vous sélectionnez une taille plus importante pour les serveurs frontaux, vous paierez pour les cœurs qui n’étaient pas alloués automatiquement. Par exemple, si vous ajustez le ratio de mise à l’échelle sur 10, cela signifie qu’un serveur frontal est ajouté toutes les 10 instances dans vos plans App Service. Le prix fixe couvre un taux de mise à l’échelle de 1 serveur frontal toutes les 15 instances. Avec un ratio de mise à l’échelle de 10, vous serez facturé pour le troisième serveur frontal qui est ajouté pour les 10 instances ASP, mais vous n’aurez pas besoin de payer lorsque vous atteignez 15 instances, car elles auraient été ajoutées automatiquement.

Pour en savoir plus, consultez [Tarification de App Service][Pricing].

## <a name="deleting-an-ase"></a>Suppression d’un environnement ASE ##

Si vous voulez supprimer un environnement App Service, utilisez simplement l’action **Supprimer** en haut du panneau Environnement App Service. Lorsque vous effectuez cette opération, vous êtes invité à entrer le nom de votre environnement App Service pour confirmer votre action. Lorsque vous supprimez un environnement App Service, vous supprimez également l’ensemble de son contenu. 

![][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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

