## <a name="how-to-create-a-vnet-using-a-network-config-file-from-powershell"></a>Création d'un réseau virtuel à l'aide d'un fichier de configuration réseau à partir de PowerShell
Azure utilise un fichier xml pour définir tous les réseaux virtuels disponibles pour un abonnement. Vous pouvez télécharger ce fichier, le modifier pour modifier ou supprimer des réseaux virtuels existants, et en créer de nouveaux. Dans ce document, vous découvrez comment télécharger ce fichier, appelé fichier de configuration réseau (ou netcgf), et le modifier pour créer un réseau virtuel. Pour plus d'informations sur le fichier de configuration réseau, consultez la rubrique [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) .

Pour créer un réseau virtuel à l'aide d'un fichier netcfg en utilisant PowerShell, procédez comme suit :

1. Si vous n'avez jamais utilisé Azure PowerShell, consultez la page [Installation et configuration d'Azure PowerShell](/powershell/azureps-cmdlets-docs) et suivez les instructions jusqu'à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. Dans la console Azure PowerShell, utilisez l'applet de commande **Get-AzureVnetConfig** pour télécharger le fichier de configuration réseau en exécutant la commande ci-dessous. 
   
        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml
   
    Sortie attendue :
   
        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  
3. Ouvrez le fichier enregistré à l’étape 2 ci-dessus à l’aide d’un éditeur XML ou de texte quelconque, et recherchez l’élément **<VirtualNetworkSites>** . Si vous avez déjà créé des réseaux, chacun est affiché comme son propre élément **<VirtualNetworkSite>** .
4. Pour créer le réseau virtuel décrit dans ce scénario, ajoutez le code XML suivant juste sous l’élément **<VirtualNetworkSites>** :
   
        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>
5. Enregistrez le fichier de configuration réseau.
6. Dans la console Azure PowerShell, utilisez l’applet de commande **Set-AzureVnetConfig** pour télécharger le fichier de configuration réseau en exécutant la commande ci-dessous. Observez le résultat sous la commande. L’état **Succeeded** doit apparaître sous **OperationStatus**. Si ce n'est pas le cas, recherchez les éventuelles erreurs dans le fichier xml.
   
       Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml
   
   Voici le résultat attendu pour la commande ci-dessus :
   
       OperationDescription OperationId                          OperationStatus
       -------------------- -----------                          ---------------
       Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
7. Dans la console Azure PowerShell, utilisez l’applet de commande **Get-AzureVnetSite** pour vérifier que le nouveau réseau a été ajouté en exécutant la commande ci-dessous. 
   
       Get-AzureVNetSite -VNetName TestVNet
   
   Voici le résultat attendu pour la commande ci-dessus :
   
       AddressSpacePrefixes : {192.168.0.0/16}
       Location             : Central US
       AffinityGroup        : 
       DnsServers           : {}
       GatewayProfile       : 
       GatewaySites         : 
       Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
       InUse                : False
       Label                : 
       Name                 : TestVNet
       State                : Created
       Subnets              : {FrontEnd, BackEnd}
       OperationDescription : Get-AzureVNetSite
       OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
       OperationStatus      : Succeeded

