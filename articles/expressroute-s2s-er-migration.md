<properties
   pageTitle="Migration de votre VPN de site à site vers ExpressRoute"
   description="Cet article va vous aider à migrer votre VPN de site à site vers ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />
   
# Migration de la connexion : d’un VPN de site à site à ExpressRoute

Les clients Azure peuvent se connecter à leur réseau virtuel de deux manières : via un VPN de site à site ou via ExpressRoute. La connexion VPN de site à site passe par l’Internet public. IPsec permet de chiffrer le trafic réseau. ExpressRoute est une connexion privée à Azure. Vous pouvez vous connecter à Azure via un [fournisseur Exchange](expressroute-exchange-providers.md) ou un [fournisseur de services réseau](expressroute-network-service-providers.md).

Si vous avez déjà une connexion VPN de site à site à votre réseau virtuel, vous pouvez suivre les étapes ci-dessous pour passer à une connexion via ExpressRoute.

1) Connectez-vous au portail Azure

2) Dans le volet de navigation, cliquez sur **RÉSEAUX**, puis sur le réseau virtuel associé à une connexion VPN

3) Cliquez sur **TABLEAU DE BORD**. En bas de la page, cliquez sur **SUPPRIMER LA PASSERELLE**, puis sur **OUI**.

  Cette étape permet de supprimer la passerelle VPN de site à site.

4) Lorsque le portail affiche « LA PASSERELLE N’A PAS ÉTÉ CRÉÉE » pour le réseau virtuel, cliquez sur la page **Configurer**.

a) Activez la case à cocher « Utiliser ExpressRoute »

b) Définissez le CIDR du sous-réseau de passerelle sur /28

![Partage d’abonnement](./media/expressroute-s2s-er-migration/expressroute-s2s-er.png)

5) En bas de la page, cliquez sur **ENREGISTRER**, puis sur **OUI**.

6) Cliquez sur **TABLEAU DE BORD**. En bas de la page, cliquez sur **CRÉER LA PASSERELLE**, puis sur **OUI**.

 Cette étape permet de créer la passerelle ExpressRoute
 
Une fois la passerelle créée, le réseau virtuel peut être connecté à un circuit ExpressRoute. Toutes les machines virtuelles du réseau virtuel sont opérationnelles tout au long du processus. Il est inutile de les déplacer ou de les arrêter.

Passez à la dernière étape du didacticiel sur la configuration d’ExpressRoute via des [fournisseurs Exchange](expressroute-configuring-exps.md)/[fournisseurs de services réseau](expressroute-configuring-nsps.md) pour lier votre réseau virtuel à un circuit ExpressRoute.



<!--HONumber=54-->