## Homologation entre des abonnements

Dans ce scénario, vous allez créer une homologation entre deux réseaux virtuels appartenant à différents abonnements.

![scénario impliquant différents abonnements](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet Peering s’appuie sur le contrôle d’accès en fonction du rôle (RBAC) pour l’autorisation. Pour un scénario impliquant différents abonnements, vous devez tout d’abord octroyer une autorisation suffisante aux utilisateurs qui créeront le lien d’homologation :

> [AZURE.NOTE] Si un même utilisateur dispose du privilège sur les deux abonnements, vous pouvez ignorer les étapes 1 à 4 indiquées ci-dessous.

<!---HONumber=AcomDC_0921_2016-->