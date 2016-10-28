<properties
   pageTitle="Création de groupes de sécurité réseau en mode classique à l’aide de PowerShell | Microsoft Azure"
   description="Découvrez comment créer et déployer des groupes de sécurité réseau en mode classique à l'aide de PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# Procédure de création des groupes de sécurité réseau (classique) dans PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Cet article traite du modèle de déploiement classique. Vous pouvez également [créer un groupe de sécurité réseau dans le modèle de déploiement Resource Manager](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Les exemples de commandes PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessous. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Création d’un réseau virtuel](virtual-networks-create-vnet-classic-netcfg-ps.md).

## Création du groupe de sécurité réseau pour le sous-réseau frontal
Pour créer un groupe de sécurité réseau nommé **NSG-FrontEnd** selon le scénario ci-dessus, suivez les étapes ci-dessous :

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.

3. Création d’un groupe de sécurité réseau nommé **NSG-FrontEnd**.

		New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
		    -Label "Front end subnet NSG"

	Sortie attendue :

		Name         Location   Label               
		----         --------   -----               
		NSG-FrontEnd West US 	Front end subnet NSG


4. Créer une règle de sécurité autorisant l'accès à partir d'Internet vers le port 3389.

		Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
		| Set-AzureNetworkSecurityRule -Name rdp-rule `
		    -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
		    -SourceAddressPrefix Internet  -SourcePortRange '*' `
		    -DestinationAddressPrefix '*' -DestinationPortRange '3389'

	Sortie attendue :

		Name     : NSG-FrontEnd
		Location : Central US
		Label    : Front end subnet NSG
		Rules    :

		              Type: Inbound

		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           rdp-rule             100       Allow    INTERNET        *             *                3389           TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       


		              Type: Outbound

		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *

4. Créer une règle de sécurité autorisant l'accès à partir d'Internet vers le port 80.

		Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
		| Set-AzureNetworkSecurityRule -Name web-rule `
		    -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
		    -SourceAddressPrefix Internet  -SourcePortRange '*' `
		    -DestinationAddressPrefix '*' -DestinationPortRange '80'

	Sortie attendue :


		Name     : NSG-FrontEnd
		Location : Central US
		Label    : Front end subnet NSG
		Rules    :

		              Type: Inbound

		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           rdp-rule             100       Allow    INTERNET        *             *                3389           TCP     
		           web-rule             200       Allow    INTERNET        *             *                80             TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       


		              Type: Outbound

		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *   

## Création du groupe de sécurité réseau pour le sous-réseau principal
3. Création d’un groupe de sécurité réseau nommé **NSG-BackEnd**.

		New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
		    -Label "Back end subnet NSG"

	Sortie attendue :

		Name        Location   Label              
		----        --------   -----              
		NSG-BackEnd West US    Back end subnet NSG


4. Créer une règle de sécurité permettant l’accès depuis le sous-réseau frontal vers le port 1433 (port par défaut utilisé par SQL Server).

		Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
		| Set-AzureNetworkSecurityRule -Name rdp-rule `
		    -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
		    -DestinationAddressPrefix '*' -DestinationPortRange '1433'

	Sortie attendue :

		Name     : NSG-BackEnd
		Location : Central US
		Label    : Back end subnet NSG
		Rules    :

		              Type: Inbound

		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           fe-rule              100       Allow    192.168.1.0/24  *             *                1433           TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       


		              Type: Outbound

		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *      

4. Créer une règle de sécurité bloquant l'accès depuis le sous-réseau vers Internet.

		Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
		| Set-AzureNetworkSecurityRule -Name block-internet `
		    -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
		    -SourceAddressPrefix '*'  -SourcePortRange '*' `
		    -DestinationAddressPrefix Internet -DestinationPortRange '*'

	Sortie attendue :

		Name     : NSG-BackEnd
		Location : Central US
		Label    : Back end subnet NSG
		Rules    :

		              Type: Inbound

		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           fe-rule              100       Allow    192.168.1.0/24  *             *                1433           TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       


		              Type: Outbound

		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           block-internet       200       Deny     *               *             INTERNET         *              *       
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *   

<!---HONumber=AcomDC_0810_2016-->