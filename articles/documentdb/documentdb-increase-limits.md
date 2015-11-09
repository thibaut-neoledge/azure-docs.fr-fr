<properties
	pageTitle="Demande d’augmentation des limites de compte DocumentDB | Microsoft Azure"
	description="Découvrez comment demander un ajustement des limites DocumentDB telles que le nombre de collections autorisées, les procédures stockées et les clauses de requête."
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/22/2015"
	ms.author="anhoh"/>

# Demande d’augmentation des limites de compte DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) dispose d’un ensemble d’application de limites et de quotas par défaut. Plusieurs quotas peuvent être ajustés en contactant le support Azure. Cet article montre comment demander une augmentation de limite de compte.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

-	Quels sont les quotas de compte qui peuvent être ajustés en contactant le support Azure ?
-	Comment puis-je demander un ajustement de quota de compte DocumentDB ?

##<a id="AdjustableQuotas"></a> Ajustement des quotas de compte DocumentDB

Le tableau suivant décrit les quotas DocumentDB qui peuvent être ajustés en contactant le support Azure :

|Entité |Quota (offre standard)|
|-------|--------|
|Comptes de base de données |5
|Nombre de procédures stockées, de déclencheurs et de fonctions définies par l'utilisateur par collection |25 de chaque
|Nombre maximal de collections par compte de base de données |100
|Quantité maximale de stockage de documents par base de données (100 collections) |1 To
|Nombre maximal de fonctions définies par l'utilisateur par requête |2
|Nombre maximal de clauses JOIN par requête |5
|Nombre maximal de clauses AND par requête |20
|Nombre maximal de clauses OR par requête |10
|Nombre maximal de valeurs par expression IN |100
|Nombre maximal de points dans un argument de polygone dans une requête ST\_WITHIN |16
|Nombre maximal de créations de collection par minute |5
|Nombre maximal d'opérations de mise à l'échelle par minute |5

##<a id="RequestQuotaIncrease"></a> Demande d’ajustement de quota
Les étapes suivantes montrent comment demander un ajustement de quota.

1. Dans le [portail Azure en version préliminaire](https://portal.azure.com), cliquez sur **Parcourir**, puis sur **Aide + Support**.

	![Capture d'écran de la demande d'aide et de support](media/documentdb-increase-limits/helpsupport.png)

2. Dans le panneau **Aide + Support**, cliquez sur **Obtenir un support**.

	![Capture d'écran de la création d'un ticket de support](media/documentdb-increase-limits/getsupport.png)

3. Dans le panneau **Nouvelle demande de prise en charge**, cliquez sur **De base**. Définissez ensuite **Type de problème** sur **Quota**, **Abonnement** sur votre abonnement qui héberge votre compte DocumentDB, **Service** sur **DocumentDB** et **Plan de support** sur **Support quota - Inclus**. Enfin, cliquez sur **Suivant**.

	![Capture d'écran du ticket de support du type de demande](media/documentdb-increase-limits/supportrequest1.png)

4. Dans le panneau **Problème**, sélectionnez un niveau de gravité. Définissez **Type de problème** sur **DocumentDB** et ajoutez des informations sur l’augmentation de votre quota dans **Détails**. Cliquez sur **Next**.

	![Capture d'écran du ticket de support du sélecteur d'abonnement](media/documentdb-increase-limits/supportrequest2.png)

5. Enfin, renseignez vos informations de contact dans le panneau **Coordonnées**.

	![Capture d'écran du ticket de support du sélecteur de ressources](media/documentdb-increase-limits/supportrequest3.png)

Une fois le ticket de support créé, vous recevrez le numéro de demande de support par e-mail. Vous pouvez également afficher la demande de support en cliquant sur **Gérer les demandes de support** dans le panneau **Aide + support**.

![Capture d'écran du panneau des demandes de support](media/documentdb-increase-limits/supportrequest4.png)


##<a name="NextSteps"></a>Étapes suivantes
- Pour en savoir plus sur DocumentDB, cliquez [ici](http://azure.com/docdb).

<!---HONumber=Nov15_HO1-->