<properties 
	pageTitle="Licence du kit de portage du client de diffusion en continu lisse Microsoft®" 
	description="En savoir plus sur la licence du kit de portage Smooth Streaming client Microsoft®." 
	services="media-services" 
	documentationCenter="" 
	authors="xpouyat,vsood" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/11/2016"  
	ms.author="xpouyat"/>

#Licence du kit de portage du client de diffusion en continu lisse Microsoft®

##Vue d'ensemble

Le kit de portage client Smooth Streaming Microsoft (**SSPK** en abrégé) est une implémentation du client Smooth Streaming optimisée pour aider les fabricants de périphériques intégrés, les opérateurs mobiles et du câble des opérateurs, les fournisseurs de services de contenu, les fabricants de combinés, les éditeurs de logiciels indépendants (ISV) et des fournisseurs de solutions servant à créer des produits et des services pour diffuser des contenus adaptatif, dans un format Smooth Streaming. SSPK est une implémentation Smooth Streaming cliente indépendante des périphériques et des plateformes pouvant être transférée par le titulaire de la licence vers n’importe quel périphérique ou plateforme.

Vous trouverez ci-après une architecture de haut niveau, et le package du kit de portage Smooth Streaming IIS est l’implémentation de client Smooth Streaming fournie par Microsoft. Il inclut la logique de base pour une lecture de contenu de Smooth Streaming. Il est ensuite transféré par des partenaires pour un périphérique ou une plate-forme, grâce à l’implémentation des interfaces appropriées.

![SSPK](./media/media-services-sspk/sspk-arch.png)

##Description

SSPK est concédé sous licence selon des conditions représentant une excellente valeur pour l’entreprise. La licence SSPK offre au secteur :

- Source de kit de portage Smooth Streaming en C++ 
  - implémente la fonctionnalité Client Smooth Streaming
  - ajoute une analyse de format, heuristique, une logique de mise en mémoire tampon, etc.
- API d’application joueur 
  -	interfaces de programmation pour une interaction avec une application de lecteur multimédia
- Interface de couche d’abstraction de plateforme (PAL) 
  -	interfaces de programmation pour l‘interaction avec le système d‘exploitation (threads, sockets)
- Interface de couche d’abstraction matérielle 
  -	interfaces de programmation pour l‘interaction avec les décodeurs A/V matériels (décodage, rendu)
- Interface de gestion de droits numériques (Digital Rights Management - DRM) 
  -	interfaces de programmation pour la gestion des DRM via la couche Abstraction DRM (DAL)
  -	Le kit de portage PlayReady Microsoft est fourni séparément, mais s’intègre via cette interface. Pour plus d‘informations sur les licences pour appareil Microsoft PlayReady cliquez [ici](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Échantillons de l’implémentation 
  -	exemple d‘implémentation PAL pour Linux
  -	exemple d‘implémentation HAL pour GStreamer

##Options de licence

Le kit de portage Smooth Streaming client est accessible aux détenteurs de licences sous forme de deux contrats de licence distincts : l’un correspondant au développement des produits intermédiaires Smooth Streaming client, et l’autre pour la distribution de produits Smooth Streaming finaux pour les clients finaux.
 
- Pour les fabricants de puces, les intégrateurs système ou les fournisseurs de logiciels indépendants (ISV) qui ont besoin d‘un kit de portage de code source pour développer des produits intermédiaires, un kit de portage client Smooth Streaming Microsoft, **Licence produit intermédiaire** doit être exécutée.
- Pour les fabricants de périphériques ou les éditeurs de logiciels qui exigent des droits de distribution pour les produits de Smooth Streaming pour client final, c’est le kit de portage client Smooth Streaming **licence du produit Final** qui doit être exécuté.

###Licence de produit intermédiaire pour le kit Microsoft Smooth Streaming Client Porting

Sous cette licence, Microsoft propose un kit de portage Smooth Streaming client et les droits de propriété intellectuelle nécessaires pour développer et distribuer les produits intermédiaires de client de Smooth Streaming pour les autres titulaires de licences de kit de portage Smooth Streaming client les produits finaux client de Smooth Streaming.

####Structure des frais

Des frais de licence de 50 000 dollars US payables en une fois offrent un accès au kit de portage Smooth Streaming client.

###Licence de produit final pour le kit de portage Smooth Streaming client

Avec cette licence, Microsoft offre les droits de propriété intellectuelle nécessaires pour recevoir les produits intermédiaires de client de Smooth Streaming de la part d’autres titulaires de licences de kit de portage Smooth Streaming client et distribuer des produits Smooth Streaming à la marque de la société en continu lisse pour les clients finaux.

####Structure des frais

Le produit client final de Smooth Streaming est proposé selon un modèle soumis à redevance :

- 0,10 $ par implémentation de périphérique expédiés
- La redevance est limitée à 50 000 dollars chaque année
- Pas de redevance pour les 10 000 premières implémentations de périphérique chaque année 

##Procédure d’achat de licence et accès à SSPK

Veuillez envoyer un e-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) pour toute requête relative aux licences des requêtes.

Le [portail de distribution SSPK Distribution](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) est accessible aux détenteurs de licences intermédiaires enregistrés.

Les titulaires de licence SSPK intermédiaire ou finale peuvent soumettre des questions techniques à [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

##Les titulaires de licence d’accord de produit intermédiaire de client de diffusion lisse Microsoft

- Adroit Business Solutions, Inc
- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Alticast Corporation
- Amazon Digital Services, Inc.
- Amlogic, Co., Ltd.
- AVC Multimedia Software Co., Ltd.
- EchoStar Purchasing Corporation
- Enseo, Inc.
- Fluendo S.A.
- Guangdong OPPO Mobile Telecommunications Corp., Ltd.
- HANDAN BroadInfoCom Co., Ltd.
- Infomir GMBH
- Inside Secure
- Irdeto USA Inc.
- Liberty Global Services BV
- MediaTek Inc.
- MStar Co, Ltd
- Nintendo Co., Ltd.
- OpenTV, Inc.
- Research In Motion Limited
- Saffron Digital Limited
- Sichuan Changhong Electric Co., Ltd
- SoftAtHome
- Tatung Technology Inc.
- Vestel Elektronik Sanayi ve Ticaret A.S.
- VisualOn, Inc.
- ZTE Corporation

##Titulaires de licence d’accord de produit final de client de diffusion lisse Microsoft

- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Amazon Digital Services, Inc.
- AmTRAN Technology Co., Ltd.
- Arcadyan Technology Corporation
- ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
- British Sky Broadcasting Limited
- CastPal Technology Inc., Shenzhen
- Compal Electronics, Inc.
- Dongguan Digital AV Technology Corp., Ltd.
- EchoStar Purchasing Corporation
- Enseo, Inc.
- Filmflex Movies Limited
- Fluendo S.A.
- Gibson Innovations Limited
- Guangdong OPPO Mobile Telecommunications Corp., Ltd.
- HANDAN BroadInfoCom Co., Ltd.
- Hisense International Co., Ltd
- Homecast Co.,Ltd
- Hon Hai Precision Industry Co., Ltd.
- Infomir GMBH
- Kaonmedia Co., Ltd.
- KDDI Corporation
- Nintendo Co., Ltd.
- Orange SA
- PIXELA Corporation
- Saffron Digital Limited
- Sagemcom Broadband SAS
- Sharp Corporation
- Shenzhen Coship Electronics CO., LTD
- Shenzhen Jiuzhou Electric Co., Ltd
- Shenzhen Skyworth Digital Technology Co., Ltd
- Sichuan Changhong Electric Co., Ltd.
- Sky Deutschland Fernsehen GmbH & Co. KG
- SmarDTV S.A.
- SoftAtHome
- TCL Overseas Marketing (Macao Commercial Offshore) Limited
- Technicolor Delivery Technologies, SAS
- Toshiba Lifestyle Products & Services Corporation
- Virgin Media Limited
- VIZIO, Inc.
- Wistron Corporation
- ZTE Corporation

##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0413_2016-->