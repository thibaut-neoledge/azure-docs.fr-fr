---
title: "Mise à l’échelle géolocalisée avec les environnements App Service"
description: "Découvrez comment mettre à l’échelle des applications horizontalement en utilisant Traffic Manager et les environnements App Service."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 21f747239e565aba79a84c8e946a71e306b64968
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Mise à l’échelle géolocalisée avec les environnements App Service
## <a name="overview"></a>Vue d'ensemble
Les scénarios d’application s’appliquant à très grande échelle peuvent dépasser la capacité de ressources de calcul disponible pour un seul déploiement d’application.  À titre d’exemple, les applications de vote, les événements sportifs et les programmes de divertissement télévisés sont des scénarios qui fonctionnent à très grande échelle. Les exigences à grande échelle peuvent être satisfaites par la mise à l’échelle horizontale des applications, avec plusieurs déploiements dans une seule ou plusieurs régions, pour gérer les charges extrêmes.

Les environnements App Service sont une plate-forme idéale pour l’évolution horizontale.  Une fois que la configuration d’environnements App Service a été sélectionnée et qu’elle peut prend en charge un taux de requête connu, les développeurs peuvent déployer des environnements App Service supplémentaires en mode « découpage » pour atteindre la capacité de charge maximale souhaitée.

Par exemple, supposons qu’une application s’exécutant sur une configuration de l’environnement App Service a été testée pour gérer les demandes de 20 Ko par seconde (RPS).  Si la capacité de charge maximale souhaitée est de 100 Ko RPS, il est possible de créer et configurer cinq (5) environnements App Service s’assurer que l’application pourra gérer la charge maximale prévue.

Les clients accédant généralement aux applications par le biais d’un domaine personnalisé (ou personnel), les développeurs ont besoin d’un moyen de distribuer les demandes d’application parmi toutes les instances d’environnement App Service.  Un excellent moyen d’y parvenir consiste à résoudre le domaine personnalisé à l’aide d’un [profil Azure Traffic Manager][AzureTrafficManagerProfile].  Le profil Traffic Manager peut être configuré pour pointer sur tous les environnements App Service.  Traffic Manager peut être configuré pour pointer automatiquement sur tous les environnements App Service en fonction des paramètres d’équilibrage de charge du profil Traffic Manager.  Cette approche fonctionne que les environnements App Service soient déployés dans une seule région Azure ou dans plusieurs régions Azure à travers le monde.

En outre, comme les clients accèdent aux applications via le domaine personnel, les clients ne connaissent pas le nombre d’environnements App Service exécutant une application.  Par conséquent les développeurs peuvent rapidement et facilement ajouter et supprimer des environnements App Service en fonction de la charge du trafic observée.

Le schéma conceptuel ci-dessous décrit une application mise à l’échelle horizontalement dans trois environnements App Service au sein d’une seule région.

![Architecture conceptuelle][ConceptualArchitecture] 

Le reste de cette rubrique décrit les étapes nécessaires à la configuration d’une topologie distribuée pour l’exemple d’application à l’aide de plusieurs environnements App Service.

## <a name="planning-the-topology"></a>Planification de la topologie
Avant de créer une empreinte d’application distribuée, il peut s’avérer utile de disposer de quelques éléments d’informations.

* **Domaine personnalisé pour l’application :** quel est le nom de domaine personnalisé que les utilisateurs utiliseront pour accéder à l’application ?  Pour l’exemple d’application, le nom de domaine personnalisé est *www.scalableasedemo.com*
* **Domaine Traffic Manager :** vous devez choisir un nom de domaine au moment de la création d’un [profil Azure Traffic Manager][AzureTrafficManagerProfile].  Ce nom est associé au suffixe *trafficmanager.net* pour enregistrer une entrée de domaine gérée par Traffic Manager.  Dans l’exemple d’application, le nom choisi est *scalable-ase-demo*.  Par conséquent, le nom de domaine complet géré par Traffic Manager est *scalable-ase-demo.trafficmanager.net*.
* **Stratégie de mise à l’échelle de l’empreinte de l’application :** l’empreinte de l’application sera-t-elle distribuée sur plusieurs environnements App Service dans une seule région ?  Plusieurs régions ?  Une combinaison des deux approches ?  La décision doit être prise en fonction des attentes depuis l’emplacement d’origine du trafic du client, ainsi que sur la manière dont peut évoluer le reste de l’application prenant en charge l’infrastructure principale.  Par exemple, avec une application à 100 % sans état, une application peut être adaptée à très grande échelle à l’aide d’une combinaison de plusieurs environnements App Service, puis multipliée par les environnements App Service déployés dans plusieurs régions Azure.  Avec plus de 15 régions Azure publiques disponibles, les clients peuvent véritablement créer une empreinte d’application à échelle mondial.  Pour l’exemple d’application utilisé pour cet article, trois environnements App Service ont été créés dans une seule région Azure (Sud-Central des États-Unis).
* **Convention de dénomination pour les environnements App Service :** chaque environnement App Service exige un nom unique.  Au-delà d’un ou deux environnements App Service, une convention d’affectation de noms identifiant chaque environnement App Service peut s’avérer utile.  Pour l’exemple d’application, une convention d’affectation de noms simple a été utilisée.  Les noms des trois environnements App Service sont respectivement *fe1ase*, *fe2ase* et *fe3ase*.
* **Convention de dénomination pour les applications :** comme plusieurs instances de l’application vont être déployées, un nom est requis pour chacune d’entre elles.  Les environnements App Service proposent une fonctionnalité peu connue, mais très pratique, qui fait que le même nom d’application peut être utilisé sur plusieurs environnements App Service.  Étant donné que chaque environnement App Service comporte un suffixe de domaine unique, les développeurs peuvent choisir d’utiliser le même nom d’application dans chaque environnement.  Par exemple, un développeur peut avoir des applications nommées comme suit : *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc.  Cependant, pour l’exemple d’application, chaque instance de l’application a également un nom unique.  Les noms d’instance application utilisés sont *webfrontend1*, *webfrontend2* et *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Configuration d’un profil Traffic Manager
Une fois que plusieurs instances d’une application sont déployées sur plusieurs environnements App Service, les instances d’application individuelles peuvent être enregistrées avec Traffic Manager.  Dans l’exemple d’application, un profil Traffic Manager qui peut acheminer les clients vers les instances d’applications qui suivent est nécessaire pour *scalable-ase-demo.trafficmanager.net* :

* **webfrontend1.fe1ase.p.azurewebsites.net :** instance de l’exemple d’application déployée sur le premier environnement App Service.
* **webfrontend2.fe2ase.p.azurewebsites.net :** instance de l’exemple d’application déployée sur le deuxième environnement App Service.
* **webfrontend3.fe3ase.p.azurewebsites.net :** instance de l’exemple d’application déployée sur le troisième environnement App Service.

Le moyen le plus simple d’enregistrer plusieurs points de terminaison Azure App Service s’exécutant tous dans la **même** région Azure, est d’utiliser la [prise en charge Powershell Azure Resource Manager Traffic Manager][ARMTrafficManager].  

La première étape consiste à créer un profil Azure Traffic Manager.  Le code suivant montre comment le profil a été créé pour l’exemple d’application :

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Notez la manière dont le paramètre *RelativeDnsName* a été défini sur *scalable-ase-demo*.  Voici comment le nom de domaine *scalable-ase-demo.trafficmanager.net* est créé et associé à un profil Traffic Manager.

Le paramètre *TrafficRoutingMethod* définit la stratégie d’équilibrage de charge que Traffic Manager va utiliser pour déterminer la répartition de la charge du client sur tous les points de terminaison disponibles.  Dans cet exemple, la méthode *Weighted* a été choisie.  Ainsi, les demandes clients sont réparties entre les différents points de terminaison d’application enregistrés en fonction de la pondération relative associée à chacun d’eux. 

Avec le profil créé, chaque instance de l’application est ajoutée au profil en tant que point de terminaison Azure natif.  Le code ci-dessous extrait une référence à chaque application web frontale et ajoute chaque application en tant que point de terminaison Traffic Manager au moyen du paramètre *TargetResourceId* .

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Notez qu’il existe un appel de *Add-AzureTrafficManagerEndpointConfig* pour chaque instance d’application individuelle.  Le paramètre *TargetResourceId* dans chaque commande Powershell fait référence à l’une des trois instances d’application déployées.  Le profil Traffic Manager répartira la charge entre les trois points de terminaison enregistrés dans le profil.

Les trois points de terminaison utilisent la même valeur (10) pour le paramètre *Poids* .  Ainsi, Traffic Manager répartit les demandes clients entre les trois instances d’application de façon relativement uniforme. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Pointer le domaine personnalisé de l’application sur le domaine Traffic Manager
La dernière étape obligatoire consiste à pointer le domaine personnalisé de l’application sur le domaine Traffic Manager.  Pour l’exemple d’application, cela signifie pointer *www.scalableasedemo.com* sur *scalable-ase-demo.trafficmanager.net*.  Cette étape doit être effectuée avec le service de registre de domaine qui gère le domaine personnalisé.  

Avec les outils de gestion d’enregistrement de domaine, vous devez créer un enregistrement CNAME pointant sur le domaine personnalisé du domaine Traffic Manager.  L’illustration ci-dessous montre un exemple de cette configuration CNAME :

![Enregistrement CNAME pour le domaine personnalisé][CNAMEforCustomDomain] 

Bien que cet aspect ne soit pas traité dans cette rubrique, n’oubliez pas que le domaine personnalisé doit être enregistré avec chaque instance d’application individuelle.  Si ce n’est pas le cas, si une demande s’adresse à une instance d’application et que l’application ne dispose pas d’un domaine personnalisé enregistré avec l’application, la demande échoue.  

Dans cet exemple le domaine personnalisé est *www.scalableasedemo.com*, et chaque instance d’application est associée à un domaine personnalisé.

![Domaine personnalisé][CustomDomain] 

Pour obtenir un récapitulatif de l’enregistrement d’un domaine personnalisé avec les applications Azure App Service, consultez l’article qui suit sur l’[enregistrement de domaines personnalisés][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Essai de la topologie distribuée
Le résultat final de la configuration de Traffic Manager et de DNS est que les demandes de *www.scalableasedemo.com* circulent dans suivant la séquence suivante :

1. Un navigateur ou un périphérique effectue une recherche DNS sur *www.scalableasedemo.com*
2. L’entrée CNAME sur l’enregistrement de domaine fait que la recherche DNS est redirigée vers Azure Traffic Manager.
3. Une recherche DNS sur *scalable-ase-demo.trafficmanager.net* est effectuée sur l’un des serveurs DNS Traffic Manager d’Azure.
4. Selon la stratégie d’équilibrage de charge (paramètre *TrafficRoutingMethod* utilisé précédemment lors de la création du profil Traffic Manager), Traffic Manager sélectionne un des points de terminaison configurés et renvoie le nom de domaine complet de ce point de terminaison au navigateur ou au périphérique.
5. Étant donné que le nom de domaine complet du point de terminaison est l’Url d’une instance d’application s’exécutant dans un environnement App Service, le navigateur ou le périphérique demandera à un serveur DNS de Microsoft Azure de résoudre le nom de domaine complet sur une adresse IP. 
6. Le navigateur ou le périphérique envoie la demande HTTP/S à l’adresse IP.  
7. La demande arrive dans une des instances d’application en cours d’exécution sur un des environnements App Service.

L’image de la console ci-dessous représente une recherche DNS sur le domaine personnalisé de l’exemple d’application se résolvant sur une instance d’application en cours d’exécution sur un des trois exemples d’environnements (dans ce cas le deuxième des trois environnements d’App Service ) :

![Recherche DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Informations et liens supplémentaires
Documentation sur la [prise en charge Powershell Azure Resource Manager Traffic Manager][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 

