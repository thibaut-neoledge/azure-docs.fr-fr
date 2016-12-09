## <a name="overview-of-azure-resource-manager-templates"></a>Vue d’ensemble des modèles Azure Resource Manager
Le modèle Azure Resource Manager vous permet de spécifier de manière déclarative l’infrastructure IaaS Azure dans le langage Json en définissant les dépendances entre ressources. Pour obtenir une présentation détaillée des modèles Azure Resource Manager, consultez les articles suivants :

[Présentation du groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)

## <a name="sample-template-snippet-for-vm-extensions"></a>Extrait de l’exemple de modèle pour les extensions de machine virtuelle.
Pour déployer une extension de machine virtuelle dans le cadre du modèle Azure Resource Manager, vous devez spécifier la configuration de l’extension de façon déclarative dans le modèle.
Voici le format qui permet de spécifier la configuration de l’extension.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Comme vous pouvez le voir ci-dessus, le modèle d’extension contient deux parties principales :

1. Nom de l’extension, éditeur et version.
2. Configuration de l’extension.

## <a name="identifying-the-publisher-type-and-typehandlerversion-for-any-extension"></a>Identification de l’éditeur, du type et de typeHandlerVersion pour les extensions.
Les extensions de machine virtuelle Azure sont publiées par Microsoft et approuvées par des éditeurs tiers, et chaque extension est identifiée de façon univoque par son éditeur, son type et la typeHandlerVersion. Ils peuvent être déterminés comme suit :  



<!--HONumber=Nov16_HO3-->


