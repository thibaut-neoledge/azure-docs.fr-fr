1. Dans le portail Azure, accédez à **Nouveau** **>** **Mise en réseau** **>** **Passerelle de réseau local**.

	![créer une passerelle de réseau local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. Dans le panneau **Créer une passerelle de réseau local**, spécifiez un **nom** pour votre objet passerelle de réseau local.
 
3. Spécifiez une **adresse IP** pour votre passerelle. Il s’agit de l’adresse IP du périphérique VPN externe auquel vous souhaitez vous connecter. Elle ne peut pas se trouver derrière NAT et doit être accessible par Azure.

4. **Espace d’adressage** fait référence aux plages d’adresses sur votre réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Les plages que vous avez entrées ici ne peuvent pas chevaucher les plages d’espaces d’adressage que vous utilisez pour les réseaux virtuels qui communiqueront via la passerelle. Vous devez vous coordonner avec votre configuration locale ainsi qu’avec les espaces d’adressage de votre réseau virtuel Azure.
 
5. Pour **Abonnement**, vérifiez que l’abonnement approprié s’affiche.

6. Pour **Groupe de ressources**, sélectionnez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé. Pour créer un groupe de ressources, saisissez son nom dans la zone. Pour sélectionner un groupe de ressources déjà créé, cliquez sur **Groupe de ressources** pour ouvrir le panneau **Groupe de ressources**, puis sélectionnez le groupe de ressources que vous souhaitez utiliser.

7. Pour **Emplacement**, si vous créez une passerelle de réseau local, vous pouvez utiliser le même emplacement que celui de la passerelle de réseau virtuel, mais cela n’est pas requis. La passerelle de réseau local peut se trouver à un autre emplacement.

8. Laissez l’option « Épingler au tableau de bord » sélectionnée si vous souhaitez rechercher cette passerelle de réseau local facilement à partir du tableau de bord.

9. Cliquez sur **Créer** pour créer la passerelle de réseau local. Le message « Déploiement de la passerelle de réseau local » s’affichera sur votre tableau de bord.

10. Lorsque la passerelle de réseau local sera créée, elle s’ouvrira dans le portail pour que vous la visualisiez.

	
