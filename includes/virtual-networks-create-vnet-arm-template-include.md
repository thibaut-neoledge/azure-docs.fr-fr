## Télécharger et comprendre le modèle ARM
Vous pouvez télécharger le modèle ARM existant pour créer un réseau virtuel et deux sous-réseaux sur GitHub, apporter les modifications souhaitées, puis le réutiliser. Pour ce faire, procédez comme suit :

1. Accédez à la [page d’exemples de modèles](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Cliquez sur **azuredeploy.json**, puis sur **RAW**.
3. Enregistrez le fichier dans un dossier local sur votre ordinateur.
4. Si vous connaissez déjà les modèles ARM, passez à l’étape 7.
5. Ouvrez le fichier que vous venez d’enregistrer et consultez le contenu sous **parameters** à la ligne 5. Les paramètres de modèle ARM fournissent un espace réservé pour les valeurs à remplir lors du déploiement.
   
   | Paramètre | Description |
   | --- | --- |
   | **emplacement** |Région Azure où le réseau virtuel doit être créé. |
   | **vnetName** |Nom du nouveau réseau virtuel |
   | **addressPrefix** |Espace d’adressage du réseau virtuel, au format CIDR |
   | **subnet1Name** |Nom du premier réseau virtuel |
   | **subnet1Prefix** |Bloc CIDR du premier sous-réseau |
   | **subnet2Name** |Nom du deuxième réseau virtuel |
   | **subnet2Prefix** |Bloc CIDR du deuxième sous-réseau |
   
   > [!IMPORTANT]
   > Les modèles ARM de GitHub sont susceptibles d’évoluer. Vérifiez le modèle avant de l’utiliser.
   > 
   > 
6. Vérifiez le contenu sous **resources** et notez les éléments suivants :
   
   * **type**. Type de ressource créée par le modèle. Dans ce cas, **Microsoft.Network/virtualNetworks**, qui représente un réseau virtuel.
   * **name**. Nom de la ressource. Remarquez l’utilisation de **[parameters('vnetName')]**, ce qui signifie que le nom sera fourni par l’utilisateur ou un fichier de paramètres au cours du déploiement.
   * **properties**. Liste des propriétés de la ressource. Ce modèle utilise les propriétés d’espace d’adressage et de sous-réseau lors de la création du réseau virtuel.
7. Retournez à la [page d’exemples de modèles](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Cliquez sur **azuredeploy-paremeters.json**, puis cliquez sur **RAW**.
9. Enregistrez le fichier dans un dossier local sur votre ordinateur.
10. Ouvrez le fichier que vous venez d’enregistrer et modifiez les valeurs des paramètres. Utilisez les valeurs ci-dessous pour déployer le réseau virtuel décrit dans notre scénario.
    
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
11. Enregistrez le fichier.

<!---HONumber=AcomDC_0211_2016-->