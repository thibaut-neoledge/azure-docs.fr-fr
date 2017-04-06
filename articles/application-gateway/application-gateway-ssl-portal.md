---
title: "Configurer le déchargement SSL - Passerelle Azure Application Gateway - Portail Azure | Microsoft Docs"
description: "Cette page fournit des instructions pour la création d’une passerelle Application Gateway pour le déchargement SSL à l’aide du portail"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: a9cb2d921d1be226661311d91367b2b6f44fa0dc
ms.lasthandoff: 03/24/2017


---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>Configurer une passerelle d’application pour le déchargement SSL en utilisant le portail

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-ssl-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Azure Classic PowerShell](application-gateway-ssl.md)

Il est possible de configurer Azure Application Gateway de façon à mettre fin à la session SSL (Secure Sockets Layer) sur la passerelle pour éviter les tâches de déchiffrement SSL coûteuses au niveau de la batterie de serveurs web. Le déchargement SSL simplifie aussi la configuration de serveur principal et la gestion de l’application web.

## <a name="scenario"></a>Scénario

Le scénario suivant passe par la configuration du déchargement SSL sur une passerelle d’application existante. Le scénario suppose que vous avez déjà suivi la procédure de [Création d’une passerelle Application Gateway](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer le déchargement SSL avec une passerelle Application Gateway, un certificat est requis. Ce certificat est chargé sur la passerelle d’application et utilisé pour chiffrer et déchiffrer le trafic envoyé via SSL. Le certificat doit être partagé au format Personal Information Exchange (.pfx). Ce format de fichier permet d’exporter la clé privée requise par la passerelle d’application pour effectuer le chiffrement et le déchiffrement du trafic.

## <a name="add-an-https-listener"></a>Ajouter un écouteur HTTPS

L’écouteur HTTPS recherche le trafic en fonction de sa configuration et aide à acheminer le trafic vers les pools principaux.

### <a name="step-1"></a>Étape 1

Accédez au portail Azure et sélectionnez une passerelle d’application existante.

### <a name="step-2"></a>Étape 2

Cliquez sur Écouteurs et cliquez sur le bouton Ajouter pour ajouter un écouteur.

![Panneau de vue d’ensemble de passerelle d’application][1]

### <a name="step-3"></a>Étape 3 :

Remplissez les informations requises pour l’écouteur, et téléchargez le certificat .pfx. Lorsque vous avez terminé, cliquez sur OK.

**Nom** - il s’agit d’un nom convivial pour l’écouteur.

**Configuration IP frontale** - il s’agit de la configuration d’IP frontale utilisée pour l’écouteur.

**Port frontal (nom/port)** - un nom convivial pour le port utilisé sur le serveur frontal de la passerelle Application Gateway et le port utilisé.

**Protocole** - un commutateur qui détermine si https ou http est utilisé pour le serveur frontal.

**Certificat (nom/mot de passe)** - si le déchargement SSL est utilisé, un certificat .pfx est requis pour ce paramètre, et un nom convivial et un mot de passe sont requis.

![ajouter panneau d’écouteur][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Créer une règle et l’associer à l’écouteur

L’écouteur a été créé. Il est temps de créer une règle pour gérer le trafic de l’écouteur. Les règles définissent la façon dont le trafic est acheminé aux pools principaux en fonction de plusieurs paramètres de configuration, dont l’utilisation ou non de l’affinité de session basée sur les cookies, le protocole, le port et les sondes d’intégrité.

### <a name="step-1"></a>Étape 1

Cliquez sur **Règles** dans la passerelle Application Gateway, puis cliquez sur Ajouter.

![Panneau de règles Application Gateway][3]

### <a name="step-2"></a>Étape 2

Sur le panneau **Ajouter une règle de base** , tapez le nom convivial de la règle et choisissez l’écouteur créé à l’étape précédente. Choisissez le pool principal approprié et la configuration http souhaitée, puis cliquez sur **OK**

![fenêtre de paramètres https][4]

Les paramètres sont maintenant enregistrés pour la passerelle Application Gateway. Le processus d’enregistrement pour ces paramètres peut prendre un certain temps avant leur disponibilité sur le portail ou via PowerShell. Une fois enregistrée, la passerelle Application Gateway gère le chiffrement et le déchiffrement du trafic. Tout le trafic entre la passerelle Application Gateway et les serveurs web principaux est géré via http. Toute communication lancée vers le client via le protocole https sera renvoyée chiffrée au client.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une sonde d’intégrité personnalisée avec Azure Application Gateway, consultez [Créer une sonde d’intégrité personnalisée](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

