## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Création d’un réseau virtuel dans le portail Azure
Pour créer un réseau virtuel selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://manage.windowsazure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **NOUVEAU** > **SERVICES RÉSEAU** > **RÉSEAU VIRTUEL** > **CRÉATION PERSONNALISÉE**, comme illustré ci-après.
   
    ![Créer un réseau virtuel dans le portail](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)
3. Sur la page **Détails du réseau virtuel**, tapez le **NOM** du réseau virtuel, sélectionnez son **EMPLACEMENT**, puis cliquez sur la flèche dans le coin inférieur droit de la page pour passer à l’étape 2. La figure ci-dessous illustre les paramètres de ce scénario.
   
    ![Détails du réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)
4. Dans la page **Serveurs DNS et connectivité VPN** , spécifiez le nom et l’adresse IP d’un maximum de 9 serveurs DNS. Si vous ne spécifiez pas de serveur DNS, votre réseau virtuel utilise la résolution de noms interne fournie par Azure. Pour notre scénario, nous n’allons pas configurer de serveur DNS.
5. Si vous souhaitez fournir un accès VPN de point à site à votre réseau virtuel, activez la case à cocher **Configurer un réseau VPN de point à site** . Si vous ne configurez pas de VPN de point à site, vous pouvez l’ajouter à votre réseau virtuel à tout moment après sa création. Pour notre scénario, nous n’allons pas configurer de VPN de point à site.
6. Si vous souhaitez offrir une connectivité VPN de site à site entre votre réseau virtuel et un autre réseau virtuel ou votre réseau local, cochez la case **Configurer un réseau VPN de site à site** et spécifiez si vous souhaitez ou non utiliser **ExpressRoute**, ainsi que le nom du réseau auquel se connecter. Si vous ne configurez pas de VPN de site à site, vous pouvez l’ajouter à votre réseau virtuel à tout moment après sa création. Pour notre scénario, nous n’allons pas configurer de VPN de site à site.
7. Cliquez sur la flèche dans l’angle inférieur droit de la page pour passer à l’étape 3. La figure ci-dessous illustre les paramètres de ce scénario.
   
    ![Serveurs DNS et page de connectivité VPN](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)
8. Sur la page **Espaces d’adresses du réseau virtuel**, sous **ADRESSE IP DE DÉPART**, cliquez sur *10.0.0.0* pour modifier l’espace d’adressage virtuel, puis tapez l’espace d’adressage de départ que vous voulez utiliser. Pour notre scénario, tapez *192.168.0.0*. 
9. Sous **CIDR (NOMBRE D’ADRESSES)** , sélectionnez le nombre de bits du masque de sous-réseau. Pour notre scénario, sélectionnez *16 (65536)*.
10. Sous **SOUS-RÉSEAUX**, cliquez sur *Subnet-1* et renommez le sous-réseau si nécessaire. Pour notre scénario, changez son nom en *frontal*.
    
    > [!NOTE]
    > Si vous cliquez en dehors de la zone de texte du nom du sous-réseau, vous ne pouvez pas remodifier le nom de ce sous-réseau. Pour cela, vous devez supprimer le sous-réseau en cliquant sur le bouton X à droite, puis ajouter un nouveau sous-réseau comme décrit à l’étape 13 ci-dessous.
    > 
    > 
11. Sous **ADRESSE IP DE DÉPART** pour le premier sous-réseau, spécifiez l’adresse IP de départ du sous-réseau. Pour notre scénario, tapez *192.168.1.0*.
12. Sous **CIDR (NOMBRE D’ADRESSES)** , sélectionnez le nombre de bits du masque de sous-réseau du premier sous-réseau. Pour notre scénario, sélectionnez *24 (256)*.
13. Cliquez sur **Ajouter un sous-réseau** pour ajouter un nouveau sous-réseau, si nécessaire. Pour notre scénario, ajoutez un sous-réseau et répétez les étapes 10 à 12 pour configurer le réseau virtuel comme indiqué dans la figure ci-dessous.
    
    ![Espaces d’adressage du réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)
14. Cliquez sur la coche dans le coin inférieur droit de la page pour créer le réseau virtuel. Après quelques secondes, votre réseau virtuel apparaît dans la liste des réseaux virtuels disponibles, comme indiqué dans la figure ci-dessous.
    
    ![Nouveau réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)



<!--HONumber=Nov16_HO3-->


