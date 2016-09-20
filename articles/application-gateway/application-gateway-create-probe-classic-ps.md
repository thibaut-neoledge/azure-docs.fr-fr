<properties
   pageTitle="Créer une sonde personnalisée pour Application Gateway en utilisant PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprendre à créer une sonde personnalisée pour Application Gateway en utilisant PowerShell dans le modèle de déploiement classique"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace" />

# Créer une sonde personnalisée pour Azure Application Gateway (classique) en utilisant PowerShell

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-probe-portal.md)
- [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-probe-ps.md)
- [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

<BR>

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](application-gateway-create-probe-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## Créer une passerelle Application Gateway

Pour créer une passerelle d’application :

1. Créez une ressource de passerelle d’application.
2. Créez un fichier XML de configuration ou un objet de configuration.
3. Validez la configuration dans la ressource de passerelle d’application nouvellement créée.

### Créer une ressource de passerelle d’application

Pour créer la passerelle, utilisez l’applet de commande **New-AzureApplicationGateway** en remplaçant les valeurs par les vôtres. La facturation de la passerelle ne démarre pas à ce stade. La facturation commence à une étape ultérieure, lorsque la passerelle a démarré correctement.

L’exemple suivant illustre la création d’une nouvelle passerelle d’application avec un réseau virtuel appelé « testvnet1 » et un sous-réseau appelé « subnet-1 ».

	New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Pour vérifier que la passerelle a bien été créée, vous pouvez utiliser l’applet de commande **Get-AzureApplicationGateway**.

	Get-AzureApplicationGateway AppGwTest

>[AZURE.NOTE]  La valeur par défaut du paramètre *InstanceCount* est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous avez le choix entre Small, Medium et Large.

 Les paramètres *VirtualIPs* et *DnsName* sont sans valeur, car la passerelle n’a pas encore démarré. Ces valeurs seront créées une fois la passerelle en cours d'exécution.

## Configurer une passerelle d’application

Vous pouvez configurer la passerelle d’application à l’aide d’un objet de configuration ou de XML.

## Configurer une passerelle d’application à l’aide de XML

Dans l’exemple ci-dessous, vous allez utiliser un fichier XML pour configurer tous les paramètres de la passerelle d’application et les valider dans la ressource de passerelle d’application.

### Étape 1 :

Copiez le texte suivant dans le Bloc-notes.

	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
	<FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
      </Probes>
     <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
				<IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
	    <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


Modifiez les valeurs entre parenthèses pour les éléments de configuration. Enregistrez le fichier avec l’extension .xml.

L’exemple suivant montre comment utiliser un fichier de configuration pour configurer la passerelle d’application en vue d’équilibrer la charge du trafic HTTP sur le port public 80 et d’orienter le trafic réseau vers le port 80 principal entre deux adresses IP en utilisant une sonde personnalisée.

>[AZURE.IMPORTANT] L’élément de protocole Http ou Https est sensible à la casse.

Un nouvel élément de configuration <Probe> est ajouté pour configurer les sondes personnalisées.

Les paramètres de configuration sont :

- **Nom** : nom de référence de la sonde personnalisée.
- **Protocole** : protocole utilisé (les valeurs possibles sont HTTP ou HTTPS).
- **Hôte** et **Chemin** : chemin complet de l’URL qui est appelé par la passerelle d’application pour déterminer l’intégrité de l’instance. Par exemple : avec un site web http://contoso.com/, la sonde personnalisée peut être configurée pour « http://contoso.com/path/custompath.htm » afin que les contrôles de sonde renvoient une réponse HTTP réussie.
- **Intervalle** : configure les vérifications d’intervalle de sonde en secondes.
- **Délai d’expiration** : définit le délai d’expiration d’un contrôle de réponse HTTP.
- **Seuil de défaillance sur le plan de l’intégrité** : le nombre d’échecs de réponses HTTP nécessaires pour marquer l’instance de serveur principal comme *défectueuse*.

Le nom de la sonde est référencé dans la configuration <BackendHttpSettings> pour affecter le pool principal qui va utiliser les paramètres de sonde personnalisée.

## Ajouter une configuration de sonde personnalisée à une passerelle d’application existante

La modification de la configuration actuelle d’une passerelle d’application se fait en trois étapes : obtenez le fichier de configuration XML actuel, modifiez-le de façon à avoir une sonde personnalisée et configurez la passerelle d’application avec les nouveaux paramètres XML.

### Étape 1 :

Accédez au fichier XML à l’aide de get-AzureApplicationGatewayConfig. Le fichier XML de configuration est alors exporté pour être modifié de façon à y ajouter un paramètre de sonde.

	Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### Étape 2 :

Ouvrez le fichier XML dans un éditeur de texte. Ajoutez une section `<probe>` après `<frontendport>`.

	<Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    </Probes>

Dans la section backendHttpSettings du fichier XML, ajoutez le nom de la sonde comme dans l’exemple suivant :

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Enregistrez le fichier XML.

### Étape 3

Mettez à jour la configuration de la passerelle d’application à partir du nouveau fichier XML en utilisant **Set-AzureApplicationGatewayConfig**. Cette opération permettra de mettre à jour votre passerelle d’application avec cette nouvelle configuration.

	Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## Étapes suivantes

Si vous voulez configurer le déchargement SSL (Secure Sockets Layer), consultez [Configuration d’une passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous voulez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

<!---HONumber=AcomDC_0907_2016-->