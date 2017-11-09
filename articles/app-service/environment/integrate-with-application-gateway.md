---
title: "Intégration de votre environnement App Service ILB à une passerelle Azure Application Gateway"
description: "Procédure pas à pas de l’intégration d’une application à votre environnement App Service ILB par l’intermédiaire de votre passerelle Azure Application Gateway"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>Intégration de votre environnement App Service ILB à une passerelle d’application #

[Azure App Service Environment](./intro.md) est un déploiement d’Azure App Service dans le sous-réseau du réseau virtuel Azure d’un client. Il peut être déployé avec un point de terminaison public ou privé pour accéder aux applications. Le déploiement de l’environnement App Service avec un point de terminaison privé est appelé « environnement App Service ILB ».  
Azure Application Gateway est une appliance virtuelle qui fournit l’équilibrage de charge de couche 7, le déchargement SSL et la protection du pare-feu d’applications web (WAF). Elle peut écouter sur une adresse IP publique et acheminer le trafic jusqu’au point de terminaison de votre application. Les informations suivantes expliquent comment intégrer une passerelle d’application configurée avec le WAF à une application dans un environnement App Service ILB.  

L’intégration de la passerelle d’application à l’environnement App Service ILB se fait au niveau de l’application.  Lorsque vous configurez la passerelle d’application avec votre environnement App Service ILB, vous le faites pour des applications spécifiques dans cet environnement. Cela permet l’hébergement d’applications multilocataires sécurisées dans un seul environnement App Service ILB.  

![Passerelle d’application pointant vers une application dans un environnement App Service ILB][1]

Lors de cette procédure pas à pas, vous allez :

* Créer une passerelle d’application
* Configurer la passerelle d’application pour pointer vers une application dans votre environnement App Service ILB
* Configurer votre application pour honorer le nom de domaine personnalisé
* Modifier votre nom d’hôte DNS public qui pointe vers votre passerelle d’application

Pour intégrer votre passerelle d’application à votre environnement App Service iLB, vous avez besoin des éléments suivants :

* Un environnement App Service ILB
* Une application en cours d’exécution dans l’environnement App Service ILB
* Un nom de domaine routable sur internet à utiliser avec votre application dans l’environnement App Service ILB
* L’adresse ILB utilisée par votre environnement App Service ILB (elle se trouve dans le portail de l’environnement App Service ILB sous **Paramètres -> Adresses IP**)

    ![Les adresses IP utilisées par votre environnement App Service ILB][9]
    
* Un nom DNS public à utiliser ultérieurement pour pointer vers votre passerelle d’application 

Pour obtenir plus d’informations sur la création d’un environnement App Service ILB, lisez le document [Création et utilisation d’un environnement App Service ILB][ilbase]

Ce guide présuppose que vous souhaitez disposer d’une passerelle d’application dans le même réseau virtuel Azure que celui où l’environnement App Service ILB est déployé. Avant de démarrer la création de la passerelle d’application, choisissez ou créez un sous-réseau destiné à son hébergement. Vous devez utiliser un autre sous-réseau que GatewaySubnet, ou que celui utilisé par l’environnement App Service ILB.
Si vous placez la passerelle d’application sur GatewaySubnet, il vous est impossible par la suite de créer une passerelle de réseau virtuel. Vous ne pouvez pas la placer non plus sur le sous-réseau utilisé par votre environnement App Service ILB, car l’environnement App Service est la seule chose qui puisse se trouver sur son sous-réseau.

## <a name="steps-to-configure"></a>Étapes de configuration ##

1. Dans le portail Azure, accédez à **Nouveau > Réseau > Application Gateway** 
    1. Procédez de la façon suivante :
        1. Renseignez le nom de la passerelle d’application
        1. Sélectionnez le pare-feu d’applications web
        1. Sélectionnez l’abonnement utilisé pour le réseau virtuel de l’environnement App Service
        1. Créez ou sélectionnez le groupe de ressources
        1. Sélectionnez l’emplacement où se trouve le réseau virtuel de l’environnement App Service

    ![Informations de base indispensables à la création d’une passerelle d’application][2]   
    1. Dans la zone Paramètres :
        1. Définissez le réseau virtuel de l’environnement App Service.
        1. Configurez le sous-réseau dans lequel la passerelle d’application doit être déployée. N’utilisez pas GatewaySubnet, car il empêche la création des passerelles VPN.
        1. Sélectionnez Public.
        1. Sélectionnez une adresse IP publique. Si vous n’en avez pas, créez-en une à ce stade.
        1. Configurez HTTP ou HTTPS. Si vous choisissez le protocole HTTPS, vous devez fournir un certificat PFX.
        1. Sélectionnez les paramètres du pare-feu d’applications web. Ici, vous pouvez activer le pare-feu et le définir également pour la détection ou la prévention, en fonction de vos besoins.

    ![Paramètres de création d’une passerelle d’application][3]
    
    1. Dans la section récapitulative permettant de vérifier vos choix, sélectionnez **OK**. L’installation de votre passerelle d’application peut prendre une trentaine de minutes.  

2. Lorsque cette installation est terminée, accédez à votre portail Application Gateway. Sélectionnez **Pool principal**.  Ajoutez l’adresse ILB pour votre environnement App Service ILB.

    ![Configuration du pool principal][4]

3. Une fois l’opération de configuration de votre pool principal terminée, sélectionnez **Sondes d’intégrité**. Créez une sonde d’intégrité pour le nom de domaine que vous souhaitez destiner à votre application. 

    ![Configurer les sondes d’intégrité][5]
    
4. Lorsque l’opération de configuration de vos sondes d’intégrité est terminée, sélectionnez **Paramètres HTTP**.  Modifiez le paramètre existant à cet endroit, sélectionnez **Utiliser la sonde personnalisée** et choisissez la sonde que vous avez configurée.

    ![Configuration des paramètres HTTP][6]
    
5. Accédez à la **Vue d’ensemble** de la passerelle Application Gateway et copiez l’adresse IP publique utilisée pour votre passerelle d’application.  Définissez cette adresse IP en tant qu’enregistrement A pour le nom de domaine de votre application, ou utilisez le nom DNS pour cette adresse dans un enregistrement CNAME.  Il est plus facile de sélectionner l’adresse IP publique pour la copier à partir de l’interface utilisateur de l’adresse IP publique que de la copier à partir du lien présent dans la section Vue d’ensemble de la passerelle Application Gateway. 

    ![Portail Application Gateway][7]

6. Définissez le nom de domaine personnalisé pour votre application dans l’environnement App Service ILB.  Accédez à votre application dans le portail et, sous Paramètres, sélectionnez **Domaines personnalisés**.

![Définition d’un nom de domaine personnalisé sur l’application][8]

Des informations sur la définition des noms de domaine personnalisés pour vos applications web sont disponibles ici [Définition de noms de domaine personnalisés pour votre application web][custom-domain]. La différence entre une application d’un environnement App Service ILB et ce document réside dans le fait qu’il n’y a aucune validation sur le nom de domaine.  Étant donné que vous possédez le DNS qui gère les points de terminaison de l’application, vous pouvez y placer tout ce que vous voulez. Le nom de domaine personnalisé que vous ajoutez dans ce cas ne doit pas nécessairement être dans votre système DNS, mais il a toujours besoin d’être configuré avec votre application. 

Dès lors que l’opération de configuration est terminée et que vous avez autorisé un court laps de temps pour que les modifications DNS se propagent, vous pouvez accéder à votre application par le nom de domaine personnalisé que vous avez créé. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
