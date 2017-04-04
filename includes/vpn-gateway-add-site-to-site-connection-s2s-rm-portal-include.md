1. Localisez votre passerelle de réseau virtuel.
2. Cliquez sur **Connexions**. En haut du panneau Connexions, cliquez sur **+ Ajouter** pour ouvrir le panneau **Ajouter une connexion**.
   
    ![Créer une connexion de site à site](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection.png)
3. Dans le panneau **Ajouter une connexion**, donnez un **Nom** à votre connexion. 
4. Pour **Type de connexion**, sélectionnez **Site à site (IPSec)**.
5. Pour **Passerelle de réseau virtuel**, la valeur est fixe, car vous vous connectez à partir de cette passerelle.
6. Pour **Passerelle de réseau local**, cliquez sur **Choisir une passerelle de réseau local** et sélectionnez la passerelle de réseau local que vous souhaitez utiliser. 
7. Pour **Clé partagée**, la valeur doit correspondre à celle que vous utilisez pour votre périphérique VPN local. Dans l’exemple, nous avons utilisé « abc123 », mais vous pouvez (et devriez) utiliser une valeur plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée lors de la configuration de votre périphérique VPN.
8. Les autres valeurs pour **abonnement**, **groupe de ressources**, et **emplacement** sont fixes.
9. Cliquez sur **OK** pour créer votre connexion. Le message *Création de la connexion* clignote à l'écran.
10. Une fois la connexion créée, elle apparaît dans le panneau **Connexions** de la passerelle de réseau virtuel.
    
    ![Créer une connexion de site à site](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connectionstatus450.png)

