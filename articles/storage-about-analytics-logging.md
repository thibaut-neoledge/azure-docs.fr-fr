<properties 
	pageTitle="À propos de la journalisation Storage Analytics" 
	description="Découvrez comment utiliser le journal Storage Analytics, les demandes authentifiées et non authentifiées, et comment sont stockés les journaux " 
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

# À propos de la journalisation Storage Analytics

## Vue d'ensemble
Storage Analytics enregistre des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage. Ces informations peuvent servir à analyser des demandes individuelles et à diagnostiquer les problèmes au niveau d'un service de stockage. Les demandes sont enregistrées sur la base du meilleur effort.

Pour utiliser Storage Analytics, vous devez l'activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l'activer à partir du [Portail de gestion Azure](https://manage.windowsazure.com/) ; pour plus d'informations, consultez [Surveillance d'un compte de stockage](../how-to-monitor-a-storage-account). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations Get Blob Service Properties, Get Queue Service Properties et Get Table Service Properties pour activer Storage Analytics pour chaque service.

Les entrées de journal sont créées uniquement s'il existe une activité du service de stockage. Par exemple, si un compte de stockage a une activité dans son service BLOB, mais pas dans ses services de Table ou de File d'attente, seuls des journaux relatifs au service BLOB sont créés.

##Enregistrement des demandes authentifiées
Les types de demandes authentifiées suivants sont enregistrés :

- Demandes ayant réussi

- Demandes ayant échoué, y compris les erreurs de délai d'expiration, limitation, réseau, autorisation et autres erreurs

- Demandes utilisant une signature d'accès partagé, y compris les demandes ayant réussi et ayant échoué

- Demandes de données d'analyse

Les demandes effectuées par Storage Analytics lui-même, telles que la création ou la suppression d'un journal, ne sont pas enregistrées. Une liste complète des données enregistrées est documentée dans les rubriques [Opérations et messages d'état enregistrés Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx) et [Format de journal de Storage Analytics](https://msdn.microsoft.com/library/hh343259.aspx).

##Journalisation des demandes anonymes
Les types de demandes anonymes suivants sont enregistrés :

- Demandes ayant réussi

- Erreurs de serveur

- Erreurs de délai d'expiration pour le client et le serveur

- Demandes GET ayant échoué avec le code d'erreur 304 (non modifié)

Aucune autre demande anonyme ayant échoué n'est enregistrée. Une liste complète des données enregistrées est documentée dans les rubriques [Opérations et messages d'état enregistrés Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx) et [Format de journal de Storage Analytics](](https://msdn.microsoft.com/library/hh343259.aspx)).

##Mode de stockage des journaux
Tous les journaux sont stockés dans des objets blob de blocs dans un conteneur nommé $logs, qui est automatiquement créé lorsque Storage Analytics est activé pour un compte de stockage. Le conteneur $logs se trouve dans l'espace de noms d'objets blob du compte de stockage, par exemple : `http://<accountname>.blob.core.windows.net/$logs`. Ce conteneur ne peut pas être supprimé une fois Storage Analytics activé, mais son contenu peut l'être.

>[Azure.NOTE] Le conteneur $logs n'est pas affiché lorsqu'une opération d'énumération de conteneurs est exécutée, telle que la méthode [ListContainers](https://msdn.microsoft.com/library/ee758348.aspx). Vous devez y accéder directement. Par exemple, vous pouvez utiliser la méthode [ListBlobs](https://msdn.microsoft.com/library/ee772878.aspx) pour accéder aux objets blob dans le conteneur " $logs ".
À mesure que des demandes sont enregistrées, Storage Analytics télécharge les résultats intermédiaires en tant que blocs. Périodiquement, Storage Analytics valide ces blocs et les rend accessibles sous forme d'objets blob.

Il peut exister des enregistrements en double pour les journaux créés dans la même heure. Vous pouvez déterminer si un enregistrement est un doublon en vérifiant les nombres **RequestId** et **Operation**.

##Conventions d'appellation de journal
Chaque journal est écrit au format suivant :

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log 

Le tableau suivant décrit chaque attribut du nom du journal :

| Attribut      	| Description                                                                                                                                                                                	|
|----------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| <service-name> 	| Nom du service de stockage. Par exemple : blob, table ou file d'attente                                                                                                                        	|
| YYYY           	| Année à quatre chiffres pour le journal. Par exemple : 2011                                                                                                                                         	|
| MM             	| Mois à deux chiffres pour le journal. Par exemple : 07                                                                                                                                           	|
| DD             	| Jour à deux chiffres pour le journal. Par exemple : 07                                                                                                                                           	|
| hh             	| Heure à deux chiffres qui indique l'heure de début pour les journaux, au format UTC 24 heures. Par exemple : 18                                                                                   	|
| mm             	| Nombre à deux chiffres qui indique la minute de début pour les journaux. >[AZURE.NOTE]Cette valeur n'étant pas prise en charge dans la version actuelle de Storage Analytics, elle est toujours égale à 00. 	|
| <counter>      	| Compteur de base zéro à six chiffres qui indique le nombre d'objets blob de journal générés pour le service de stockage durant une période d'une heure. Ce compteur commence à 000000. Par exemple : 000001   	|

Voici un exemple complet de nom de journal qui combine les exemples ci-dessus :

    blob/2011/07/31/1800/000001.log

Voici un exemple d'URI qui peut être utilisé pour accéder au journal ci-dessus :

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log 

Lorsqu'une demande de stockage est enregistrée, le nom du journal qui en résulte correspond à l'heure de fin de l'opération demandée. Par exemple, si une demande GetBlob se termine à 18h30 le 31/07/2011, le journal est écrit avec le préfixe suivant : " blob/2011/07/31/1800/ "

##Métadonnées du journal
Tous les objets blob de journal sont stockés avec des métadonnées qui peuvent être utilisées pour identifier les données de journalisation contenues dans l'objet blob. Le tableau suivant décrit chaque attribut de métadonnées :

| Attribut  	| Description                                                                                                                                                                                                                                               	|
|------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| LogType    	| Décrit si le journal contient des informations relatives aux opérations de lecture, écriture ou suppression. Cette valeur peut inclure un type ou une combinaison des trois, séparés par des virgules.   Exemple 1 : write Exemple 2 : read,write Exemple 3 : read,write,delete 	|
| StartTime  	| Heure la plus antérieure d'une entrée dans le journal, au format AAAA-MM-JJThh:mm:ssZ. Par exemple : 2011-07-31T18:21:46Z                                                                                                                                          	|
| EndTime    	| Heure la plus récente d'une entrée dans le journal, au format AAAA-MM-JJThh:mm:ssZ. Par exemple : 2011-07-31T18:22:09Z                                                                                                                                            	|
| LogVersion 	| Version du format du journal. Actuellement, la seule valeur possible est : 1.0                                                                                                                                                                                 	|

La liste suivante présente un exemple de métadonnées complètes utilisant les exemples ci-dessus :

- LogType=write 

- StartTime=2011-07-31T18:21:46Z 

- EndTime=2011-07-31T18:22:09Z 

- LogVersion=1.0 

##Accès aux données de journalisation

Toutes les données du conteneur " $logs " sont accessibles à l'aide des API de service BLOB, y compris les API .NET fournies par la bibliothèque managée Azure. L'administrateur de compte de stockage peut lire et supprimer des journaux, mais il ne peut pas les créer ou les mettre à jour. Les métadonnées et le nom du journal peuvent être utilisés lors de l'interrogation d'un journal. Il est possible que les journaux d'une heure donnée semblent désordonnés, mais les métadonnées spécifient toujours la fourchette de temps des entrées dans un journal. Par conséquent, vous pouvez utiliser une combinaison de noms de journaux et de métadonnées lors de la recherche d'un journal particulier.

##Étapes suivantes

[Format de journal de Storage Analytics](https://msdn.microsoft.com/library/hh343259.aspx)

[Opérations et messages d'état enregistrés Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx)

[À propos des métriques de Storage Analytics](https://msdn.microsoft.com/library/hh343258.aspx)
<!--HONumber=47-->
