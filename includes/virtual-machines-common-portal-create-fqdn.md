## Procédure rapide
Cet article suppose que vous vous êtes connecté à votre abonnement dans le portail et que vous avez créé une machine virtuelle avec les images disponibles, à l’aide du modèle de déploiement Resource Manager. Une fois que votre machine virtuelle commence à s’exécuter, suivez ces étapes.

1. Affichez les paramètres de machine virtuelle sur le portail, puis cliquez sur l’adresse IP publique.
   
   ![localiser la ressource d’adresse IP](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)
2. Notez que le nom DNS de l’adresse IP publique est vide. Cliquez sur **Configuration** dans le panneau de l’adresse IP publique.
   
   ![paramètres ip](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)
3. Entrez l’étiquette souhaitée pour le nom DNS, puis **enregistrez** cette configuration.
   
   ![entrer l’étiquette du nom dns](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)
   
   La ressource d’adresse IP publique affiche désormais cette nouvelle étiquette DNS sur son panneau.
4. Fermez les panneaux de l’adresse IP publique, puis revenez au panneau de la machine virtuelle dans le portail. Vérifiez que le nom DNS/nom de domaine complet apparaît en regard de l’adresse IP pour la ressource d’adresse IP publique.
   
   ![le nom de domaine complet est créé](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)

<!---HONumber=AcomDC_0831_2016-->