1. Dans le portail, accédez à **Nouveau** > **Mise en réseau** > **Passerelle de réseau virtuel**. Cette opération ouvre le panneau **Créer une passerelle de réseau virtuel**.

	![Passerelle](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. Dans le panneau **Créer une passerelle de réseau virtuel**, attribuez un nom à votre passerelle dans le champ **Nom**. Cela ne revient pas au même que de nommer un sous-réseau de passerelle. Il s’agit du nom de l’objet de passerelle que vous allez créer.

3. Renseignez le champ **Emplacement** de manière à ce qu’il pointe vers l’emplacement où se trouve votre réseau virtuel. Si vous ne le faites pas, la liste des réseaux virtuels n’affiche pas votre réseau virtuel.
 
4. Choisissez ensuite le réseau virtuel auquel vous souhaitez ajouter cette passerelle. Cliquez sur **Réseau virtuel** pour ouvrir le panneau **Choisir un réseau virtuel**. Sélectionnez le réseau virtuel. Pour que le réseau virtuel apparaisse dans la liste, il doit déjà comporter un sous-réseau de passerelle valide.

5. Définissez une adresse IP publique. Cliquez sur **l’adresse IP publique** pour ouvrir le panneau **Choisir une adresse IP publique**. Cliquez sur **Créer** pour ouvrir le panneau **Créer une adresse IP publique**. Donnez à un nom à votre adresse IP publique. Cette opération crée un objet d’adresse IP publique à laquelle une adresse IP publique sera être affectée dynamiquement. <br>Cliquez sur **OK** pour enregistrer vos modifications.

5. Dans **Type de passerelle**, sélectionnez le type de passerelle spécifié pour votre configuration.

6. Dans **Type de réseau privé virtuel**, sélectionnez le type de VPN spécifié pour votre configuration.

7. Dans **Abonnement**, vérifiez que l’abonnement approprié est sélectionné.

8. Le **Groupe de ressources** est déterminé par le réseau virtuel que vous sélectionnez.

9. N’ajustez pas **l’emplacement** après avoir spécifié les paramètres ci-dessus.

10. À ce stade, votre panneau ressemble au graphique de l’étape 1. Vérifiez que les paramètres correspondent aux paramètres de votre propre configuration. Vous pouvez sélectionner **Épingler au tableau de bord** en bas du panneau si vous souhaitez que votre passerelle apparaisse sur le tableau de bord.

11. Cliquez sur **Créer** pour créer la passerelle. Les paramètres sont alors validés et la vignette « Déploiement d’une passerelle de réseau virtuel » s’affiche sur le tableau de bord. La création d’une passerelle peut prendre jusqu’à 45 minutes Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

	![Passerelle](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. Une fois la passerelle créée, examinez le réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît comme un appareil connecté. Vous pouvez cliquer sur le périphérique connecté (votre passerelle de réseau virtuel) pour afficher davantage d’informations.

<!---HONumber=AcomDC_0810_2016-->