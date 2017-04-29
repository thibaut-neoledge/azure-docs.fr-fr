Pour créer un réseau virtuel dans le modèle de déploiement Resource Manager à l’aide du portail Azure, suivez les étapes ci-dessous. Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres. Pour plus d’informations sur l’utilisation des réseaux virtuels, voir [Présentation du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau**. Dans le champ **Rechercher dans le marketplace**, tapez « réseau virtuel ». Localisez **Réseau virtuel** dans la liste renvoyée et cliquez pour ouvrir le panneau **Réseau virtuel**.
   
    ![Panneau de ressources Locate Virtual Network (Localiser le réseau virtuel)](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Locate virtual network resource blade")
3. En bas du panneau Réseau virtuel, à partir de la liste **Sélectionner un modèle de déploiement**, choisissez **Resource Manager** puis cliquez sur **Créer**.

    ![Sélectionner Resource Manager](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Select Resource Manager")

1. Dans le panneau **Créer un réseau virtuel** , configurez les paramètres du réseau virtuel. Lorsque vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont valides.
   
    ![Validation du champ](./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Field validation")
2. Le panneau **Créer un réseau virtuel** ressemble à l’exemple suivant. Il se peut que certaines valeurs soient renseignées automatiquement. Dans ce cas, remplacez ces valeurs par les vôtres.
   
    ![Panneau Créer un réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Create virtual network blade")
3. **Nom** : entrez le nom du réseau virtuel.
4. **Espace d’adressage** : entrez l’espace d’adressage. Si vous avez plusieurs espaces d’adressage à ajouter, ajoutez le premier espace d’adressage. Vous pourrez ajouter des espaces d’adressage supplémentaires plus tard, après avoir créé le réseau virtuel.
5. **Nom du sous-réseau** : ajoutez le nom du sous-réseau et de la plage d’adresses de sous-réseau. Vous pourrez ajouter des sous-réseaux supplémentaires plus tard, après avoir créé le réseau virtuel.
6. **Abonnement** : vérifiez que l’abonnement répertorié est approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
7. **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un nouveau en tapant un nom pour ce dernier. Si vous créez un groupe, nommez-le en fonction de vos valeurs de configuration planifiée. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
8. **Emplacement** : sélectionnez l’emplacement du réseau virtuel. L’emplacement détermine où se trouveront les ressources que vous déployez sur ce réseau virtuel.
9. Sélectionnez **Épingler au tableau de bord** si vous souhaitez être en mesure de trouver votre réseau virtuel facilement sur le tableau de bord, puis cliquez sur **Créer**.
   
   ![Épingler au tableau de bord](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "pin to dashboard")
10. Après avoir cliqué sur **Créer**, vous verrez une vignette apparaître sur votre tableau de bord. Celle-ci indique la progression de votre réseau virtuel. La vignette change lorsque le réseau virtuel est créé.
    
    ![Mosaïque de création du réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Creating virtual network tile")

