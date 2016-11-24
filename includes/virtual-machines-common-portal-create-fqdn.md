## <a name="quick-steps"></a>Procédure rapide
Cet article suppose que vous vous êtes connecté à votre abonnement dans le portail et que vous avez créé une machine virtuelle avec les images disponibles, à l’aide du modèle de déploiement Resource Manager. Une fois que votre machine virtuelle commence à s’exécuter, suivez ces étapes.

1. Sélectionnez votre machine virtuelle dans le portail. Le nom DNS est vide. Cliquez sur **Adresse IP publique** :
   
   ![Sélectionner la ressource d’adresse IP publique dans le portail](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2. Entrez le nom DNS souhaité, puis cliquez sur **Enregistrer**.
   
   ![Entrer un nom DNS pour votre ressource IP publique](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)
   
   La ressource d’adresse IP publique affiche désormais cette nouvelle étiquette DNS sur son panneau.

3. Fermez les panneaux d’adresse IP publique, puis revenez au panneau de vue d’ensemble de machine virtuelle dans le portail. Au bout de quelques secondes, le portail doit mettre à jour vos paramètres. Vérifiez que le nom DNS/nom de domaine complet apparaît en regard de l’adresse IP pour la ressource **Adresse IP publique**.
   
   ![Vérifier la définition de votre nouveau nom DNS](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)



<!--HONumber=Nov16_HO3-->


