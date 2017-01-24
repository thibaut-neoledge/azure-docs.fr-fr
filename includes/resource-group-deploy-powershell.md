## <a name="how-to-deploy-with-powershell"></a>Comment effectuer un déploiement avec PowerShell
1. Connectez-vous à votre compte Azure.
   
          Add-AzureAccount
   
   Une fois que vous avez entré vos informations d'identification, la commande retourne les informations relatives à votre compte.
   
          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   
2. Si vous avez plusieurs abonnements, fournissez l'ID d'abonnement que vous souhaitez utiliser pour le déploiement. 
   
          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>
3. Basculez sur le module Azure Resource Manager.
   
          Switch-AzureMode AzureResourceManager
4. Si vous n'avez pas de groupe de ressources, créez-en un. Indiquez le nom du groupe de ressources et l'emplacement dont vous avez besoin pour votre solution.
   
        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
   Un résumé du nouveau groupe de ressources est retourné.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup
5. Pour créer un déploiement pour votre groupe de ressources, exécutez la commande **New-AzureResourceGroupDeployment** et indiquez les paramètres nécessaires. Les paramètres comprennent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d'accès ou l'URL du modèle que vous avez créé et tous les autres paramètres nécessaires à votre scénario. 
   
   Vous disposez des options suivantes pour fournir les valeurs des paramètres : 
   
   * Utiliser des paramètres incorporés.
     
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"
   * Utiliser un objet de paramètres.
     
            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters
   * Utiliser un fichier de paramètres.
     
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>
   
   Une fois le groupe de ressources déployé, un résumé du déploiement apparaît.
   
             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...
6. Pour obtenir des informations sur les échecs de déploiement.
   
        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed
7. Pour obtenir des informations détaillées sur les échecs de déploiement.
   
        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput



<!--HONumber=Jan17_HO3-->


