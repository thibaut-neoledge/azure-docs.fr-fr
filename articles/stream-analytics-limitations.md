<properties 
	pageTitle="Limitations de la version préliminaire de Stream Analytics | Azure" 
	description="Découvrez les limitations de la version préliminaire publique d'Azure Stream Analytics" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/10/2015" 
	ms.author="jgao"/>

#Limitations et problèmes connus de la version préliminaire d'Azure Stream Analytics

Ce document décrit les limitations et les problèmes connus de la version préliminaire d'[Azure Stream Analytics][stream.analytics.documentation].  Dans la plupart des cas ces limites ont soit été fixées dans l'intention de recevoir vos commentaires et suggestions, soit suite à des contraintes liées à la capacité actuelle. 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##Dans cet article
+ [Limitations](#limitations) 
+ [Notes de publication et problèmes connus](#knownissues)
+ [Étapes suivantes]

##<a name="limitations"></a> Limites

###Disponibilité régionale
Les travaux de la version préliminaire de Stream Analytics peuvent uniquement être configurés dans les régions États-Unis et Europe de l'ouest.

###Mise à l'échelle 
**Quota d'unité de diffusion en continu**

En raison des contraintes de capacité actuelles, nous avons dû appliquer un quota de 12 unités de diffusion par région, par abonnement. Pour plus d'informations, consultez [Mise à l'échelle des travaux d'Azure Stream Analytics][stream.analytics.scale.jobs]. Si vous souhaitez assouplir cette limite, veuillez contacter le [Support Microsoft][microsoft.support]. Nous ferons de notre mieux pour vous proposer une solution respectant les contraintes de l'offre publique. 

**Utilisation d'une unité de diffusion en continu**

Dans cette version préliminaire, le nombre d'unités de diffusion en continu fournies pour un travail peut parfois être supérieur à la quantité sélectionnée ou facturée.  En outre, les unités de diffusion en continu ne sont pas limitées, ce qui signifie que les performances observées peuvent être supérieures à celles garanties selon la disponibilité des ressources de calcul.

**Clé de partition**

Lors de la mise à l'échelle de votre requête avec PARTITION BY, le champ de la partition doit être PartitionId.  Le partitionnement sur d'autres champs définis par l'utilisateur sera activé dans une version ultérieure.
Pour plus d'informations sur la mise à l'échelle de votre travail, consultez [Mise à l'échelle des travaux Azure Stream Analytics][stream.analytics.scale.jobs].

###Entrées



**Encodage de caractères**

Le seul format d'encodage pris en charge pour les sources d'entrée CSV et JSON est UTF-8.


###Complexité de requête
Le nombre maximal de fonctions d'agrégation prises en charge dans une définition de requête de travail Stream Analytics est 7.

###Gestion du cycle de vie

**Mise à niveau d'un travail**

Pour le moment, Stream Analytics ne prend pas en charge les modifications de définition ou de configuration en direct d'un travail en cours d'exécution.  Pour modifier l'entrée, la sortie, la requête, la mise à l'échelle ou la configuration d'un travail en cours d'exécution, vous devez tout d'abord l'arrêter.

**Arrêt et redémarrage d'un travail**

L'arrêt d'un travail n'entraîne pas la conservation d'un état pour les derniers événements traités par ce travail, ce qui signifie qu'il n'existe actuellement aucun moyen de configurer un travail redémarré pour qu'il reprenne là où il s'est arrêté.  Cette limitation sera résolue dans une version ultérieure.  Pour obtenir des recommandations sur le démarrage et arrêt des travaux, consultez le [Guide de développement pour Azure Stream Analytics][stream.analytics.developer.guide]. 

###Surveillance
Certaines mesures relatives à l'utilisation et aux performances d'un travail, telles que la latence, ne sont pas disponibles dans la version préliminaire.  De même, la version préliminaire mesure uniquement le débit d'un travail en termes de nombre d'événements, pas en taille.

##<a name="knownissues"></a>Notes de publication / problèmes connus

Voici une liste des problèmes connus touchant Azure Stream Analytics. Cette section peut évoluer à mesure que nous supprimons des éléments de la liste, que nous rencontrons de nouveaux problèmes, ou que nous en savons plus sur les problèmes existants.


###Autorisations élevées requises pour le concentrateur d'événements
Pour le moment, Stream Analytics requiert une stratégie d'accès partagé avec des autorisations de gestion pour les sources d'entrée et de sortie du concentrateur d'événements.

###Retard dans la configuration du compte Azure Storage
Si vous créez un travail Stream Analytics dans une région pour la première fois, vous devrez créer un compte de stockage ou indiquer un compte existant pour la surveillance des travaux Stream Analytics de cette région.  En raison d'une latence dans la configuration de la surveillance, la création d'autres travaux Stream Analytics dans la même région dans les 30 minutes entraînera une invitation à indiquer un second compte de stockage au lieu d'afficher celui que vous venez de configurer dans le menu déroulant Création d'un autre travail d'analyse de flux dans les 30 minutes dans la même région invite en raison de la latence dans la configuration de l'analyse, la spécification d'un deuxième compte de stockage au lieu de celle récemment configuré dans la liste déroulante Surveillance de compte de stockage.  Pour éviter la création d'un compte de stockage inutile, attendez 30 minutes après votre première création de travail dans une région, puis procédez à la configuration des travaux supplémentaires dans cette région. 

###Les travaux utilisent un groupe de consommateurs par défaut pour le concentrateur d'événements
Lorsqu'un concentrateur d'événements est spécifié en tant qu'entrée, les travaux Stream Analytics utilisent le groupe de consommateurs par défaut pour récupérer les données à partir du concentrateur d'événements.  Procéder ainsi sans configuration supplémentaire empêche les autres récepteurs d'accéder au concentrateur d'événements.  Pour permettre à un concentrateur d'événements d'avoir plusieurs récepteurs, vous devez configurer d'autres groupes de consommateurs.  Pour en savoir plus, consultez le [guide de développement de Concentrateurs d'événements][azure.event.hubs.developer.guide].

###Ajout d'une entrée/sortie : concentrateur d'événements 
La troisième page des boîtes de dialogue Ajouter une entrée et Ajouter une sortie aux sources du concentrateur d'événements est dotée d'une liste déroulante intitulée Concentrateur d'événements. Elle contient une liste d'espaces de noms Service Bus pour l'abonnement actuel, ainsi qu'une option permettant de se connecter à un concentrateur d'événements dans un autre abonnement.  Si vous souhaitez vous connecter à un concentrateur d'événements dans le même abonnement, sélectionnez son espace de noms Service Bus ici.  Si vous souhaitez vous connecter à un concentrateur d'événements dans un autre abonnement, sélectionnez " Utiliser le concentrateur d'événements à partir d'un autre abonnement ".  


###Impossible de référencer plusieurs fois la même étape de requête
Cette version préliminaire ne permet pas de référencer plusieurs fois une étape donnée de requête secondaire définie à l'aide du mot clé WITH.  Cela peut avoir un impact sur un scénario courant tel qu'une jointure réflexive utilisant des alias de la même étape.  Pour contourner ce problème, veuillez créer deux étapes distinctes avec la même requête secondaire et des noms différents.

###Des conversions de type non prises en charge entraînent des valeurs NULL
Toute valeur d'événement dont les conversions de type ne sont pas prises en charge dans la section Types de données des [Références sur le langage des requêtes d'Azure Stream Analytics][stream.analytics.query.language.reference] entraîne une valeur NULL.  Aucune journalisation d'erreur n'est mise en place dans cette version préliminaire pour ce type d'exception de conversion. 

###Problème de mémoire insuffisante
Les travaux Streaming Analytics ayant un intervalle de temps de tolérance important pour les événements arrivant en désordre et/ou des requêtes complexes tenant à jour un grand nombre d'états peuvent entraîner un manque de mémoire, ce qui provoque leur redémarrage. Les opérations de démarrage et d'arrêt seront visibles dans les journaux des opérations du travail.  Pour éviter ce comportement, procédez à une mise à l'échelle sur plusieurs partitions.  Dans une version ultérieure, cette limitation sera traitée par la dégradation des performances des travaux affectés au lieu de les redémarrer.

###Une partition de concentrateur d'événements vide dans une requête partitionnée entraîne une absence de sortie
Lorsque vous exécutez une requête partitionnée comportant une requête secondaire non partitionnée en tant que la deuxième étape, si l'une des partitions du concentrateur d'événements de l'entrée est vide, la requête ne génère pas de résultats. Cela sera signalé par une erreur dans les journaux des opérations du travail.  Pour éviter ce problème, assurez-vous que toutes les partitions du concentrateur d'événements ont des événements entrants en permanence.

###Limitation du volume d'événements de base de données SQL
Lorsque vous utilisez une base de données SQL comme source de sortie, les volumes de données de sortie particulièrement importants peuvent provoquer une expiration du délai du travail Stream Analytics. Pour résoudre ce problème, réduire le volume de sortie à l'aide d'opérateurs d'agrégation ou de jointure, ou choisissez des objets blob de stockage ou un concentrateur d'événements comme source de sortie.

###Les entrées d'objets blob volumineux ne sont pas prises en charge
L'utilisation de fichiers d'objets blob de stockage volumineux peut entraîner l'arrêt imprévu de vos travaux Stream Analytics.  Pour éviter ce problème, faites en sorte que la taille de chaque objet blob ne dépasse pas 10 Mo.

###LEFT OUTER JOIN n'est pas prise en charge
L'opération LEFT OUTER JOIN est activée dans le langage des requêtes Stream Analytics mais n'est pas prise en charge.  Exécution d'une requête contenant LEFT OUTER JOIN pendant plusieurs minutes entraîne des problèmes de consommation de mémoire.  Nous vous recommandons de ne pas utiliser cette opération pour les scénarios plus complexes qu'une expérimentation de requête à durée de vie limitée.  Cette limitation sera résolue dans une version ultérieure.


##<a name="nextsteps"></a>Étapes suivantes

- [Présentation d'Azure Stream Analytics][stream.analytics.introduction]
- [Prise en main d'Azure Stream Analytics][stream.analytics.get.started]
- [Mise à l'échelle des travaux Azure Stream Analytics][stream.analytics.scale.jobs]
- [Limitations et problèmes connus d'Azure Stream Analytics][stream.analytics.limitations]
- [Références sur le langage des requêtes d'Azure Stream Analytics][stream.analytics.query.language.reference]
- [Références sur l'API REST de gestion d'Azure Stream Analytics][stream.analytics.rest.api.reference]

<!--Anchors-->
[Limites]: #Limitations
[Notes de publication et problèmes connus]: #Release-notes-and-known-issues
[Étapes suivantes]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.limitations]: ../stream-analytics-limitations/


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[Lien 1 vers une autre rubrique de documentation azure.microsoft.com]: ../virtual-machines-windows-tutorial/
[Lien 2 vers une autre rubrique de documentation azure.microsoft.com]: ../web-sites-custom-domain-name/
[Lien 3 vers une autre rubrique de documentation azure.microsoft.com]: ../storage-whatis-account/

<!--HONumber=46--> 
