### Pour créer un sous-réseau de passerelle dans le portail Azure

1. Dans le portail, accédez au réseau virtuel auquel vous souhaitez connecter une passerelle. Dans le panneau de votre réseau virtuel, cliquez sur l’icône **Paramètres** en haut du panneau pour développer le panneau. 

2. Dans le panneau **Paramètres**, cliquez sur **Sous-réseaux** pour développer le panneau Sous-réseaux.

3. Dans le panneau **Sous-réseaux**, cliquez sur **Ajouter** pour développer le panneau **Ajouter un sous-réseau**.

	![Ajouter le sous-réseau de passerelle](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsubnet250.png)

4. Dans le panneau **Ajouter un sous-réseau**, nommez votre sous-réseau **GatewaySubnet**. Vous ne devez pas lui affecter un autre nom, sinon la passerelle ne fonctionnera pas.

5. Ajoutez la **plage d’adresses** IP de votre passerelle.

6. Cliquez sur **OK** en bas du panneau pour créer le sous-réseau.

<!---HONumber=AcomDC_0406_2016-->