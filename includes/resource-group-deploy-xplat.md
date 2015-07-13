## Comment effectuer un déploiement avec l'interface de ligne de commande Azure

1. Connectez-vous à votre compte Azure.

        azure login

  Une fois que vous avez entré vos informations d'identification, la commande retourne le résultat de votre connexion.

        ...
        info:    login command OK

2. Si vous avez plusieurs abonnements, fournissez l'ID d'abonnement que vous souhaitez utiliser pour le déploiement.

        azure account set <YourSubscriptionNameOrId>

3. Basculez sur le module Azure Resource Manager.

        azure config mode arm

   Vous recevrez la confirmation du nouveau mode.

        info:     New mode is arm

4. Si vous n'avez pas de groupe de ressources, créez-en un. Indiquez le nom du groupe de ressources et l'emplacement dont vous avez besoin pour votre solution.

        azure group create -n ExampleResourceGroup -l "West US"

   Un résumé du nouveau groupe de ressources est retourné.

        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Pour créer un déploiement pour votre groupe de ressources, exécutez la commande suivante et indiquez les paramètres nécessaires. Les paramètres comprennent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d'accès ou l'URL du modèle que vous avez créé et tous les autres paramètres nécessaires à votre scénario.

   Vous disposez des options suivantes pour fournir les valeurs des paramètres :

   - Utiliser des paramètres incorporés et un modèle local.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Utiliser des paramètres incorporés et un lien vers un modèle.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Utiliser un fichier de paramètres.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  Une fois le groupe de ressources déployé, un résumé du déploiement apparaît.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. Pour obtenir des informations sur votre dernier déploiement.

         azure group log show -l ExampleResourceGroup

7. Pour obtenir des informations détaillées sur les échecs de déploiement.

         azure group log show -l -v ExampleResourceGroup

<!---HONumber=62-->