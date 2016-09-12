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


#  Données et stockage Azure Government

##  Storage

Les informations suivantes identifient les limites d’Azure Government pour le stockage Azure :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Les données entrées, stockées et traitées au sein d’un produit de stockage Azure peuvent contenir des données contrôlées à l’exportation. Authentificateurs statiques, tels que les mots de passe et les codes confidentiels de carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Autres informations de sécurité/secrets, tels que les certificats, clés de chiffrement, clés principales et clés de stockage stockées dans les services Azure. | Les métadonnées Azure Storage n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre produit de stockage. N’entrez pas de données réglementées/contrôlées dans les champs suivants : Groupes de ressources, Noms de déploiements, Noms de ressources, Balises de ressources  

Pour plus d’informations, consultez la <a href=https://azure.microsoft.com/fr-FR/documentation/services/storage/> documentation publique d’Azure Storage </a>.

Pour obtenir des informations supplémentaires et des mises à jour, veuillez vous inscrire au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.

##  Base de données SQL

Les informations suivantes identifient les limites d’Azure Government pour le stockage Azure :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données stockées et traitées dans Microsoft Azure SQL peuvent contenir des données réglementées Azure Government. Vous devez utiliser des outils de base de données pour le transfert de données Azure Government réglementées. | Les métadonnées Azure SQL n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre produit de stockage. N’entrez pas de données réglementées/contrôlées dans les champs suivants : Nom de base de données, Nom d’abonnement, Groupes de ressources, Nom de serveur, Identifiant d’administrateur du serveur, Noms de déploiements, Noms de ressources, Balises de ressources

SQL Database 11 est mis à la disposition générale dans Azure Government.

Consultez le <a href="https://msdn.microsoft.com/fr-FR/library/bb510589.aspx"> Centre de sécurité Microsoft pour le moteur de base de données SQL </a> et la <a href="https://azure.microsoft.com/fr-FR/documentation/services/sql-database/"> documentation publique des bases de données SQL Azure </a> pour obtenir des conseils supplémentaires sur la configuration de visibilité des métadonnées, ainsi que les meilleures pratiques de protection.

Pour obtenir des informations supplémentaires et des mises à jour, veuillez vous inscrire au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.

<!---HONumber=AcomDC_0831_2016-->