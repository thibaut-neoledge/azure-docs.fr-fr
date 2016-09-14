<properties
	pageTitle="Documentation Azure Government | Microsoft Azure"
	description="This provides a comparision of features and guidance on developing applications for Azure Government"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>


#  Sécurité et gestion d’Azure Government

##  Key Vault

Les informations suivantes identifient les limites d’Azure Government pour Azure Key Vault :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données chiffrées avec une clé Azure Key Vault peuvent contenir des données contrôlées/réglementées. | Les métadonnées Azure Key Vault n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre coffre de clés. N’entrez pas de données réglementées/contrôlées dans les champs suivants : Noms de groupes de ressources, Noms de coffres de clés, Nom d’abonnement |

Azure Key Vault est mis à la disposition générale dans Azure Government. Comme en environnement public, il n’y a pas d’extension. Azure Key Vault est donc uniquement disponible via PowerShell et l’interface CLI.

Pour plus d’informations, consultez la [documentation publique d’Azure Key Vault](/key-vault-get-started).

Pour obtenir des informations supplémentaires et des mises à jour, veuillez vous inscrire au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.

<!---HONumber=AcomDC_0831_2016-->