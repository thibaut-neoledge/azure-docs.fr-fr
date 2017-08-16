---
title: "Configurer un pare-feu d’applications web : passerelle Application Gateway Azure | Microsoft Docs"
description: "Cet article explique comment utiliser un pare-feu d’applications web sur une passerelle d’application nouvelle ou existante."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ac6c629ceaf1a8036643f593ce3d7ef9ea096ef8
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Configurer un pare-feu d’application web sur une passerelle Application Gateway nouvelle ou existante avec Azure CLI

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Interface de ligne de commande Azure](application-gateway-web-application-firewall-cli.md)

Découvrez comment créer une passerelle d’application avec un pare-feu d’applications web activé ou comment ajouter des pare-feu d’applications web à une passerelle d’application existante.

Le pare-feu d’applications web (WAF, Web Application Firewall) d’Azure Application Gateway protège les applications web des attaques basées sur le web courantes comme l’injection de code SQL, les attaques de script de site à site et les piratages de session.

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. La passerelle d’application offre de nombreuses fonctionnalités du contrôleur de livraison d’applications (ADC) : équilibrage de charge HTTP, affinité de session basée sur les cookies, déchargement de protocole SSL, sondes d’intégrité personnalisées, prise en charge multisite, etc. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez : [Vue d’ensemble de la passerelle Application Gateway](application-gateway-introduction.md).

L’article suivant montre comment [ajouter un pare-feu d’applications web à une passerelle d’application existante](#add-web-application-firewall-to-an-existing-application-gateway) et [créer une passerelle d’application qui utilise le pare-feu d’applications web](#create-an-application-gateway-with-web-application-firewall).

![image du scénario][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Condition préalable : installer Azure CLI 2.0

Pour exécuter la procédure indiquée dans cet article, vous devez [installer l’interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>Différences de configuration WAF

Si vous avez lu la rubrique [Create an Application Gateway with Azure CLI](application-gateway-create-gateway-cli.md) (Créer une passerelle Application Gateway avec Azure CLI), vous connaissez les paramètres de référence SKU à configurer lors de la création d’une passerelle d’application. WAF inclut des paramètres supplémentaires à définir lors de la configuration de la référence SKU sur une passerelle d’application. Aucune autre modification n’est nécessaire sur la passerelle d’application elle-même.

| **Paramètre** | **Détails**
|---|---|
|**Référence (SKU)** |Une passerelle d’application normale sans WAF prend en charge les tailles **Standard\_Small**, **Standard\_Medium** et **Standard\_Large**. Avec l’introduction de WAF, deux autres SKU sont disponibles : **WAF\_Medium** et **WAF\_Large**. WAF n’est pas pris en charge sur les petites passerelles d’application.|
|**Mode** | Ce paramètre indique le mode de WAF. Les valeurs autorisées sont **Détection** et **Prévention**. Lorsque WAF est configuré en mode de détection, toutes les menaces sont stockées dans un fichier journal. En mode de prévention, les événements sont toujours consignés, mais l’attaquant reçoit une erreur d’autorisation de type 403 de la part de la passerelle d’application.|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Ajout d’un pare-feu d’applications web à une passerelle d’application existante

La commande suivante transforme une passerelle d’application standard en passerelle d’application avec WAF activé.

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Cette commande met à jour la passerelle d’application avec le pare-feu d’applications web. Consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md) pour comprendre comment afficher les journaux de votre passerelle d’application. En raison des critères de sécurité inhérents à WAF, les journaux doivent être régulièrement examinés pour comprendre la politique de sécurité appliquée à vos applications web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Créer une passerelle Application Gateway avec le pare-feu d’applications web

La commande suivante permet de créer une passerelle Application Gateway avec le pare-feu d’applications web.

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> Les passerelles Application Gateway créées avec la configuration de pare-feu d’application web de base sont définies avec la solution CRS 3.0 dédiée aux protections.

## <a name="get-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle Application Gateway

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle d’application, un enregistrement CNAME peut être utilisé pour pointer vers le point de terminaison public de la passerelle d’application. [Configuration d’un nom de domaine personnalisé pour Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Pour configurer un enregistrement CNAME, récupérez les détails de la passerelle d’application et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle d’application. Le nom DNS de la passerelle Application Gateway doit être utilisé pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment personnaliser les règles WAF en consultant [Personnaliser les règles de pare-feu d’applications web par le biais d’Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png

