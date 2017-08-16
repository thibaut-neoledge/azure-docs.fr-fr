---
title: "Créer ou mettre à jour une passerelle Application Gateway Azure avec le pare-feu d’applications web | Microsoft Docs"
description: "Apprenez à créer une passerelle Application Gateway avec le pare-feu d’applications web à l’aide du portail"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 650f26d19615d27a94f3947aad7b7904b6c1fabc
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Création d’une passerelle d’application avec le pare-feu d’applications web à l’aide du portail

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Interface de ligne de commande Azure](application-gateway-web-application-firewall-cli.md)

Découvrez comment créer une passerelle d’application avec le pare-feu d’applications web activé.

Le pare-feu d’applications web (WAF, Web Application Firewall) d’Azure Application Gateway protège les applications web des attaques basées sur le web courantes comme l’injection de code SQL, les attaques de script de site à site et les piratages de session. L’application web protège contre la plupart des 10 plus courantes vulnérabilités web de l’OWASP.

## <a name="scenarios"></a>Scénarios

Dans cet article, il existe deux scénarios :

Dans le premier scénario, vous découvrez comment [créer une passerelle d’application avec le pare-feu d’applications web](#create-an-application-gateway-with-web-application-firewall)

Dans le second scénario, vous découvrez comment [ajouter des pare-feu d’applications web à une passerelle d’application existante](#add-web-application-firewall-to-an-existing-application-gateway).

![Exemple de scénario][scenario]

> [!NOTE]
> La configuration supplémentaire de la passerelle Application Gateway, y compris les sondes d’intégrité personnalisées, les adresses de pool principal et les règles supplémentaires sont configurées après avoir configuré la passerelle Application Gateway et non lors du déploiement initial.

## <a name="before-you-begin"></a>Avant de commencer

La passerelle Application Gateway Azure requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace d’adresse pour disposer de plusieurs sous-réseaux. Une fois que vous avez déployé une passerelle Application Gateway sur un sous-réseau, seules les passerelles Application Gateway supplémentaires peuvent être ajoutées au sous-réseau.

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a> Ajout d’un pare-feu d’applications web à une passerelle Application Gateway existante

Cet exemple met à jour une passerelle d’application existante afin de prendre en charge des pare-feu d’applications web en mode de prévention.

1. Allez dans le panneau **Favoris** du portail Azure, puis cliquez sur **Toutes les ressources**. Cliquez sur la passerelle d’application existante dans le panneau **Toutes les ressources**. Si l’abonnement déjà sélectionné a plusieurs ressources, vous pouvez entrer le nom dans **Filtrer par nom...** pour accéder facilement à la zone DNS.

   ![Création d’une passerelle Application Gateway][1]

1. Cliquez sur **Pare-feu d’applications web** et mettez à jour les paramètres de la passerelle Application Gateway. Lorsque vous avez terminé, cliquez sur **Enregistrer**

    Les paramètres permettant de mettre à jour une passerelle d’application existante afin de prendre en charge des pare-feu d’applications web sont les suivants :

   | **Paramètre** | **Valeur** | **Détails**
   |---|---|---|
   |**Mise à niveau vers le niveau WAF**| Activé | Ceci définit le niveau de la passerelle d’application au niveau WAF.|
   |**État du pare-feu**| Activé | Ce paramètre active le pare-feu sur le WAF.|
   |**Mode du pare-feu** | Prévention | Ce paramètre spécifique comment le pare-feu d’application web traite le trafic malveillant. Le mode de **Détection** consigne uniquement les événements, tandis que le mode de **Prévention** consigne les événements et bloque le trafic malveillant.|
   |**Ensemble de règles**|3.0|Ce paramètre détermine l’[ensemble de règles principal](application-gateway-web-application-firewall-overview.md#core-rule-sets) qui est utilisé pour protéger les membres du pool principal.|
   |**Configurer des règles désactivées**|varie|Pour éviter les faux positifs potentiels, ce paramètre vous permet de désactiver certaines [règles et certains groupes de règles](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > Lors de la mise à niveau d’une passerelle Application Gateway existante vers la référence SKU WAF, la taille de la référence (SKU) devient **moyen**. Cela peut être reconfiguré une fois la configuration terminée.

    ![panneau montrant les paramètres de base][2-1]

    > [!NOTE]
    > Pour afficher les journaux de pare-feu d’applications web, les diagnostics doivent être activés et ApplicationGatewayFirewallLog doit être sélectionné. Vous pouvez choisir un nombre d’instances de 1 à des fins de test. Il est important de savoir que n’importe quel nombre d’instances inférieur à 2 n’est pas couvert par le contrat SLA et n’est donc pas recommandé. Les petites passerelles ne sont pas disponibles lorsque vous utilisez des pare-feu d’applications web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>créer une passerelle d’application avec le pare-feu d’applications web

Ce scénario va :

* créer une passerelle Application Gateway moyenne avec deux instances ;
* créer un réseau virtuel nommé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16 ;
* créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme bloc CIDR ;
* configurer un certificat pour le déchargement SSL.

1. Connectez-vous au [portail Azure](https://portal.azure.com). Si vous ne possédez pas encore de compte, vous pouvez [vous inscrire pour bénéficier d’un essai gratuit d’un mois](https://azure.microsoft.com/free).
1. Dans le volet Favoris, cliquez sur **Nouveau**.
1. Dans le panneau **Nouveau**, cliquez sur **Mise en réseau**. Dans le panneau **Mise en réseau**, cliquez sur **Application Gateway**, comme indiqué dans l’image suivante :
1. Accédez au portail Azure, cliquez sur **Nouveau** > **Mise en réseau** > **Application Gateway**

    ![Création d’une passerelle Application Gateway][1]

1. Dans le panneau **De base** qui s’affiche, entrez les valeurs suivantes et cliquez sur **OK** :

   | **Paramètre** | **Valeur** | **Détails**
   |---|---|---|
   |**Nom**|AdatumAppGateway|Nom de la passerelle Application Gateway|
   |**Niveau**|WAF|Les valeurs disponibles sont Standard et WAF. Consultez la page [Pare-feu d’applications web (WAF)](application-gateway-web-application-firewall-overview.md) pour en savoir plus sur WAF.|
   |**Taille de la référence (SKU)**|Moyenne|Si vous sélectionnez le niveau Standard, vous avez le choix entre Petite, Moyenne et Grande. Si vous choisissez le niveau WAF, les options sont limitées à Moyenne et Grande.|
   |**Nombre d’instances**|2|Nombre d’instances de la passerelle Application Gateway pour la haute disponibilité. Un nombre d’instances de 1 doit être utilisé uniquement à des fins de test.|
   |**Abonnement**|[Votre abonnement]|Sélectionnez l’abonnement dans lequel créer la passerelle de l’application.|
   |**Groupe de ressources**|**Créer un nouveau :** AdatumAppGatewayRG|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Emplacement**|Ouest des États-Unis||

   ![panneau montrant les paramètres de base][2-2]

1. Dans le panneau **Paramètres** qui s’affiche sous **Réseau virtuel**, cliquez sur **Choisir un réseau virtuel**. Vous accédez alors au panneau **Choisir un réseau virtuel** .  Cliquez sur **Créer un nouveau** pour ouvrir le panneau **Créer un réseau virtuel**.

   ![choisir un réseau virtuel][2]

1. Dans le panneau **Créer un réseau virtuel**, entrez les valeurs suivantes, puis cliquez sur **OK**. Cette étape ferme les panneaux **Créer un réseau virtuel** et **Choisir un réseau virtuel** . Le champ **Sous-réseau** sur le panneau **Paramètres** est ainsi rempli avec le sous-réseau sélectionné.

   |**Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Nom**|AdatumAppGatewayVNET|Nom de la passerelle Application Gateway|
   |**Espace d’adressage**|10.0.0.0/16| Cette valeur correspond à l’espace d’adressage du réseau virtuel|
   |**Nom du sous-réseau**|AppGatewaySubnet|Nom du sous-réseau de la passerelle Application Gateway|
   |**Plage d’adresses de sous-réseau**|10.0.0.0/28 | Ce sous-réseau autorise plusieurs sous-réseaux supplémentaires dans le réseau virtuel pour les membres du pool principal|

1. Dans le panneau **Paramètres**, sous **Configuration d’adresse IP frontale**, choisissez **Public** comme **Type d’adresse IP**

1. Dans le panneau **Paramètres**, sous **Adresse IP publique**, cliquez sur **Choisir une adresse IP publique** pour accéder au panneau **Choisir une adresse IP publique**, puis cliquez sur **Créer**.

   ![choisir une adresse ip publique][3]

1. Dans le panneau **Créer une adresse IP publique**, acceptez la valeur par défaut et cliquez sur **OK**. Cette étape ferme les panneaux **Choisir une adresse IP publique** et **Créer une adresse IP publique** et remplit **Adresse IP publique** avec l’adresse IP publique choisie.

1. Dans le panneau **Paramètres**, sous **Configuration de l’écouteur**, cliquez sur **HTTP** sous **Protocole**. Un certificat est requis pour utiliser **https**. La clé privée du certificat est nécessaire de sorte qu’un export .pfx du certificat soit requis, ainsi que le mot de passer du fichier.

1. Configurez les paramètres **WAF** spécifiques.

   |**Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**État du pare-feu**| Activé| Ce paramètre active ou désactive WAF.|
   |**Mode du pare-feu** | Prévention| Ce paramètre détermine les actions effectuées par WAF concernant le trafic malveillant. Si le mode de **détection** est choisi, le trafic est uniquement consigné.  Si le mode de **prévention** est choisi, le trafic est consigné et bloqué avec une réponse d’erreur d’autorisation 403.|


1. Consultez la page Résumé et cliquez sur **OK**.  La passerelle Application Gateway est maintenant mise en file d’attente et créée.

1. Une fois la passerelle Application Gateway créée, accédez à celle-ci dans le portail pour poursuivre sa configuration.

    ![Vue des ressources de la passerelle Application Gateway][10]

Ces étapes permettent de créer une passerelle Application Gateway de base avec les paramètres par défaut pour l’écouteur, le pool backend, les paramètres http backend et les règles. Vous pouvez modifier ces paramètres en fonction de votre déploiement une fois l’approvisionnement réussi

> [!NOTE]
> Les passerelles Application Gateway créées avec la configuration de pare-feu d’application web de base sont définies avec la solution CRS 3.0 dédiée aux protections.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez à présent apprendre à configurer un alias de domaine personnalisé pour [l’adresse IP publique](../dns/dns-custom-domain.md#public-ip-address) à l’aide de DNS Azure ou d’un autre fournisseur DNS.

Apprenez à configurer la journalisation des diagnostics, à consigner les événements détectés ou bloqués par le pare-feu Application Gateway web en consultant la rubrique [Diagnostics de la passerelle Application Gateway](application-gateway-diagnostics.md)

Apprenez à créer des sondes d’intégrité personnalisées en vous rendant sur [Créer une sonde d’intégrité personnalisée](application-gateway-create-probe-portal.md)

Découvrez comment configurer le déchargement SSL et éviter à vos serveurs web le déchiffrement SSL coûteux en vous rendant sur [Configurer le déchargement SSL](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

