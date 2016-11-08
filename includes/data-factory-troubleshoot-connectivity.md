## Résoudre les problèmes de connectivité
Utilisez l’onglet **Diagnostics** du **Gestionnaire de configuration de la passerelle de gestion des données** pour résoudre les problèmes de connexion.

1. Lancez le **Gestionnaire de configuration de la passerelle de gestion des données**. Vous pouvez exécuter directement « C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\ConfigManager.exe » (ou) rechercher **Passerelle** pour trouver un lien vers l’application **Passerelle de gestion des données de Microsoft**, comme l’illustre l'image suivante. 
   
    ![Rechercher la passerelle](./media/data-factory-troubleshoot-connectivity/search-gateway.png)
2. Basculez vers l’onglet **Diagnostics**.
   
    ![Diagnostics de la passerelle](./media/data-factory-troubleshoot-connectivity/data-factory-gateway-diagnostics.png) 
3. Sélectionnez le **type** de magasin de données (service lié). 
4. Spécifiez l’**authentification** et entrez les **informations d'identification** (ou) entrez la **chaîne de connexion** pour vous connecter au magasin de données. 
5. Cliquez sur **Tester la connexion** pour tester la connexion au magasin de données. 

<!---HONumber=AcomDC_0420_2016-->