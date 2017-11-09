1. Dans le [portail](http://portal.azure.com), accédez au réseau virtuel Resource Manager pour lequel vous souhaitez créer une passerelle de réseau virtuel.
2. Dans la section **Paramètres** de la page de votre réseau virtuel, cliquez sur **Sous-réseaux** pour développer la page Sous-réseaux.
3. Sur la page **Sous-réseaux**, cliquez sur **+Sous-réseau de passerelle** pour ouvrir la page **Ajouter un sous-réseau**.

  ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Ajouter un sous-réseau de passerelle")
4. Le **Nom** de votre sous-réseau est automatiquement rempli avec la valeur « GatewaySubnet ». Cette valeur est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle. Ajustez le remplissage automatique des valeurs de la **plage d’adresses** pour correspondre à votre configuration requise, puis cliquez sur **OK** au bas de la page pour créer le sous-réseau.

  ![Ajout du sous-réseau](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Ajout du sous-réseau")