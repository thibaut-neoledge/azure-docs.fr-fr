# Vue d’ensemble
## [Présentation de Resource Manager](resource-group-overview.md)
## [Services pris en charge](resource-manager-supported-services.md)
## [Déploiements Resource Manager et classiques](resource-manager-deployment-model.md)
## [Gouvernance de l’abonnement](resource-manager-subscription-governance.md)
## [Applications gérées](managed-application-overview.md)

# Prise en main
## [Exportation du modèle](resource-manager-export-template.md)
## [Créer votre premier modèle](resource-manager-create-first-template.md)
## [Visual Studio avec Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Exemples
## PowerShell
### [Déployer un modèle](resource-manager-samples-powershell-deploy.md)

## Interface de ligne de commande Azure
### [Déployer un modèle](resource-manager-samples-cli-deploy.md)

# Procédures
## Créer des modèles
### [Meilleures pratiques relatives aux modèles](resource-manager-template-best-practices.md)
### [Sections du modèle](resource-group-authoring-templates.md)
### [Lier à d’autres modèles](resource-group-linked-templates.md)
### [Définir la dépendance entre les ressources](resource-group-define-dependencies.md)
### [Créer plusieurs instances](resource-group-create-multiple.md)
### [Boucle séquentielle](resource-manager-sequential-loop.md)
### [Définir un emplacement](resource-manager-template-location.md)
### [Assigner des balises](resource-manager-template-tags.md)
### [Définir un nom et un type de ressource enfant](resource-manager-template-child-resource.md)
### [Mettre à jour une ressource](resource-manager-update.md)
### [Utiliser des objets pour les paramètres](resource-manager-objects-as-parameters.md)
### [Partager l’état entre les modèles liés](best-practices-resource-manager-state.md)
### [Modèles pour la conception de modèles](best-practices-resource-manager-design-templates.md)

## Déployer
### PowerShell
#### [Déployer un modèle](resource-group-template-deploy.md)
#### [Déployer un modèle privé avec un jeton SAS](resource-manager-powershell-sas-token.md)
#### [Exporter un modèle et le redéployer](resource-manager-export-template-powershell.md)
### Interface de ligne de commande Azure
#### [Déployer un modèle](resource-group-template-deploy-cli.md)
#### [Déployer un modèle privé avec un jeton SAS](resource-manager-cli-sas-token.md)
#### [Exporter un modèle et le redéployer](resource-manager-export-template-cli.md)
### [Portail](resource-group-template-deploy-portal.md)
### [API REST](resource-group-template-deploy-rest.md)
### [Déploiements de plusieurs groupes de ressources](resource-manager-cross-resource-group-deployment.md)
### [Intégration continue avec Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Passage de valeurs sécurisées lors du déploiement](resource-manager-keyvault-parameter.md)

## Gérer
### [PowerShell](powershell-azure-resource-manager.md)
### [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)
### [Portail](resource-group-portal.md)
### [API REST](resource-manager-rest-api.md)
### [Utiliser des balises pour organiser les ressources](resource-group-using-tags.md)
### [Déplacer les ressources vers un nouveau groupe ou un nouvel abonnement](resource-group-move-resources.md)
### [Exemples de gouvernance](resource-manager-subscription-examples.md)

## Contrôler l’accès
### Créer un principal du service
#### [PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure CLI 1.0](resource-group-authenticate-service-principal-cli.md)
#### [Portail](resource-group-create-service-principal-portal.md)
### [API d’authentification pour l’accès aux abonnements](resource-manager-api-authentication.md)
### [Verrouiller des ressources](resource-group-lock-resources.md)
### [Sécurité](best-practices-resource-manager-security.md)

## Définir des stratégies de ressources
### [Que sont les stratégies de ressources ?](resource-manager-policy.md)
### [Attribution de la stratégie du portail](resource-manager-policy-portal.md)
### [Attribution de la stratégie de script](resource-manager-policy-create-assign.md)
### [Stratégies de balises de ressources](resource-manager-policy-tags.md)
### [Stratégies de stockage](resource-manager-policy-storage.md)
### [Stratégies de machine virtuelle Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Stratégies de machine virtuelle Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## Utiliser des applications gérées
### [Publier une application gérée](managed-application-publishing.md)
### [Utiliser une application gérée](managed-application-consumption.md)
### [Créer des définitions d’interface utilisateur](managed-application-createuidefinition-overview.md)

## Audit
### [Afficher les journaux d’activité](resource-group-audit.md)
### [Voir les opérations de déploiement](resource-manager-deployment-operations.md)

## Résolution des problèmes
### [Erreurs de déploiement courantes](resource-manager-common-deployment-errors.md)

# Référence
## [Format de modèle](/azure/templates/)
## [Fonctions des modèles de gestionnaire des ressources Azure](resource-group-template-functions.md)
### [Fonctions de tableau et d’objet](resource-group-template-functions-array.md)
### [Fonctions de comparaison](resource-group-template-functions-comparison.md)
### [Fonctions de déploiement](resource-group-template-functions-deployment.md)
### [Fonctions numériques](resource-group-template-functions-numeric.md)
### [Fonctions de ressource](resource-group-template-functions-resource.md)
### [Fonctions de chaîne](resource-group-template-functions-string.md)
## [Fonctions de définition de l’interface utilisateur](managed-application-createuidefinition-functions.md)
## [Éléments de définition de l’interface utilisateur](managed-application-createuidefinition-elements.md)
### [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
### [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
### [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
### [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
### [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
### [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
### [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
### [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
### [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
### [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
### [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
### [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
### [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Interface de ligne de commande Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# les ressources
## [Demandes de limitation](resource-manager-request-limits.md)
## [Suivre les opérations asynchrones](resource-manager-async-operations.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=azure-resource-manager)
