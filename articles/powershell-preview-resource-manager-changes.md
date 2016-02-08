<properties
	pageTitle="Modifications de Resource Manager dans Azure PowerShell 1.0 en version préliminaire | Microsoft Azure"
	description="Décrit les modifications qui ont été apportées aux applets de commande Resource Manager dans Azure PowerShell 1.0 en version préliminaire."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="na"
	ms.date="01/26/2016"
	ms.author="gauravbh;tomfitz"/>

#Modifications apportées aux applets de commande PowerShell de gestion dans Azure Resource Manager

Dans le cadre du lancement d’Azure PowerShell 1.0 en version préliminaire, nous avons apporté quelques améliorations aux applets de commande de gestion. Ces améliorations viennent s’ajouter aux modifications des noms d’applet de commande effectuées dans la version préliminaire 1.0. Du fait que certaines de ces améliorations impliquent des changements importants, il est possible que vos scripts existants ne s’exécutent plus correctement. Nous espérons toutefois que les modifications apportées amélioreront votre expérience utilisateur. Nous vous invitons à nous envoyer vos commentaires sur ces modifications afin que nous puissions les prendre en compte pour Azure PowerShell 1.0. Alors, n’hésitez pas à essayer les nouvelles applets de commande et à nous faire part de votre avis.

##Déploiement de modèle découplé des groupes de ressources

Dans la version 0.9.8, tous les paramètres de déploiement de modèle étaient également définis dans les applets de commande de groupe de ressources. Dans New-AzureResourceGroup, vous pouviez donc créer un groupe de ressources, mais aussi fournir des informations sur les modèles à déployer. New-AzureResourceGroupDeployment proposait la même fonctionnalité de déploiement de modèle. Dans la version préliminaire 1.0, nous avons découplé cette fonctionnalité. Maintenant, New-AzureRMResourceGroup fournit la fonctionnalité de création des groupes de ressources, tandis que New-AzureRmResourceGroupDeployment fournit la fonctionnalité de déploiement du modèle. Le chaînage (piping) vous permet de les utiliser ensemble. Elles sont ainsi plus faciles à comprendre et à utiliser.

##Regroupement des applets de commande de journal d’audit

De nombreuses applets de commande permettaient d’obtenir les journaux d’audit dans différentes étendues : par exemple, Get-AzureResourceProviderLog, Get-AzureResourceGroupLog, Get-AzureSubscriptionIdLog et Get-AzureResourceLog. Pour obtenir des journaux, vous deviez souvent exécuter une combinaison d’applets de commande de journal. Ce processus n’était pas optimal. Nous avons regroupé cette fonctionnalité dans une seule applet de commande, qui peut être appelée dans différentes étendues en définissant des paramètres. À présent, vous pouvez appeler Get-AzureRmLog avec le paramètre approprié pour spécifier l’étendue.

Dans la version 0.9.8, pour obtenir les journaux d'un groupe de ressources particulier, vous exécutiez une procédure semblable à celle ci-dessous :

    Get-AzureResourceGroupLog -ResourceGroup <resource-group-name>

Pour obtenir les journaux pour une ressource particulière, vous procédiez ainsi :

     Get-AzureResourceLog -ResourceId
     /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>/providers/<provider-namespace>/
     <resource-name>

Dans la version 1.0 préliminaire, vous obtiendrez les mêmes informations en exécutant les applets de commande ci-dessous. Pour obtenir les journaux pour un groupe de ressources, vous allez exécuter :

     Get-AzureRmLog -ResourceGroup <resource-group-name>
     
Pour obtenir les journaux pour une ressource particulière, vous allez exécuter :

     Get-AzureRmLog -ResourceId /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>
     /providers/<provider-namespace>/<resource-name>

##Modifications apportées à l’applet de commande Get pour les ressources et les groupes de ressources

Nous avons modifié les applets de commande Get-AzureRmResource et Get-AzureRmResourceGroup pour qu’elles effectuent désormais directement les requêtes sur le fournisseur de ressources uniquement. Nous avons découplé la fonctionnalité de requête sur le cache dans de nouvelles applets de commande appelées Find-AzureRmResource*. Pour rechercher un groupe de ressources ayant une balise particulière, vous pouvez utiliser la nouvelle applet de commande Find-AzureRmResourceGroup. Avec cette modification, les paramètres de requête des balises ont été supprimés dans les applets de commande Get-AzureRmResource et Get-AzureRmResourceGroup.

Dans la version 0.9.8, pour trouver tous les groupes de ressources qui contiennent une balise spécifique, vous allez exécuter :

    Get-AzureResourceGroup -Tag <tag-object>

Dans la version 1.0 préliminaire, vous exécuterez l'applet de commande ci-dessous pour obtenir le scénario précédent :

    Find-AzureRmResourceGroup -Tag <tag-object>
    
##Suppression de Test-AzureResource et de Test-AzureResourceGroup

Ces applets de commande ne fonctionnaient pas correctement dans certains scénarios et pour certains types de ressources. Nous essayons actuellement d’améliorer cette fonctionnalité. En attendant, nous ne voulions pas continuer à vous proposer des applets de commande qui n’étaient pas suffisamment fiables. Nous avons donc supprimé ces applets de commande dans cette version, et nous les remplacerons par une solution fiable dans une version ultérieure.

##Améliorations de Get-AzureRmResourceProvider

Vous pouvez maintenant utiliser l’applet de commande Get-AzureRmResourceProvider pour obtenir des informations sur l’emplacement des fournisseurs de ressources. Cette applet de commande répertorie les fournisseurs et les types de ressources disponibles dans une région donnée. Elle vous permet aussi d’obtenir la liste des emplacements disponibles pour un fournisseur particulier. Nous avons supprimé l’applet de commande Get-AzureLocation, car vous pouvez obtenir toutes les informations sur les emplacements à l’aide de l’applet de commande Get-AzureRmResourceProvider.

Dans la version 0.9.8, pour obtenir la liste de tous les emplacements pris en charge, vous exécuterez :

     Get-AzureLocation

Et pour obtenir l'état d'enregistrement des fournisseurs, vous allez exécuter :

     Get-AzureProvider -ListAvailable

Dans la version 1.0 préliminaire, vous pouvez effectuer les opérations ci-dessus à l'aide d'une seule applet de commande, comme indiqué ci-dessous :

     Get-AzureRmResourceProvider -Location <location>

La procédure ci-dessus filtre les résultats afin d'afficher uniquement les fournisseurs et les types qui sont disponibles dans l'emplacement spécifié.

Ou vous pouvez filtrer le résultat sur un fournisseur spécifique, comme indiqué ci-dessous :

     Get-AzureRmResourceProvider -ProviderNamespace <provider-namespace>

La procédure ci-dessus filtre les résultats pour afficher les emplacements pris en charge pour le fournisseur spécifié uniquement.

##Applets de commande de stratégie

Nous avons ajouté la prise en charge des stratégies pour Azure Resource Manager. Les applets de commande PowerShell de gestion des stratégies ont été intégrées à cette version. Pour plus d’informations sur les stratégies, consultez [Utiliser une stratégie pour gérer les ressources et le contrôle d’accès](resource-manager-policy.md).

<!---HONumber=AcomDC_0128_2016-->