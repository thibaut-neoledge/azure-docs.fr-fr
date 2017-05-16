---
title: "Connexions hybrides d’Azure App Service | Microsoft Docs"
description: "Comment créer et utiliser des connexions hybrides pour accéder aux ressources dans des réseaux hétérogènes"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.contentlocale: fr-fr
ms.lasthandoff: 04/25/2017


---

# <a name="azure-app-service-hybrid-connections"></a>Connexions hybrides d’Azure App Service #

## <a name="overview"></a>Vue d’ensemble ##

Les connexions hybrides sont un service dans Azure et une fonctionnalité dans Azure App Service.  En tant que service, il exploite et inclut des fonctionnalités qui vont au-delà de celles utilisées dans Azure App Service.  Pour en savoir plus sur les connexions hybrides et leur utilisation en dehors d’Azure App Service, vous pouvez démarrer ici, [Connexions hybrides Azure Relay][HCService]

Dans Azure App Service, les connexions hybrides peuvent être utilisées pour accéder aux ressources d’application d’autres réseaux. Elles permettent d’accéder À PARTIR DE votre application À un point de terminaison d’application.  Elles n’autorisent pas à une autre fonction d’accéder à votre application.  Utilisée dans App Service, chaque connexion hybride correspond à une combinaison d’hôte et de port TCP unique.  Cela signifie que le point de terminaison de connexion hybride peut se trouver sur un quelconque système d’exploitation et toute application à condition que vous ayez accès à un port d’écoute TCP. Les connexions hybrides ne détectent pas et ne prennent pas en compte le protocole d’application ou les ressources auxquels vous accédez.  Elles fournissent simplement un accès réseau.  


## <a name="how-it-works"></a>Fonctionnement ##
La fonctionnalité Connexions hybrides se compose de deux appels sortants vers Service Bus Relay.  Il existe une connexion à partir d’une bibliothèque sur l’hôte sur lequel votre application est exécutée dans le service d’application, et entre le Gestionnaire de connexion hybride (GCH) et Service Bus Relay.  Le GCH est un service de relais que vous déployez dans le réseau d’hébergement 

Grâce aux deux connexions liées, votre application inclut un tunnel TCP vers une combinaison hôte:port fixe de l’autre côté du GCH.  La connexion utilise TLS 1.2 pour la sécurité et des clés SAP pour l’authentification/autorisation.    

![][1]

Lorsque votre application effectue une requête DNS qui correspond à un point de terminaison de connexion hybride configuré, le trafic TCP sortant est redirigé vers la connexion hybride.  

> [!NOTE]
> Cela signifie que vous devez toujours utiliser un nom DNS pour votre connexion hybride.  Certains logiciels clients n’effectuent une recherche DNS que si le point de terminaison utilise une adresse IP à la place.
>
>

Il existe deux types de connexions hybrides : les nouvelles connexions hybrides qui sont proposées en tant que service sous Azure Relay et les anciennes connexions hybrides BizTalk.  Les anciennes connexions hybrides BizTalk sont appelées des connexions hybrides classiques dans le portail.  Plus d’informations sur celles-ci sont fournies plus loin dans ce document.

### <a name="app-service-hybrid-connection-benefits"></a>Avantages d’une connexion hybride App Service ###

La fonctionnalité de connexions hybrides offre un certain nombre d’avantages, notamment

- les applications peuvent accéder en toute sécurité aux systèmes et services locaux ;
- la fonctionnalité ne nécessite pas un point de terminaison Internet accessible ;
- sa configuration est simple et rapide ;  
- chaque connexion hybride correspond à une combinaison hôte:port unique, gage de sécurité ;
- elle ne nécessite généralement pas de ports de pare-feu car les connexions sont toutes sortantes sur des ports web standard ;
- la fonctionnalité se situant au niveau du réseau, cela signifie également qu’elle n’est pas spécifique du langage utilisé par votre application et de la technologie utilisée par le point de terminaison ;
- elle peut être utilisée pour fournir un accès à plusieurs réseaux à partir d’une même application. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Ce que vous ne pouvez pas faire avec les connexions hybrides ###

Vous ne pouvez pas faire certaines opérations avec les connexions hybrides, notamment :

- montage d’un lecteur ;
- utilisation d’UDP ;
- accès à des services TCP qui utilisent des ports dynamiques tels que le mode FTP passif ou le mode passif étendu ;
- prise en charge LDAP, qui est parfois requise par UDP ;
- prise en charge d’Active Directory.

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Ajout et création d’une connexion hybride dans votre application ##

Une connexion hybride peut être créée via le portail de l’application ou à partir du portail du service de connexion hybride.  Nous vous recommandons vivement d’utiliser le portail de l’application pour créer les connexions hybrides à utiliser avec votre application.  Pour créer une connexion hybride, accédez au [portail Azure][portal] puis à l’interface utilisateur de votre application.  Sélectionnez **Mise en réseau > Configurer vos points de terminaison de connexion hybride**.  Là, vous pouvez voir ici les connexions hybrides qui sont configurées avec votre application  

![][2]

Pour ajouter une nouvelle connexion hybride, cliquez sur Ajouter une connexion hybride.  L’interface utilisateur qui s’ouvre répertorie les connexions hybrides que vous avez déjà créées.  Pour en ajouter une ou plusieurs à votre application, cliquez sur les connexions souhaitées et appuyez sur **Ajouter la connexion hybride sélectionnée**.  

![][3]

Si vous souhaitez créer une nouvelle connexion hybride, cliquez sur **Créer une connexion hybride**.  Vous spécifiez ici le : 

- Nom du point de terminaison
- Nom d’hôte du point de terminaison
- Port du point de terminaison
- Espace de noms Service Bus que vous voulez utiliser

![][4]

Chaque connexion hybride est liée à un espace de noms Service Bus et chaque espace de noms Service Bus se trouve dans une région Azure.  Il convient de tester et d’utiliser un espace de noms Service Bus dans la même région que votre application pour éviter une latence du réseau.

Si vous souhaitez supprimer votre connexion hybride de votre application, cliquez avec le bouton droit sur celle-ci et sélectionnez **Déconnecter**.  

Lorsqu’une connexion hybride est ajoutée à votre application web, vous pouvez afficher ses détails en cliquant simplement dessus.  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>Connexions hybrides et plans App Service ##

Les seules connexions hybrides que vous pouvez créer désormais sont de nouvelles connexions hybrides.  Elles sont uniquement disponibles dans les références de tarification De base, Standard, Premium et Isolé.  Des limites sont liées au plan de tarification.  

| Plan de tarification | Nombre de connexions hybrides utilisables dans le plan |
|----|----|
| De base | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolé | 200 |

Dans la mesure où il existe des restrictions de plan App Service, une interface utilisateur existe également dans le plan App Service qui vous montre le nombre de connexions hybrides utilisées et par quelles applications.  

![][6]

Comme dans la vue de l’application, vous pouvez afficher plus d’informations sur votre connexion hybride en cliquant sur celle-ci.  Dans les propriétés affichées ici, vous pouvez voir toutes les informations disponibles dans la vue de l’application, mais vous pouvez également savoir combien d’autres applications du même plan App Service utilisent cette connexion hybride.

![][7]

Même s’il existe une limite du nombre de points de terminaison de connexion hybride pouvant être utilisés dans un plan App Service, chaque connexion hybride utilisée peut l’être sur un nombre quelconque d’applications de ce plan App Service.  Ainsi, si je dispose d’1 connexion hybride que j’utilisais dans 5 applications distinctes de mon plan App Service, je dispose toujours d’1 seule connexion hybride.

Un coût supplémentaire s’applique sur les connexions hybrides en plus de celles utilisables dans une référence De base, Standard, Premium ou Isolé.  Pour plus d’informations sur la tarification de la connexion hybride, accédez ici : [Tarification Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestionnaire de connexion hybride ##

Pour que les connexions hybrides fonctionnent, vous avez besoin d’un agent de relais dans le réseau qui héberge votre point de terminaison de connexion hybride.  Cet agent de relais est appelé le Gestionnaire de connexion hybride (GCH).  Cet outil peut être téléchargé à partir de l’interface utilisateur **Mise en réseau > Configurer vos points de terminaison de connexion hybride** disponible à partir de votre application dans le [portail Azure][portal].  

Cet outil s’exécute sur Windows Server 2008 R2 et les versions ultérieures de Windows.  Une fois installé, le GCH s’exécute en tant que service.  Ce service se connecte au relais Service Bus Azure en fonction des points de terminaison configurés.  Les connexions à partir du GCH sont sortantes vers les ports 80 et 443.    

Le GCH inclut une interface utilisateur permettant de le configurer.  Après avoir installé le GCH, vous pouvez ouvrir l’interface utilisateur en exécutant le fichier HybridConnectionManagerUi.exe qui se trouve dans le répertoire d’installation du Gestionnaire de connexion hybride.  Vous pouvez également y accéder facilement sur Windows 10 en saisissant *Interface utilisateur du Gestionnaire de connexion hybride* dans la zone de recherche.  

À l’ouverture de l’interface utilisateur du GCH, vous voyez tout d’abord un tableau qui répertorie toutes les connexions configurées avec cette instance du GCH.  Si vous souhaitez apporter des modifications, vous devez vous authentifier auprès d’Azure. 

Pour ajouter une ou plusieurs connexions hybrides à votre GCH :

1. Démarrez l’interface utilisateur du GCH
1. Cliquez sur Configure another hybrid connection (Configurer une autre connexion hybride) ![][8]

1. Connectez-vous à votre compte Azure
1. Sélectionnez un abonnement
1. Cliquez sur les connexions hybrides que vous souhaitez voir relayées par ce GCH ![][9]

1. Cliquez sur Enregistrer.

À ce stade, vous voyez les connexions hybrides que vous avez ajoutées.  Vous pouvez également cliquer sur la connexion hybride configurée et afficher les détails la concernant.

![][10]

Pour que le GCH puisse prendre en charge les connexions hybrides avec lesquelles il est configuré, il doit disposer de :

- Accès TCP à Azure sur les ports 80 et 443
- Accès TCP au point de terminaison de connexion hybride
- possibilité de recherches DNS sur l’hôte du point de terminaison et l’espace de noms Service Bus Azure

Le GCH prend en charge les nouvelles connexions hybrides et les anciennes connexions hybrides BizTalk.

### <a name="redundancy"></a>Redondance ###

Chaque GCH peut prendre en charge plusieurs connexions hybrides.  De plus, toute connexion hybride peut être prise en charge par plusieurs GCH.  Le comportement par défaut consiste à faire transiter le trafic en tourniquet (round robin) entre les GCH configurés pour n’importe quel point de terminaison donné.  Si vous souhaitez une haute disponibilité sur vos connexions hybrides à partir de votre réseau, instanciez simplement plusieurs GCH sur des ordinateurs distincts. 

### <a name="manually-adding-a-hybrid-connection"></a>Ajout manuel d’une connexion hybride ###

Si vous souhaitez qu’un utilisateur en dehors de votre abonnement héberge une instance GCH pour une connexion hybride donnée, vous pouvez lui communiquer la chaîne de connexion de passerelle de la connexion hybride.  Vous la trouverez dans les propriétés d’une connexion hybride dans le [portail Azure][portal]. Pour utiliser cette chaîne, cliquez sur le bouton **Configurer manuellement** dans le GCH et collez la chaîne de connexion de passerelle.


## <a name="troubleshooting"></a>Résolution de problèmes ##

Lorsque votre connexion hybride est configurée avec une application en cours d’exécution et qu’il existe au moins un GCH dans lequel cette connexion hybride est configurée, l’état **Connecté** est indiqué dans le portail.  Si l’état n’est pas **Connecté**, cela signifie que votre application est arrêtée ou que votre GCH ne peut pas se connecter à Azure sur les ports 80 ou 443.  

La raison principale pour laquelle les clients ne peuvent pas se connecter à leur point de terminaison est que le point de terminaison a été spécifié à l’aide d’une adresse IP au lieu d’un nom DNS.  Si votre application ne peut pas accéder au point de terminaison souhaité et que vous avez utilisé une adresse IP, utilisez un nom DNS valide sur l’hôte sur lequel le GCH est exécuté.  Vous devez également vérifier que le nom DNS est correctement résolu sur l’hôte sur lequel le GCH est exécuté et qu’il existe une connectivité entre l’hôte exécutant le GCH et le point de terminaison de connexion hybride.  

Un outil dans App Service appelé tcpping peut être appelé à partir de la console.  Cet outil peut vous indiquer si vous avez accès à un point de terminaison TCP, mais ne vous dit pas si vous avez accès à un point de terminaison de connexion hybride.  Lorsqu’elle est utilisée dans la console par rapport à un point de terminaison de connexion hybride, la commande ping vous indique uniquement qu’une connexion hybride est configurée pour votre application qui utilise cette combinaison hôte:port.  

## <a name="biztalk-hybrid-connections"></a>Connexions hybrides BizTalk ##

L’ancienne fonctionnalité Connexions hybrides BizTalk a été désactivée pour ne plus permettre de créer de connexion hybride BizTalk supplémentaire.  Vous pouvez continuer à utiliser vos connexions hybrides BizTalk existantes avec vos applications, mais vous devez migrer vers le nouveau service.  Les avantages du nouveau service par rapport à la version BizTalk incluent entre autres :

- aucun compte BizTalk supplémentaire n’est requis ;
- TLS 1.2 au lieu de 1.0 comme dans les connexions hybrides BizTalk ;
- la communication se fait sur les ports 80 et 443 à l’aide d’un nom DNS pour accéder à Azure au lieu d’adresses IP et d’une plage d’autres ports supplémentaires.  

Pour ajouter une connexion hybride BizTalk à votre application, accédez à votre application dans le [portail Azure][portal], puis cliquez sur **Mise en réseau > Configurer vos points de terminaison de connexion hybride**.  Dans la table de connexions hybrides classiques, cliquez sur **Ajouter la connexion hybride classique**.  La liste de vos connexions hybrides BizTalk s’affiche alors.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/


