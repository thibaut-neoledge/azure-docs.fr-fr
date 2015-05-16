<properties 
	pageTitle="Notes de publication de Stream Analytics | Azure" 
	description="Notes de publication de Stream Analytics GA" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#Retard dans la configuration du compte Azure Storage

Si vous créez un travail Stream Analytics dans une région pour la première fois, vous devez créer un compte de stockage ou indiquer un compte existant pour la surveillance des travaux Stream Analytics de cette région. En raison d’une latence dans la configuration de la surveillance, la création d’autres travaux Stream Analytics dans la même région dans les 30 minutes entraîne une invitation à indiquer un second compte de stockage au lieu d’afficher celui que vous venez de configurer dans la liste déroulante Surveillance de compte de stockage. Pour éviter la création d’un compte de stockage inutile, attendez 30 minutes après votre première création de travail dans une région, puis procédez à la configuration des travaux supplémentaires dans cette région.

##Mise à niveau d'un travail

Pour le moment, Stream Analytics ne prend pas en charge les modifications de définition ou de configuration en direct d’un travail en cours d’exécution. Pour modifier l'entrée, la sortie, la requête, la mise à l'échelle ou la configuration d'un travail en cours d'exécution, vous devez tout d'abord l'arrêter.

##Types de données déduits de la source d’entrée

Si une instruction **CRÉER UNE TABLE** n’est pas utilisée, le type d’entrée est dérivé du format d’entrée, par exemple tous les champs du CSV sont des chaînes. Les champs doivent être explicitement convertis au type correct à l’aide de la fonction CAST pour éviter les erreurs d’incompatibilité des types.

##Les champs manquants sont renvoyés comme valeurs nulles.

Faire référence à un champ qui n’est pas présent dans la source d’entrée génère des valeurs nulles dans l’événement de sortie.

##Les instructions AVEC doivent précéder les instructions SÉLECTIONNER

Dans votre requête, les instructions SÉLECTIONNER doivent suivre les sous-requêtes définies dans les instructions AVEC.

##Problème de mémoire insuffisante

Les travaux Streaming Analytics ayant une tolérance importante pour les événements arrivant en désordre et/ou les requêtes complexes tenant à jour un grand nombre d’états, cela peut entraîner un manque de mémoire et provoquer leur redémarrage. Les opérations de démarrage et d’arrêt sont visibles dans les journaux des opérations du travail. Pour éviter ce comportement, procédez à une mise à l'échelle sur plusieurs partitions. Dans une version ultérieure, cette limitation sera traitée par la dégradation des performances des travaux affectés plutôt que par leur redémarrage.

##Les entrées importantes d’objets blob sans horodatage de la charge utile peuvent provoquer un problème d’insuffisance de mémoire

L’absorption de fichiers volumineux depuis le stockage d’objets blob peut entraîner le blocage de Stream Analytics si un champ d’horodatage n’est pas spécifié via HORODATAGE PAR. Pour éviter ce problème, faites en sorte que la taille de chaque objet blob ne dépasse pas 10 Mo.

##Limitation du volume d'événements de base de données SQL

Lorsque vous utilisez une base de données SQL comme cible de sortie, des volumes de données de sortie particulièrement importants peuvent provoquer une expiration du délai du travail Stream Analytics. Pour résoudre ce problème, réduisez le volume de sortie à l’aide d’opérateurs d’agrégation ou de filtrage, ou choisissez le service de stockage d’objets blob Azure ou Event Hubs comme cible de sortie.

##Les jeux de données Power BI ne peuvent contenir qu’une seule table

Power BI ne peut pas prendre en charge plus d’une table dans un jeu de données.

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d’Azure Stream Analytics](stream.analytics.get.started.md)
- [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=52-->
