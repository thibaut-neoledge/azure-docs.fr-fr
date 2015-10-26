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
	ms.date="10/09/2015"
	ms.author="gauravbh;tomfitz"/>

#Modifications apportées aux applets de commande PowerShell de gestion dans Azure Resource Manager

Dans le cadre du lancement d’Azure PowerShell 1.0 en version préliminaire, nous avons apporté quelques améliorations aux applets de commande de gestion. Ces améliorations viennent s’ajouter aux modifications des noms d’applet de commande effectuées dans la version préliminaire 1.0. Du fait que certaines de ces améliorations impliquent des changements importants, il est possible que vos scripts existants ne s’exécutent plus correctement. Nous espérons toutefois que les modifications apportées amélioreront votre expérience utilisateur. Nous vous invitons à nous envoyer vos commentaires sur ces modifications afin que nous puissions les prendre en compte pour Azure PowerShell 1.0. Alors, n’hésitez pas à essayer les nouvelles applets de commande et à nous faire part de votre avis.

##Déploiement de modèle découplé des groupes de ressources

Dans la version 0.9.8, tous les paramètres de déploiement de modèle étaient également définis dans les applets de commande de groupe de ressources. Dans New-AzureResourceGroup, vous pouviez donc créer un groupe de ressources, mais aussi fournir des informations sur les modèles à déployer. New-AzureResourceGroupDeployment proposait la même fonctionnalité de déploiement de modèle. Dans la version préliminaire 1.0, nous avons découplé cette fonctionnalité. Maintenant, New-AzureRMResourceGroup fournit la fonctionnalité de création des groupes de ressources, tandis que New-AzureRmResourceGroupDeployment fournit la fonctionnalité de déploiement du modèle. Le chaînage (piping) vous permet de les utiliser ensemble. Elles sont ainsi plus faciles à comprendre et à utiliser.

##Regroupement des applets de commande de journal d’audit

De nombreuses applets de commande permettaient d’obtenir les journaux d’audit dans différentes étendues : par exemple, Get-AzureResourceProviderLog, Get-AzureResourceGroupLog, Get-AzureSubscriptionIdLog et Get-AzureResourceLog. Pour obtenir des journaux, vous deviez souvent exécuter une combinaison d’applets de commande de journal. Ce processus n’était pas optimal. Nous avons regroupé cette fonctionnalité dans une seule applet de commande, qui peut être appelée dans différentes étendues en définissant des paramètres. À présent, vous pouvez appeler Get-AzureRmLog avec le paramètre approprié pour spécifier l’étendue.

##Modifications apportées à l’applet de commande Get pour les ressources et les groupes de ressources

Nous avons modifié les applets de commande Get-AzureRmResource et Get-AzureRmResourceGroup pour qu’elles effectuent désormais directement les requêtes sur le fournisseur de ressources uniquement. Nous avons découplé la fonctionnalité de requête sur le cache dans de nouvelles applets de commande appelées Find-AzureRmResource*. Pour rechercher un groupe de ressources ayant une balise particulière, vous pouvez utiliser la nouvelle applet de commande Find-AzureRmResourceGroup. Avec cette modification, les paramètres de requête des balises ont été supprimés dans les applets de commande Get-AzureRmResource et Get-AzureRmResourceGroup.

##Suppression de Test-AzureResource et de Test-AzureResourceGroup

Ces applets de commande ne fonctionnaient pas correctement dans certains scénarios et pour certains types de ressources. Nous essayons actuellement d’améliorer cette fonctionnalité. En attendant, nous ne voulions pas continuer à vous proposer des applets de commande qui n’étaient pas suffisamment fiables. Nous avons donc supprimé ces applets de commande dans cette version, et nous les remplacerons par une solution fiable dans une version ultérieure.

##Améliorations de Get-AzureRmResourceProvider

Vous pouvez maintenant utiliser l’applet de commande Get-AzureRmResourceProvider pour obtenir des informations sur l’emplacement des fournisseurs de ressources. Cette applet de commande répertorie les fournisseurs et les types de ressources disponibles dans une région donnée. Elle vous permet aussi d’obtenir la liste des emplacements disponibles pour un fournisseur particulier. Nous avons supprimé l’applet de commande Get-AzureLocation, car vous pouvez obtenir toutes les informations sur les emplacements à l’aide de l’applet de commande Get-AzureRmResourceProvider.

##Applets de commande de stratégie

Nous avons ajouté la prise en charge des stratégies pour Azure Resource Manager. Les applets de commande PowerShell de gestion des stratégies ont été intégrées à cette version. Pour plus d’informations sur les stratégies, consultez [Utiliser une stratégie pour gérer les ressources et le contrôle d’accès](resource-manager-policy.md).

<!---HONumber=Oct15_HO3-->