## <a name="how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal"></a>Création d’un réseau virtuel à l’aide d’un fichier de configuration réseau dans le portail Azure
Azure utilise un fichier xml pour définir tous les réseaux virtuels disponibles pour un abonnement. Vous pouvez télécharger ce fichier, le modifier pour modifier ou supprimer des réseaux virtuels existants, et en créer de nouveaux. Dans ce document, vous découvrez comment télécharger ce fichier, appelé fichier de configuration réseau (ou netcgf), et le modifier pour créer un réseau virtuel. Pour plus d’informations sur le fichier de configuration réseau, consultez la rubrique [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) .

Pour créer un réseau virtuel à l’aide d’un fichier netcfg en utilisant le portail Azure, procédez comme suit :

1. Dans un navigateur, accédez à http://manage.windowsazure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Faites défiler la liste des services, puis cliquez sur **RÉSEAUX** , comme indiqué ci-dessous.
   
    ![Réseaux virtuels Azure](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)
3. En bas de la page, cliquez sur le bouton **EXPORTER** , comme illustré ci-dessous.
   
    ![Bouton Exporter](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)
4. Dans la page **Exporter la configuration de réseau** , sélectionnez l’abonnement dont vous voulez exporter la configuration de réseau virtuel, puis cliquez sur la coche dans le coin inférieur gauche de la page.
5. Suivez les instructions de votre navigateur pour enregistrer le fichier **NetworkConfig.xml** . Assurez-vous de noter où vous enregistrez le fichier.
6. Ouvrez le fichier enregistré à l’étape 5 ci-dessus à l’aide d’un éditeur XML ou de texte quelconque, et recherchez l’élément **<VirtualNetworkSites>** . Si vous avez déjà créé des réseaux, chacun est affiché comme son propre élément **<VirtualNetworkSite>** .
7. Pour créer le réseau virtuel décrit dans ce scénario, ajoutez le code XML suivant juste sous l’élément **<VirtualNetworkSites>** :
   
        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>
8. Enregistrez le fichier de configuration réseau.
9. Dans le portail Azure, dans le coin inférieur gauche de la page, cliquez sur **NOUVEAU**, cliquez sur **SERVICES RÉSEAU**, sur **RÉSEAU VIRTUEL**, puis sur **IMPORTER LA CONFIGURATION** comme indiqué dans la figure ci-dessous.
   
   ![IMPORTER LA CONFIGURATION](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)
10. Sur la page **Importer le fichier de configuration réseau**, cliquez sur **RECHERCHER LE FICHIER**, puis accédez au dossier dans lequel vous avez enregistré votre fichier à l’étape 8. Sélectionnez ce fichier, puis cliquez sur **Ouvrir**. La page web doit ressembler à la figure ci-dessous. Dans l’angle inférieur droit de la page, cliquez sur la flèche pour passer à l’étape suivante.
    
    ![Page d’importation du fichier de configuration réseau](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)
11. Sur la page **Création de votre réseau** , notez la présence de votre nouveau réseau virtuel, comme illustré dans la figure ci-dessous.
    
    ![Page de création de votre réseau](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)
12. Cliquez sur la coche dans le coin inférieur droit de la page pour créer le réseau virtuel. Après quelques secondes, votre réseau virtuel apparaît dans la liste des réseaux virtuels disponibles, comme indiqué dans la figure ci-dessous.
    
    ![Nouveau réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)



<!--HONumber=Nov16_HO3-->


