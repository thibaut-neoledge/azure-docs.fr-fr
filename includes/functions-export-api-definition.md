## <a name="export-an-api-definition"></a>Exporter une définition d’API
Vous disposez déjà d’une définition OpenAPI pour votre fonction. Il s’agit de celle que vous avez créée dans la rubrique [Créer une définition OpenAPI pour une fonction](../articles/azure-functions/functions-openapi-definition.md). L’étape suivante de ce processus consiste à exporter la définition d’API pour que PowerApps et Microsoft Flow puissent l’utiliser dans une API personnalisée.

> [!IMPORTANT]
> N’oubliez pas que vous devez vous connecter à Azure avec les mêmes informations d’identification que celles utilisées pour les locataires PowerApps et Microsoft Flow. Cela permet à Azure de créer l’API personnalisée et de la rendre disponible pour PowerApps et Microsoft Flow.

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur le nom de l’application de fonction (par exemple, **function-demo-energy**) > **Fonctionnalités de la plateforme** > **Définition de l’API**.

    ![Définition de l’API](media/functions-export-api-definition/api-definition.png)

1. Cliquez sur **Exporter vers PowerApps + Flow**.

    ![Source de la définition de l’API](media/functions-export-api-definition/export-api-1.png)

1. Dans le volet droit, utilisez les paramètres spécifiés dans le tableau.

    |Paramètre|Description|
    |--------|------------|
    |**Mode d’exportation**|Sélectionnez **Express** pour générer automatiquement l’API personnalisée. Quand vous sélectionnez l’option **Manuel**, la définition de l’API est exportée, mais vous devez ensuite l’importer dans PowerApps et Microsoft Flow manuellement. Pour plus d’informations, consultez [Exporter vers PowerApps et Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Environment**|Sélectionnez l’environnement dans lequel l’API personnalisée doit être enregistrée. Pour en savoir plus, consultez [Vue d’ensemble des environnements](https://powerapps.microsoft.com/tutorials/environments-overview/) ou [Vue d’ensemble des environnements (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nom de l’API personnalisée**|Entrez un nom tel que `Turbine Repair`.|
    |**Nom de la clé API**|Entrez le nom que les concepteurs d’applications et de flux doivent voir dans l’interface utilisateur de l’API personnalisée. Notez que l’exemple comprend des informations utiles.|
 
    ![Exporter vers PowerApps et Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Cliquez sur **OK**. L’API personnalisée est maintenant créée et ajoutée à l’environnement que vous avez spécifié.