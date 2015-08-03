<properties 
	pageTitle="Demande d’augmentation des limites de compte DocumentDB | Azure" 
	description="Découvrez comment demander un ajustement des limites DocumentDB telles que le nombre de collections autorisées, les procédures stockées et les clauses de requête." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="stbaro"/>

# Demande d’augmentation des limites de compte DocumentDB

[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) dispose d’un ensemble d’application de limites et de quotas par défaut. Plusieurs quotas peuvent être ajustés en contactant le support Azure. Cet article montre comment demander une augmentation de limite de compte.

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
|Nombre maximal de fonctions définies par l'utilisateur par requête |1
|Nombre maximal de clauses JOIN par requête |2
|Nombre maximal de clauses AND par requête |5
|Nombre maximal de clauses OR par requête |5
|Nombre maximal de valeurs par expression IN |100
|Nombre maximal de créations de collection par minute |5
|Nombre maximal d'opérations de mise à l'échelle par minute |5

##<a id="RequestQuotaIncrease"></a> Demande d’ajustement de quota
Les étapes suivantes montrent comment demander un ajustement de quota.

1. Dans le [portail Azure en version préliminaire](https://portal.azure.com), cliquez sur **Parcourir**, puis sur **Aide + Support**.

	![Capture d'écran de la demande d'aide et de support](media/documentdb-increase-limits/helpsupport.png)

2. Dans le panneau **Aide + Support**, cliquez sur **Obtenir un support**.

	![Capture d'écran de la création d'un ticket de support](media/documentdb-increase-limits/getsupport.png)

3. Dans le panneau **Nouvelle demande de support**, cliquez sur **Type de demande** puis, dans le panneau **Type de demande**, cliquez sur **Quotas**.

	![Capture d'écran du ticket de support du type de demande](media/documentdb-increase-limits/supportrequest1.png)

4. Dans le panneau **Abonnement**, choisissez l’abonnement qui héberge votre compte DocumentDB.

	![Capture d'écran du ticket de support du sélecteur d'abonnement](media/documentdb-increase-limits/supportrequest2.png)

5. Dans le panneau **Ressources**, choisissez **Comptes DocumentDB**.

	![Capture d'écran du ticket de support du sélecteur de ressources](media/documentdb-increase-limits/supportrequest3.png)

6. Dans le panneau **Plan de support** lame, choisissez **Support gratuit des quotas**.

	![Capture d'écran du ticket de support du sélecteur de plan de support](media/documentdb-increase-limits/supportrequest4.png)

7. Dans le panneau **Problème** lame, choisissez la catégorie de problème **Demande d’augmentation principale ou de quotas DocumentDB**.

	![Capture d'écran du ticket de support du sélecteur de catégorie de problème](media/documentdb-increase-limits/supportrequest5.png)

8. Dans le panneau **Description**, saisissez une description de la demande. Veillez à inclure les ajustements de quota spécifiques dont vous faites la demande ainsi que les comptes pour lesquels vous souhaitez ces ajustements.

	![Capture d'écran du ticket de support de la zone de texte de description](media/documentdb-increase-limits/supportrequest6.png)

9. Cliquez sur **Create**.

	![Capture d'écran du bouton de création du ticket de support](media/documentdb-increase-limits/supportrequest7.png)

Une fois le ticket de support créé, vous recevrez le numéro de demande de support par e-mail. Vous pouvez également afficher la demande de support en cliquant sur **Demandes de support** dans le panneau **Help + support**.

![Capture d'écran du panneau des demandes de support](media/documentdb-increase-limits/supportrequest8.png)
  

##<a name="NextSteps"></a>Étapes suivantes
- Pour en savoir plus sur DocumentDB, cliquez [ici](http://azure.com/docdb).
 

<!---HONumber=July15_HO4-->