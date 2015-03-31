<properties 
	pageTitle="À propos des métriques de Storage Analytics" 
	description="Découvrez comment utiliser Storage Analytics, y compris les métriques de transaction et les métriques de capacité, comment les métriques sont stockées et comment accéder aux données de métriques." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# À propos des métriques de Storage Analytics

## Vue d'ensemble

Storage Analytics peut stocker des métriques qui comprennent les statistiques de transactions agrégées et les données de capacité relatives aux demandes adressées à un service de stockage. Les transactions sont indiquées au niveau de l'opération d'API ainsi qu'au niveau du service de stockage, et la capacité est indiquée au niveau du service de stockage. Les données de métriques peuvent être utilisées pour analyser l'utilisation du service de stockage, diagnostiquer les problèmes au niveau des demandes effectuées auprès du service de stockage et améliorer les performances des applications qui utilisent un service.

Pour utiliser Storage Analytics, vous devez l'activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l'activer à partir du [Portail de gestion Azure](https://manage.windowsazure.com/) ; pour plus d'informations, consultez [Surveillance d'un compte de stockage](../how-to-monitor-a-storage-account). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. [Utilisez les opérations Get Blob Service Properties, Get Queue Service Properties](https://msdn.microsoft.com/library/hh452239.aspx) et [Get Table Service Properties](https://msdn.microsoft.com/library/hh452238.aspx) pour activer Storage Analytics pour chaque service.

##Métriques de transaction

Un ensemble fiable de données est enregistré toutes les heures ou chaque minute pour chaque service de stockage et opération d'API demandée, notamment les entrées/sorties, la disponibilité, les erreurs et les pourcentages de demande triés par catégorie. Vous pouvez examiner une liste complète des détails de transaction dans la rubrique [Schéma de table de métriques Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx).

Les données de transaction sont enregistrées à deux niveaux : au niveau du service et au niveau de l'opération API. Au niveau du service, des statistiques résumant toutes les opérations d'API demandées sont écrites dans une entité de table toutes les heures même si aucune demande n'a été adressée au service. Au niveau de l'opération d'API, les statistiques sont écrites uniquement dans une entité si l'opération a été demandée dans l'heure.

Par exemple, si vous exécutez une opération **GetBlob** sur votre service BLOB, les métriques Storage Analytics enregistrent la demande et l'incluent dans les données agrégées pour le service BLOB et l'opération **GetBlob**. Toutefois, si aucune opération **GetBlob** n'est demandée pendant l'heure, aucune entité n'est écrite dans $MetricsTransactionsBlob pour cette opération.

Les métriques de transaction sont enregistrées pour les demandes utilisateur et les demandes effectuées par Storage Analytics. Par exemple, les demandes par Storage Analytics pour écrire des journaux et des entités de table sont enregistrées. Pour plus d'informations sur la façon dont ces demandes sont facturées, consultez [Storage Analytics et facturation](https://msdn.microsoft.com/library/hh360997.aspx).

##Métriques de capacité

>[AZURE.NOTE] Actuellement, les métriques de capacité sont disponibles uniquement pour le service BLOB. Les métriques de capacité pour le service de Table et le service de File d'attente seront disponibles dans les versions ultérieures de Storage Analytics.

Les données de capacité sont enregistrées quotidiennement pour le service BLOB d'un compte de stockage et deux entités de table sont écrites. Une entité fournit des statistiques pour les données utilisateur et l'autre fournit des statistiques sur le conteneur d'objets blob " $logs " utilisé par Storage Analytics. La table " $MetricsCapacityBlob " inclut les statistiques suivantes :

- **Capacity** : volume de stockage utilisé par le service BLOB du compte de stockage, en octets.

- **ContainerCount** : nombre de conteneurs d'objet blob dans le service BLOB du compte de stockage.

- **ObjectCount** : nombre d'objets blob de blocs ou de pages validés et non validés dans le service BLOB du compte de stockage.

Pour plus d'informations sur les métriques de capacité, consultez Schéma de table de métriques Storage Analytics.

##Stockage des métriques

Toutes les données de métriques pour chacun des services de stockage sont stockées dans trois tables réservées à ce service : une table pour les informations sur les transactions, une autre pour les informations sur les transactions par minute et une troisième pour les informations sur la capacité. Les informations relatives aux transactions et aux transactions par minute se composent des données de demande et de réponse, et les informations de capacité se composent des données d'utilisation du stockage. Les métriques par heure et par minute et la capacité pour le service BLOB d'un compte de stockage sont accessibles dans des tables nommées comme indiqué dans le tableau ci-dessous.

| Niveau de métriques                      	| Noms de tables                                                                                                                 	| Prise en charge                                                                                                                       	|
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| Métriques toutes les heures, emplacement principal   	|  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                              	| Versions antérieures à la version du 15 août 2013 uniquement. Bien que ces noms soient toujours pris en charge, il est recommandé d'utiliser les tables répertoriées ci-dessous. 	|
| Métriques toutes les heures, emplacement principal   	| $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue             	| Toutes les versions, y compris celle du 15 août 2013.                                                                                                           	|
| Métriques par minute, emplacement principal   	| $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue       	| Toutes les versions, y compris celle du 15 août 2013.                                                                                                           	|
| Métriques toutes les heures, emplacement secondaire 	| $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue       	| Toutes les versions, y compris celle du 15 août 2013. La réplication géographique redondante avec accès en lecture doit être activée.                                                   	|
| Métriques par minute, emplacement secondaire 	| $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue 	| Toutes les versions, y compris celle du 15 août 2013. La géo-réplication redondante avec accès en lecture doit être activée.                                                   	|
| Capacité (service Blob uniquement)       	| $MetricsCapacityBlob                                                                                                        	| Toutes les versions, y compris celle du 15 août 2013.                                                                                                           	|



Ces tables sont automatiquement créées lorsque Storage Analytics est activé pour un compte de stockage. Elles sont accessibles via l'espace de noms du compte de stockage, par exemple : `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`


##Accès aux données de métriques

Toutes les données des tables de métriques sont accessibles à l'aide des API du service de Table, y compris les API .NET fournies par la bibliothèque managée Azure. L'administrateur de compte de stockage peut lire et supprimer des entités de table, mais il ne peut pas les créer ou les mettre à jour.

##Étapes suivantes
###Concepts
[Activation et configuration de Storage Analytics](https://msdn.microsoft.com/library/hh360996.aspx)

[Schéma de table de métriques Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx)

[Opérations et messages d'état enregistrés Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx)

[À propos de la journalisation Storage Analytics](https://msdn.microsoft.com/library/hh343262.aspx)

###Autres ressources

[Surveillance d'un compte de stockage](../how-to-monitor-a-storage-account) 
<!--HONumber=47-->
