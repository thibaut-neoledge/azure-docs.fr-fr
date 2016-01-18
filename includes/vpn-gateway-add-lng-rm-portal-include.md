Pour créer une passerelle de réseau local, procédez comme suit :

1. Utilisez l’option **Parcourir** et filtrez pour localiser le panneau **Passerelles de réseau local**, puis cliquez sur **Ajouter**.
2. Sur le panneau **Créer des passerelles de réseau local**, attribuez un **nom** à votre objet passerelle de réseau local. Pour cet exemple, nous allons nommer la passerelle de réseau local *GW1LocalNet*.
3. Configurez une **adresse IP** pour votre passerelle. Il s’agit de l’adresse IP du périphérique VPN externe auquel vous souhaitez vous connecter. Elle ne peut pas se trouver derrière NAT et doit être accessible par Azure. Il s’agit de l’adresse IP de périphérique à laquelle votre passerelle Azure se connectera.
4. **Espace d’adressage** fait référence aux plages d’adresses sur votre réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Les plages que vous avez entrées ici ne peuvent pas chevaucher les plages d’espaces d’adressage que vous utilisez pour les réseaux virtuels qui communiqueront via la passerelle. Vous devez vous coordonner avec votre configuration locale ainsi qu’avec les espaces d’adressage de votre réseau virtuel Azure. 
5. Pour **Abonnement**, vérifiez que l’abonnement approprié s’affiche.
6. Pour **Groupe de ressources**, sélectionnez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé. Pour créer un groupe de ressources, saisissez son nom dans la zone. Pour sélectionner un groupe de ressources déjà créé, cliquez sur **Groupe de ressources** pour ouvrir le panneau **Groupe de ressources**, puis sélectionnez le groupe de ressources que vous souhaitez utiliser.
7. Pour **Emplacement**, vérifiez que l’emplacement est le même que celui de la passerelle de réseau virtuel que vous allez associer.
8. Laissez l’option « Épingler au tableau de bord » sélectionnée si vous souhaitez rechercher cette passerelle de réseau local facilement à partir du tableau de bord.
9. Cliquez sur **Créer** pour créer la passerelle de réseau local. Le message « Déploiement de la passerelle de réseau local » s’affichera sur votre tableau de bord. La création ne devrait pas prendre beaucoup de temps.

<!---HONumber=AcomDC_0107_2016-->