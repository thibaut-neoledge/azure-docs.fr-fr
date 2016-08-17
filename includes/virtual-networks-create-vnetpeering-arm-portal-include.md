## Création d’une homologation de réseaux virtuels (VNet Peering) dans le portail Azure

Pour créer une homologation de réseaux virtuels selon le scénario ci-dessus à l’aide du portail Azure, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Pour établir une homologation de réseaux virtuels, vous devez créer un lien entre deux réseaux virtuels pour chaque direction. Vous pouvez créer tout d’abord le lien d’homologation de réseaux virtuels de VNET1 à VNET2. Dans le portail, cliquez sur **Parcourir** > **choisissez Réseaux virtuels**

	![Créer une homologation de réseaux virtuels dans le portail Azure](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure01.png)

3. Dans le panneau Réseaux virtuels, choisissez VNET1 et cliquez sur Homologations, puis sur Ajouter

	![Choisir l’homologation](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure02.png)

4. Dans le panneau Ajouter l’homologation, nommez le lien d’homologation LinkToVnet2, choisissez l’abonnement et le réseau virtuel homologue VNET2, puis cliquez sur OK.

	![Lien vers le réseau virtuel](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure03.png)

5. Une fois ce lien d’homologation de réseaux virtuels créé, l’état du lien apparaît comme suit :

	![État du lien](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure04.png)

6. Ensuite, créez le lien d’homologation de réseaux virtuels de VNET2 à VNET1. Dans le panneau Réseaux virtuels, choisissez VNET2, cliquez sur Homologations, puis sur Ajouter

	![Homologue depuis l’autre réseau virtuel](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure05.png)

7. Dans le panneau Ajouter l’homologation, nommez le lien d’homologation LinkToVnet1, choisissez l’abonnement et le réseau virtuel homologue VNET1, puis cliquez sur OK.

	![Mosaïque de création du réseau virtuel](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure06.png)

8. Une fois ce lien d’homologation de réseaux virtuels créé, l’état du lien apparaît comme suit :

	![État final du lien](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure07.png)

9. Vérifiez l’état du lien LinkToVnet2. Celui-ci est à présent également défini sur Connecté.

	![État final du lien 2](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure08.png)

10. Remarque : L’homologation de réseaux virtuels est établie uniquement si les deux liens sont connectés.

<!---HONumber=AcomDC_0803_2016-->