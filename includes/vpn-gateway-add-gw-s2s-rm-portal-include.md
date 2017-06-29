1. Sur le côté gauche de la page du portail, cliquez sur **+** et tapez « Passerelle de réseau virtuel » dans la barre de recherche. Dans **Résultats**, recherchez et cliquez sur **Passerelle de réseau virtuel**.
2. En bas du panneau « Passerelle de réseau virtuel », cliquez sur **Créer**. Cette opération ouvre le panneau **Créer une passerelle de réseau virtuel**.

    ![Créer les champs du panneau de la passerelle de réseau virtuel](./media/vpn-gateway-add-gw-s2s-rm-portal-include/vnet_gw.png "Nouvelle passerelle")

3. Dans le panneau **Créer une passerelle réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel.

  - **Nom** : nommez votre passerelle. Cela ne revient pas au même que de nommer un sous-réseau de passerelle. Il s’agit du nom de l’objet de passerelle que vous créez.
  - **Type de passerelle** : sélectionnez **VPN**. Les passerelles VPN utilisent le type de passerelle de réseau virtuel **VPN**. 
  - Dans **Type de réseau privé virtuel** : sélectionnez le type de VPN spécifié pour votre configuration. La plupart des configurations requièrent un type de VPN basé sur un itinéraire.
  - **Référence** : sélectionnez la référence de passerelle dans la liste déroulante. Les références répertoriées dans la liste déroulante dépendent du type de VPN que vous sélectionnez. Pour plus d’informations sur les références de passerelle, consultez [Références (SKU) de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
  - **Emplacement** : vous devrez peut-être faire défiler pour afficher l’emplacement. Renseignez le champ **Emplacement** de manière à ce qu’il pointe vers l’emplacement où se trouve votre réseau virtuel. Si l’emplacement ne pointe pas vers la région où se trouve votre réseau virtuel, le réseau virtuel n’apparaîtra pas dans la liste déroulante « Choisir un réseau virtuel » de l’étape suivante.
  - **Réseau virtuel** : choisissez le réseau virtuel auquel vous souhaitez ajouter cette passerelle. Cliquez sur **Réseau virtuel** pour ouvrir le panneau « Choisir un réseau virtuel ». Sélectionnez le réseau virtuel. Si vous ne voyez pas votre réseau virtuel, assurez-vous que le champ Emplacement pointe sur la région dans laquelle se trouve votre réseau virtuel.
  - **Adresse IP publique** : le panneau « Créer une adresse IP publique » crée un objet d’adresse IP publique. L’adresse IP publique est affectée dynamiquement lors de la création de la passerelle VPN. Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques *dynamiques*. Toutefois, cela ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.

    - Tout d’abord, cliquez sur **Adresse IP publique** pour ouvrir le panneau « Choisir une adresse IP publique », puis cliquez sur **+ Créer** pour ouvrir le panneau « Créer une adresse IP publique ».
    - Ensuite, entrez un **nom** pour votre adresse IP publique et cliquez sur **OK** au bas de ce panneau pour enregistrer les modifications.

      ![Créer l’adresse IP publique](./media/vpn-gateway-add-gw-s2s-rm-portal-include/pip.png "Créer PIP")

4. Vérifiez les paramètres. Vous pouvez sélectionner **Épingler au tableau de bord** en bas du panneau si vous souhaitez que votre passerelle apparaisse sur le tableau de bord. 
5. Cliquez sur **Créer** pour créer la passerelle VPN. Les paramètres sont alors validés et la vignette « Déploiement d’une passerelle de réseau virtuel » s’affiche sur le tableau de bord. La création d’une passerelle peut prendre jusqu’à 45 minutes Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

Une fois la passerelle créée, examinez le réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît comme un appareil connecté. Vous pouvez cliquer sur l’appareil connecté (votre passerelle de réseau virtuel) pour afficher davantage d’informations.