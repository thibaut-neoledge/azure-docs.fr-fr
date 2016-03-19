Pour créer un réseau virtuel selon le scénario ci-dessus à l'aide du portail Azure, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.

2. Cliquez sur **NOUVEAU** > **Réseau** > **Réseau virtuel**, cliquez sur **Resource Manager** dans la liste **Sélectionner un modèle de déploiement**, puis cliquez sur **Créer**.

3. Dans le panneau **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel, comme illustré dans la figure ci-dessous.

	![Panneau Créer un réseau virtuel](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Cliquez sur **Groupe de ressources** et sélectionnez un groupe de ressources auquel ajouter le réseau virtuel ou cliquez sur **Créer** pour ajouter le réseau virtuel à un groupe de ressources. La figure ci-dessous illustre les paramètres du nouveau groupe de ressources **TestRG**. Pour plus d’informations sur les groupes de ressources, consultez la page [Présentation d’Azure Resource Manager](resource-group-overview.md/#resource-groups).

	![Groupe de ressources](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Si nécessaire, modifiez les paramètres **Abonnement** et **Emplacement** de votre réseau virtuel.

6. Cliquez sur **Créer** et notez la mosaïque **Création du réseau virtuel**, comme indiqué dans la figure ci-dessous.

	![Mosaïque de création du réseau virtuel](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

7. Attendez que le réseau virtuel soit créé, puis dans le volet **Réseau virtuel**, cliquez sur **Tous les paramètres** > **Sous-réseaux** > **Ajouter**.

8. Spécifiez les paramètres de sous-réseau pour le sous-réseau *BackEnd* comme illustré ci-dessous, puis cliquez sur **OK**.

	![Paramètres de sous-réseau](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

9. Affichez la liste des sous-réseaux.

	![Liste des sous-réseaux dans le réseau virtuel](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

<!---HONumber=AcomDC_0107_2016-->