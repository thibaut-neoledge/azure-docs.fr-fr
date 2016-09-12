<properties
   pageTitle="Créer une passerelle Application Gateway à l’aide de l’interface de ligne de commande Azure de Resource Manager | Microsoft Azure"
   description="Découvrez comment créer une passerelle Application Gateway à l’aide de l’interface de ligne de commande Azure dans Resource Manager"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="gwallace" />

# Créer une passerelle Application Gateway à l’aide de l’interface de ligne de commande Azure

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Une passerelle Application Gateway offre les fonctionnalités de livraison d’applications suivantes : équilibrage de charge HTTP, affinité de session basée sur les cookies et déchargement SSL (Secure Sockets Layer), sondes d’intégrité personnalisées et prise en charge de plusieurs sites.

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-gateway-portal.md)
- [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Modèle Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
- [Interface de ligne de commande Azure](application-gateway-create-gateway-cli.md)

<BR>

## Condition préalable : installer l’interface de ligne de commande Azure

Pour effectuer les étapes indiquées dans cet article, vous devez [installer l’interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI)](../xplat-cli-install.md) et [ouvrir une session sur Azure](../xplat-cli-connect.md).

> [AZURE.NOTE] Si vous n’avez pas de compte Azure, vous devez vous en procurer un. Inscrivez-vous à un [essai gratuit ici](../active-directory/sign-up-organization.md).

## Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle Application Gateway à l’aide du portail Azure.

Ce scénario va :

- créer une passerelle Application Gateway moyenne avec deux instances ;
- créer un réseau virtuel nommé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16 ;
- créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme bloc CIDR ;
- configurer un certificat pour le déchargement SSL.

![Exemple de scénario][scenario]

>[AZURE.NOTE] La configuration supplémentaire de la passerelle Application Gateway, y compris les sondes d’intégrité personnalisées, les adresses de pool principal et les règles supplémentaires sont configurées après avoir configuré la passerelle Application Gateway et non lors du déploiement initial.

## Avant de commencer

La passerelle Application Gateway Azure requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace d’adresse pour disposer de plusieurs sous-réseaux. Une fois que vous avez déployé une passerelle Application Gateway sur un sous-réseau, seules les passerelles Application Gateway supplémentaires peuvent être ajoutées au sous-réseau.

## Créer le groupe de ressources

Avant de créer la passerelle Application Gateway, un groupe de ressources est créé pour contenir cette dernière. Ce qui suit montre la commande et la sortie attendue.

    azure group create -n AdatumAppGatewayRG -l eastus

    info:    Executing command group create
    + Getting resource group AdatumAppGatewayRG
    + Creating resource group AdatumAppGatewayRG
    info:    Created resource group AdatumAppGatewayRG
    data:    Id:                  /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG
    data:    Name:                AdatumAppGatewayRG
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

## Créez un réseau virtuel

Une fois le groupe de ressources créé, un réseau virtuel est créé pour la passerelle Application Gateway. Dans l’exemple suivant, l’espace d’adressage était 10.0.0.0/16 tel que défini dans les notes du scénario précédent.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

    info:    Executing command network vnet create
    + Looking up the virtual network "AdatumAppGatewayVNET"
    + Creating virtual network "AdatumAppGatewayVNET"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET
    data:    Name                            : AdatumAppGatewayVNET
    data:    Type                            : Microsoft.Network/virtualNetworks
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Address prefixes:
    data:      10.0.0.0/16
    info:    network vnet create command OK

## Créer un sous-réseau

Une fois le réseau virtuel créé, un sous-réseau est ajouté pour la passerelle Application Gateway. Si vous envisagez d’utiliser la passerelle d’application avec une application web hébergée dans le même réseau virtuel que la passerelle Application Gateway, veillez à laisser suffisamment de place pour un autre sous-réseau.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

    info:    Executing command network vnet subnet create
    verbose: Looking up the virtual network "AdatumAppGatewayVNET"
    verbose: Looking up the subnet "Appgatewaysubnet"
    verbose: Creating subnet "Appgatewaysubnet"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet
    data:    Name                            : Appgatewaysubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.0.0.0/28
    info:    network vnet subnet create command OK

## Créer la passerelle Application Gateway

Une fois le réseau virtuel et le sous-réseau créés, les conditions préalables pour la passerelle Application Gateway sont remplies. En outre, un certificat .pfx exporté précédemment et le mot de passe de ce dernier sont requis pour l’étape suivante. Les adresses IP utilisées pour le serveur principal sont celles de votre serveur principal. Il peut s’agir d’adresses IP privées dans le réseau virtuel, d’adresses IP publiques ou de noms de domaine complets pour vos serveurs principaux.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd

    info:    Executing command network application-gateway create
    + Looking up an application gateway "AdatumAppGateway"
    + Looking up the subnet "Appgatewaysubnet"
    warn:    Using default http listener protocol: https
    warn:    Using default gateway ip name: ipConfig01
    warn:    Using default sku name: Standard_Medium
    warn:    Using default sku tier: Standard
    warn:    Using default sku capacity: 2
    warn:    Using default frontend ip name: frontendIp01
    warn:    Using default frontend port name: frontendPort01
    warn:    Using default frontend port: 443
    warn:    Using default address pool name: pool01
    warn:    Using default http settings name: httpSettings01
    warn:    Using default http settings protocol: http
    warn:    Using default http settings port: 80
    warn:    Using default http settings cookie based affinity: Disabled
    warn:    Using default http listener name: listener01
    warn:    Using default request routing rule name: rule01
    warn:    Using default request routing rule type: Basic
    + Looking up the subnet "Appgatewaysubnet"
    + Creating configuration for an application gateway "AdatumAppGateway"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway
    data:    Name                            : AdatumAppGateway
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Sku                             : Standard_Medium
    data:    Resource Group                  : AdatumAppGatewayRG
    data:    Gateway IP configations         : [ipConfig01]
    data:    SSL cerificates                 : [cert01]
    data:    Frontend ip configurations      : [frontendIp01]
    data:    Frontend ports                  : [frontendPort01]
    data:    Backend address pools           : [pool01]
    data:    Backend http settings           : [httpSettings01]
    data:    Http listeners                  : [listener01]
    data:    Request routing rules           : [rule01]
    data:    Probes                          : []
    data:    Url Path Maps                   : []
    data:    GatewayIpConfigurationText      : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/gatewayIPConfigurations/ipConfig01",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "ipConfig01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SslCertificateText              : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01",
            "publicCertData": "MIIDHjCCAgagAwIBAgIQE8ZZuidyb59IscYNJPYTvDANBgkqhkiG9w0BAQsFADA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wHhcNMTYwNzI4MTUyNzIwWhcNMTcwNzI4MDAwMDAwWjA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDC+pkArA1y9C10AOPWISHEThbNqvuRA+MaVDFUOu15NB9M7+0PUx5pyGlbW+1MuXb9acia/KXF9WxpYUMbQt8t8p1S5HsV4oKGdEOpdR0d7dozyPrkNgBgYvqRzDQ6R5VuK/uLq9oWpjPkqNoQeYR1wr7f/SNsIA4YsaDqqAi62ET6cvg1wN/VRXbWyi9wLeon7g6fZiCrFZspTUiSyqrRQx7sO0e/bqV7nKgSWmaqo4jLoUAqJBBCUJryDaTNfkFO4VEdnsQLN+PSGO8HwSZPJOzG1V6+MynmaGCKaTJE2UCxtLIJQHBhmES+X/BoinrIsjNVxsKqWFMv/mV7M2GBAgMBAAGjJDAiMAsGA1UdDwQEAwIEMDATBgNVHSUEDDAKBggrBgEFBQcDATANBgkqhkiG9w0BAQsFAAOCAQEAa2XLrwQJwX2ZmVN0MR+/+jWTED134wgoIKw6Ni30ukF9U936FsuvFcEjPr4vBp82cjnz76BjLNhyw/MxAHP7tTaguxzHgUHP9X9fmtcsLEUD74/D5BPmnpl+4cJ/BZMdyzIsuyyPSsDxkVN/W70ykOVTJeAb1ycwfJCllgLgkZcLVgTcMMAJYSttfWn9e1dhTUIlTIYKzD669emFvdBHi+sdTT1HGrZenpkT5oK+H6/5wIV7/DW+C+pqvXCsK0XSeYWW7KuBk5MpD8829HeCvV0rBSf538nYLwUUVUUMNHuTp5QXzouAtHOWyvo00/xRi+aDeq0NfUvTv2iS2BS/Ow==",
            "provisioningState": "Succeeded",
            "name": "cert01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendIpConfigurationText     : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01",
            "privateIPAddress": "10.0.0.6",
            "privateIPAllocationMethod": "Dynamic",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "frontendIp01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendPortText                : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01",
            "port": 443,
            "provisioningState": "Succeeded",
            "name": "frontendPort01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendAddressPoolText          : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01",
            "backendAddresses": [
                {
                    "ipAddress": "134.170.185.46"
                },
                {
                    "ipAddress": "134.170.188.221"
                },
                {
                    "ipAddress": "134.170.185.50"
                }
            ],
            "provisioningState": "Succeeded",
            "name": "pool01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendHttpSettingsText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01",
            "port": 80,
            "protocol": "Http",
            "cookieBasedAffinity": "Disabled",
            "requestTimeout": 30,
            "provisioningState": "Succeeded",
            "name": "httpSettings01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    HttpListenersText               : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01",
            "frontendIPConfiguration": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01"
            },
            "frontendPort": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01"
            },
            "protocol": "Https",
            "sslCertificate": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01"
            },
            "requireServerNameIndication": false,
            "provisioningState": "Succeeded",
            "name": "listener01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    RequestRoutingRulesText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/requestRoutingRules/rule01",
            "ruleType": "Basic",
            "backendAddressPool": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01"
            },
            "httpListener": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01"
            },
            "provisioningState": "Succeeded",
            "name": "rule01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SkuText                         : {
        "name": "Standard_Medium",
        "tier": "Standard",
        "capacity": 2
    }
    data:    ProbesText                      : []
    data:    UrlPathMapsText                 : []
    info:    network application-gateway create command OK

Cela crée une passerelle Application Gateway de base avec les paramètres par défaut pour l’écouteur, le pool principal, les paramètres http principaux et les règles. Cela configure également le déchargement SSL. Vous pouvez modifier ces paramètres en fonction de votre déploiement une fois l’approvisionnement réussi. Si vous votre application web est déjà définie avec les adresses IP pour le pool principal défini dans les étapes précédentes, l’équilibrage de charge démarre dès que la passerelle Application Gateway est configurée et lancée.

## Étapes suivantes

Apprenez à créer des sondes d’intégrité personnalisées en vous rendant sur [Créer une sonde d’intégrité personnalisée](application-gateway-create-probe-portal.md)

Découvrez comment configurer le déchargement SSL et éviter à vos serveurs web le déchiffrement SSL coûteux en vous rendant sur [Configurer le déchargement SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png

<!---HONumber=AcomDC_0831_2016-->