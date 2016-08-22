## Création d'un réseau virtuel classique dans le portail Azure

Pour créer un réseau virtuel classique selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **NOUVEAU** > **Réseau** > **Réseau virtuel**, notez que la liste **Sélectionner un modèle de déploiement** indique déjà **Classique**, puis cliquez sur **Créer**, comme illustré dans la figure ci-dessous.

	![Créer un réseau virtuel dans le portail Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. Dans le panneau **Réseau virtuel**, tapez le **Nom** du réseau virtuel, puis cliquez sur **Espace d’adressage**. Configurez les paramètres d’espace d’adressage de votre réseau virtuel et de son premier sous-réseau, puis cliquez sur **OK**. La figure ci-dessous illustre les paramètres des blocs CIDR de ce scénario.

	![Panneau Espace d’adressage](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Cliquez sur **Groupe de ressources** et sélectionnez un groupe de ressources auquel ajouter le réseau virtuel ou cliquez sur **Créer un groupe de ressources** pour ajouter le réseau virtuel à un groupe de ressources. La figure ci-dessous illustre les paramètres du nouveau groupe de ressources **TestRG**. Pour plus d’informations sur les groupes de ressources, consultez la page [Présentation d’Azure Resource Manager](../articles/virtual-network/resource-group-overview.md#resource-groups).

	![Panneau Créer un groupe de ressources](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Si nécessaire, modifiez les paramètres **Abonnement** et **Emplacement** de votre réseau virtuel.

6. Si vous ne souhaitez pas voir le réseau virtuel sous forme de mosaïque dans le **Tableau d’accueil**, désactivez **Épingler au tableau d’accueil**.

7. Cliquez sur **Créer** et notez la mosaïque **Création du réseau virtuel**, comme indiqué dans la figure ci-dessous.

	![Créer un réseau virtuel dans le portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Attendez que le réseau virtuel soit créé. Lorsque vous voyez la mosaïque ci-dessous, cliquez dessus pour ajouter d’autres sous-réseaux.

	![Créer un réseau virtuel dans le portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Vous devez voir la **configuration** de votre réseau virtuel comme indiqué ci-dessous.

	![Créer un réseau virtuel dans le portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Cliquez sur **Sous-réseaux** > **Ajouter**, puis tapez un **nom** et spécifiez un **(bloc CIDR) de plage d’adressage** pour votre sous-réseau, puis cliquez sur **OK**. La figure ci-dessous illustre les paramètres de notre scénario actuel.

	![Créer un réseau virtuel dans le portail Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

<!---HONumber=AcomDC_0810_2016-->