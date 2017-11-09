---
title: Installer un certificat client P2S | Azure
description: "Cet article vous aide à installer un certificat client pour une connexion P2S avec authentification par certificat."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2017
ms.author: cherylmc
ms.openlocfilehash: fc0cc37794ef291c9d27b094211b38cec90da55c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Installer un certificat client pour des connexions point à site avec authentification par certificat Azure

Tous les clients qui se connectent à un réseau virtuel à l’aide d’une connexion point à site avec authentification par certificat Azure nécessitent un certificat client. Cet article vous aide à installer un certificat client utilisé pour l’authentification à la connexion à réseau virtuel via une connexion P2S.

## <a name="generate"></a>Générer et exporter un certificat client

Vous pouvez générer un certificat client à partir d’un certificat racine généré à l’aide d’une solution d’autorité de certification d’entreprise, ou à partir d’un certificat racine auto-signé. Consultez les instructions [PowerShell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) pour continuer. Après avoir généré les certificats client, exportez-les en tant que fichiers .pfx. Veillez à inclure l’ensemble de la chaîne du certificat lors de l’exportation.

## <a name="installwin"></a>Installer un certificat sur des clients Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Installer un certificat sur des clients Mac

Les clients VPN Mac sont pris en charge avec le modèle de déploiement de Resource Manager seulement. Ils ne sont pas pris en charge avec le modèle de déploiement Classic.

> [!NOTE]
>  IKEv2 est actuellement en préversion.
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Poursuivez votre configuration point à site.

* [Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Portail Azure (classique)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)