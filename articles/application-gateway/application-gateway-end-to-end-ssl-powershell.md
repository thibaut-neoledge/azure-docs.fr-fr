---
title: "Configuration de la stratégie SSL et du chiffrement SSL de bout en bout avec la passerelle Application Gateway | Microsoft Docs"
description: "Cet article explique comment configurer le chiffrement SSL de bout en bout avec la passerelle Application Gateway à l’aide d’Azure Resource Manager PowerShell"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: e6d80a33-4047-4538-8c83-e88876c8834e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: ee8cfffdbf054b4251ed269745f6b9ee5a5e6c64
ms.openlocfilehash: 6f061810b865e7855877bf08b02e758351f3e63c


---
# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configuration de la stratégie SSL et du chiffrement SSL de bout en bout avec la passerelle Application Gateway à l’aide de PowerShell

## <a name="overview"></a>Vue d'ensemble

La passerelle Application Gateway prend en charge le chiffrement de bout en bout du trafic. Pour cela, la passerelle Application Gateway arrête la connexion SSL au niveau de la passerelle d’application. La passerelle applique ensuite les règles de routage au trafic, chiffre à nouveau le paquet puis transfère le paquet au serveur principal approprié selon les règles de routage définies. Toute réponse du serveur web passe par le même processus vers l’utilisateur final.

La désactivation de certaines versions du protocole SSL est une autre fonctionnalité prise en charge par la passerelle Application Gateway. La passerelle Application Gateway prend en charge la désactivation des versions de protocole suivantes : **TLSv1.0**, **TLSv1.1** et **TLSv1.2**.

> [!NOTE]
> SSL 2.0 et SSL 3.0 sont désactivés par défaut et ne peuvent pas être activés. Considérés comme non sécurisés, ils ne peuvent pas être utilisés avec Application Gateway
> 
> 

![image du scénario][scenario]

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle d’application en utilisant un chiffrement SSL de bout en bout avec PowerShell.

Ce scénario va :

* créer un groupe de ressources nommé **appgw-rg** ;
* créer un réseau virtuel nommé **appgwvnet** avec un bloc CIDR réservé de 10.0.0.0/16 ;
* créer deux sous-réseaux appelés **appgwsubnet** et **appsubnet** ;
* créer une petite passerelle d’application prenant en charge le chiffrement SSL de bout en bout qui désactive certains protocoles SSL.

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer un chiffrement SSL de bout en bout avec une passerelle d’application, un certificat est requis pour la passerelle et des certificats sont requis pour les serveurs principaux. Le certificat de la passerelle sert à chiffrer et à déchiffrer le trafic envoyé à l’aide de SSL. Le certificat de passerelle doit être partagé au format Personal Information Exchange (.pfx). Ce format de fichier permet d’exporter la clé privée requise par la passerelle d’application pour effectuer le chiffrement et le déchiffrement du trafic.

Pour le chiffrement SSL de bout en bout, le serveur principal doit figurer sur la liste approuvée par la passerelle d’application. Pour cela, vous devez charger le certificat public des serveurs principaux sur la passerelle d’application. Ainsi, la passerelle d’application communique uniquement avec des instances de serveur principal connues, ce qui sécurise la communication de bout en bout.

Ce processus est décrit dans les étapes suivantes :

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Cette section vous guide lors de la création d’un groupe de ressources contenant la passerelle d’application.

### <a name="step-1"></a>Étape 1 :

Connectez-vous à votre compte Azure.

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Étape 2 :

Sélectionnez l’abonnement à utiliser pour ce scénario.

```powershell
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>Étape 3

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple suivant crée un réseau virtuel et deux sous-réseaux. Un sous-réseau sert à héberger la passerelle d’application. Le second sous-réseau est utilisé pour les serveurs principaux hébergeant l’application web.

### <a name="step-1"></a>Étape 1

Affectez une plage d’adresses au sous-réseau utilisé pour la passerelle Application Gateway elle-même.

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> Les sous-réseaux configurés pour la passerelle d’application doivent être correctement dimensionnés. Une passerelle d’application peut être configurée pour 10 instances maximum. Chaque instance prend 1 adresse IP du sous-réseau. Un sous-réseau trop petit peut nuire à la montée en charge d’une passerelle d’application.
> 
> 

### <a name="step-2"></a>Étape 2

Affectez une plage d’adresses au pool d’adresses principales.

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-3"></a>Étape 3 :

Créez un réseau virtuel avec les sous-réseaux définis dans les étapes précédentes.

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-4"></a>Étape 4

Récupérez les ressources de réseau virtuel et les ressources de sous-réseau à utiliser dans les étapes suivantes :

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

Créez une ressource IP publique à utiliser pour la passerelle d’application. Cette adresse IP publique est utilisée dans une prochaine étape.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> La passerelle Application Gateway ne prend pas en charge l’utilisation d’une adresse IP publique créée avec un nom de domaine défini. Seule une adresse IP publique avec un nom de domaine créé dynamiquement est prise en charge. Si vous avez besoin d’un nom DNS convivial pour la passerelle Application Gateway, il est recommandé d’utiliser un enregistrement cname comme alias.
> 
> 

## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration de passerelle Application Gateway

Avant de créer la passerelle d’application, vous devez installer tous les éléments de configuration. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### <a name="step-1"></a>Étape 1 :

Créez une configuration IP de passerelle application : ce paramètre détermine quel sous-réseau utilise la passerelle d’application. Au démarrage, la passerelle Application Gateway sélectionne une adresse IP du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-2"></a>Étape 2 :

Créez une configuration IP frontale : ce paramètre mappe une adresse IP privée ou publique au composant frontal de la passerelle d’application. L’étape suivante associe l’adresse IP publique à l’étape précédente à la configuration IP frontale.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-3"></a>Étape 3

Configurez le pool d’adresses IP principales avec les adresses IP des serveurs web principaux. Il s’agit des adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Remplacez les adresses IP suivantes pour ajouter vos propres points de terminaison d’adresse IP d’application.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

> [!NOTE]
> Un nom de domaine complet (FQDN) est également une valeur valide pour remplacer une adresse IP dans les serveurs principaux à l’aide du commutateur -BackendFqdns.
> 
> 

### <a name="step-4"></a>Étape 4

Configurez le port IP frontal pour le point de terminaison IP public. Ce port est le port auquel les utilisateurs finaux se connectent.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-5"></a>Étape 5

Configurez le certificat pour la passerelle d’application. Ce certificat sert à chiffrer et à chiffrer à nouveau le trafic sur la passerelle d’application.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

> [!NOTE]
> Cet exemple configure le certificat utilisé pour la connexion SSL. Le certificat doit être au format .pfx et le mot de passe contenir entre 4 et 12 caractères.
> 
> 

### <a name="step-6"></a>Étape 6

Créez l’écouteur HTTP pour la passerelle d’application. Affectez la configuration IP frontale, le port et le certificat SSL à utiliser.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-7"></a>Étape 7

Téléchargez le certificat à utiliser sur les ressources du pool principal pour lequel le chiffrement SSL est activé.

> [!NOTE]
> La sonde par défaut obtient la clé publique de la liaison SSL **par défaut** sur l’adresse IP du serveur principal et compare la valeur de clé publique reçue à celle que vous fournissez ici. La clé publique récupérée n’est pas nécessairement le site vers lequel vous souhaitez que trafic soit dirigé **si** vous utilisez des en-têtes d’hôte et SNI sur le serveur principal. En cas de doute, visitez https://127.0.0.1/ sur les serveurs principaux pour confirmer le certificat utilisé pour la liaison SSL **par défaut**. Utilisez la clé publique de cette demande dans cette section. Si vous utilisez des en-têtes d’hôte et SNI sur les liaisons HTTPS et que vous ne recevez pas une réponse et un certificat à partir d’une demande de navigateur manuelle vers https://127.0.0.1/ sur les serveurs principaux, vous devez configurer une liaison SSL par défaut sur les serveurs principaux. Si vous ne le faites pas, les sondes échouent et le serveur principal ne figure pas dans la liste approuvée.
> 
> 

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
```

> [!NOTE]
> Le certificat fourni dans cette étape doit être la clé publique du certificat pfx présent sur le serveur principal. Exportez le certificat (pas le certificat racine) installé sur le serveur principal au format .CER et utilisez-le dans cette étape. Cette étape permet d’ajouter le serveur principal à la liste approuvée par la passerelle d’application.
> 
> 

### <a name="step-8"></a>Étape 8

Configurez les paramètres http principaux de la passerelle d’application. Affectez le certificat téléchargé à l’étape précédente aux paramètres http.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-9"></a>Étape 9

Créez une règle d'acheminement d'équilibrage de charge nommée qui configure le comportement d'équilibrage de charge. Dans cet exemple, une simple règle de type tourniquet (round robin) est créée.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-10"></a>Étape 10

Configurez la taille d'instance de la passerelle Application Gateway.  Les tailles disponibles sont **Standard\_Small**, **Standard\_Medium** et **Standard\_Large**.  Pour la capacité, les valeurs disponibles sont 1 à 10.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> Vous pouvez choisir un nombre d’instances de 1 à des fins de test. Il est important de savoir que n’importe quel nombre d’instances inférieur à 2 n’est pas couvert par le contrat SLA et n’est donc pas recommandé. Les petites passerelles doivent être utilisées pour les tests de développement et non à des fins de production.
> 
> 

### <a name="step-11"></a>Étape 11

Configurez la stratégie SSL à utiliser sur la passerelle Application Gateway. La passerelle Application Gateway prend en charge la possibilité de désactiver certaines versions du protocole SSL.

Les valeurs suivantes représentent une liste des versions de protocole qui peuvent être désactivées.

* **TLSv1_0**
* **TLSv1_1**
* **TLSv1_2**

L’exemple suivant désactive **TLSv1\_0**.

```powershell
$sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

À l’aide des étapes précédentes, créez la passerelle Application Gateway. La création de la passerelle est un processus à long terme.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Désactivation de versions du protocole SSL sur une passerelle Application Gateway existante

Les étapes précédentes vous guident dans la création d’une application en utilisant un chiffrement SSL de bout en bout et en désactivant certaines versions du protocole SSL. L’exemple suivant désactive certaines stratégies SSL sur une passerelle d’application existante.

### <a name="step-1"></a>Étape 1 :

Récupérez la passerelle d’application à mettre à jour.

```powershell
$gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
```

### <a name="step-2"></a>Étape 2 :

Définissez une stratégie SSL. Dans l’exemple suivant, TLSv1.0 et TLSv1.1 sont désactivés.

```powershell
Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw
```

### <a name="step-3"></a>Étape 3 :

Pour finir, mettez à jour la passerelle. Il est important de noter que cette dernière étape représente une tâche à long terme. Une fois l’opération terminée, le chiffrement SSL de bout en bout est configuré sur la passerelle d’application.

```powershell
$gw | Set-AzureRmApplicationGateway
```

## <a name="get-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle Application Gateway

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle Application Gateway, un enregistrement CNAME peut être utilisé pour pointer vers le point de terminaison public de la passerelle Application Gateway. [Configuration d’un nom de domaine personnalisé pour Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Pour ce faire, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Le nom DNS de la passerelle Application Gateway doit être utilisé pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le renforcement de la sécurité de vos applications web avec un pare-feu d’applications Web via la passerelle Application Gateway, consultez la rubrique [Vue d’ensemble du pare-feu d'applications web](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png



<!--HONumber=Nov16_HO3-->


