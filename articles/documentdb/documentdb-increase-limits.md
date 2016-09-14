<properties
	pageTitle="Demande d’augmentation des quotas de compte DocumentDB | Microsoft Azure"
	description="Découvrez comment demander un ajustement des quotas de base de données DocumentDB tels que le stockage de documents et le débit par collection."
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
	ms.date="08/25/2016"
	ms.author="anhoh"/>

# Demande d’augmentation des limites de compte DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) dispose d’un jeu de quotas par défaut qui peut être ajusté en contactant le support Azure. Cet article montre comment demander une augmentation du quota.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

-	Quels sont les quotas de base de données DocumentDB qui peuvent être ajustés en contactant le support Azure ?
-	Comment puis-je demander un ajustement de quota de compte DocumentDB ?

##<a id="Quotas"></a> Quotas de compte DocumentDB

Le tableau suivant décrit les quotas de DocumentDB. Les quotas marqués d'un astérisque (*) peuvent être ajustés en contactant le support Azure :

[AZURE.INCLUDE [azure-documentdb-limits](../../includes/azure-documentdb-limits.md)]


##<a id="RequestQuotaIncrease"></a> Demande d’ajustement de quota
Les étapes suivantes montrent comment demander un ajustement de quota.

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Plus de services**, puis sur **Aide + Support**.

	![Capture d'écran de la demande d'aide et de support](media/documentdb-increase-limits/helpsupport.png)

2. Dans le panneau **Aide + Support**, cliquez sur **Nouvelle demande de support**.

	![Capture d'écran de la création d'un ticket de support](media/documentdb-increase-limits/getsupport.png)

3. Dans le panneau **Nouvelle demande de prise en charge**, cliquez sur **De base**. Définissez ensuite **Type de problème** sur **Quota**, **Abonnement** sur votre abonnement qui héberge votre compte DocumentDB, **Type de quota** sur **DocumentDB** et **Plan de support** sur **Support quota - Inclus**. Cliquez ensuite sur **Suivant**.

	![Capture d'écran du ticket de support du type de demande](media/documentdb-increase-limits/supportrequest1.png)

4. Dans le panneau **Problème**, choisissez un niveau de gravité et ajoutez des informations sur l’augmentation de votre quota dans **Détails**. Cliquez sur **Next**.

	![Capture d'écran du ticket de support du sélecteur d'abonnement](media/documentdb-increase-limits/supportrequest2.png)

5. Enfin, renseignez vos informations de contact dans le panneau **Coordonnées**, puis cliquez sur **Créer**.

Une fois le ticket de support créé, vous recevrez le numéro de demande de support par e-mail. Vous pouvez également afficher la demande de support en cliquant sur **Gérer les demandes de support** dans le panneau **Aide + support**.

![Capture d'écran du panneau des demandes de support](media/documentdb-increase-limits/supportrequest4.png)


##<a name="NextSteps"></a>Étapes suivantes
- Pour en savoir plus sur DocumentDB, cliquez [ici](http://azure.com/docdb).

<!---HONumber=AcomDC_0831_2016-->