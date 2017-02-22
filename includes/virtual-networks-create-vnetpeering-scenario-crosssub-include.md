## <a name="a-namex-subapeering-across-subscriptions"></a><a name="x-sub"></a>Homologation entre des abonnements
Dans ce scénario, vous allez créer une homologation entre deux réseaux virtuels existants dans différents abonnements.

![scénario impliquant différents abonnements](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet Peering s’appuie sur le contrôle d’accès en fonction du rôle (RBAC) pour l’autorisation. Pour un scénario impliquant différents abonnements, vous devez tout d’abord octroyer une autorisation suffisante aux utilisateurs qui créeront le lien d’homologation.

> [!NOTE]
> Si un même utilisateur dispose du privilège sur les deux abonnements, vous pouvez ignorer les étapes 1 à 4 suivantes.
> 
> 



<!--HONumber=Feb17_HO1-->


