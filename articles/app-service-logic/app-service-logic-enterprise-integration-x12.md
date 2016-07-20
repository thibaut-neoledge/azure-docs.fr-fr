<properties 
	pageTitle="Vue d’ensemble de X12 et d’Enterprise Integration Pack | Microsoft Azure App Service" 
	description="Découvrez comment utiliser les contrats X12 pour créer des applications logiques" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Intégration d’entreprise avec X12 

## Créer un contrat X12 
Avant de pouvoir échanger des messages X12, vous devez créer un contrat X12 et le stocker dans votre compte d’intégration. Les étapes suivantes vous guideront lors du processus de création d’un contrat X12.

### Voici ce dont vous avez besoin pour commencer
- Un [compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) défini dans votre abonnement Azure
- Au moins deux [partenaires](./app-service-logic-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration

>[AZURE.NOTE]Lorsque vous créez un contrat, le contenu du fichier de contrat doit correspondre au type de contrat.


Une fois que vous avez [créé un compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) et [ajouté des partenaires](./app-service-logic-enterprise-integration-partners.md), vous pouvez créer un contrat X12 en suivant ces étapes :

### À partir de la page d’accueil du portail Azure

Une fois que vous êtes connecté au [portail Azure](http://portal.azure.com "Portail Azure") :
1. Sélectionnez **Parcourir** dans le menu à gauche.

>[AZURE.TIP]Si le lien **Parcourir** ne s’affiche pas, vous devrez peut-être développer le menu. Pour ce faire, sélectionnez le lien **Afficher le menu** situé dans le coin supérieur gauche du menu réduit.

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Entrez *intégration* dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Dans le panneau **Integration Accounts** (Comptes d’intégration) qui s’affiche, sélectionnez le compte d’intégration dans lequel vous allez créer le contrat. Si aucun compte d’intégration ne s’affiche, [créez-en un](./app-service-logic-enterprise-integration-accounts.md "Tout sur les comptes d’intégration"). ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Sélectionnez la mosaïque **Contrats**. Si vous ne voyez pas la mosaïque Contrats, commencez par l’ajouter. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)
5. Sélectionnez le bouton **Ajouter** dans le panneau Contrats qui s’affiche. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)
6. Entrez un **nom** pour votre contrat, puis sélectionnez le **type de contrat**, le **partenaire hôte**, **l’identité de l’hôte**, le **partenaire invité**, **l’identité de l’invité**, dans le panneau Contrats qui s’affiche. ![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)

Voici quelques informations qui peuvent s’avérer utiles lorsque vous configurez les paramètres de votre contrat :
  
|Propriété|Description|
|----|----|
|Partenaire hôte|Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire hôte représente l’organisation qui configure le contrat.|
|Identité de l’hôte|Identificateur du partenaire hôte. |
|Partenaire invité|Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire invité représente l’organisation qui travaille avec le partenaire hôte.|
|Identité de l’invité|Identificateur du partenaire invité.|
|Paramètres de réception|Ces propriétés s’appliquent à tous les messages reçus par un contrat|
|Paramètres d’envoi|Ces propriétés s’appliquent à tous les messages envoyés par un contrat|  
Poursuivons :
7. Sélectionnez les **Paramètres de réception** pour configurer la façon dont les messages reçus dans le cadre de ce contrat doivent être traités.
 
 - Si vous le souhaitez, vous pouvez remplacer les propriétés dans le message entrant. Pour ce faire, cochez la case **Override message properties** (Remplacer les propriétés du message).
  - Cochez la case **Le message doit être signé** si vous souhaitez exiger que tous les messages entrants soient signés. Si vous sélectionnez cette option, vous devrez également sélectionner le **certificat** qui sera utilisé pour valider la signature dans les messages.
  - Si vous le souhaitez, vous pouvez aussi exiger que les messages soient chiffrés. Pour ce faire, cochez la case **Le message doit être chiffré**. Vous devrez ensuite sélectionner le **certificat** qui sera utilisé pour décoder les messages entrants.
  - Vous pouvez également exiger que les messages soient compressés. Pour ce faire, cochez la case **Le message doit être compressé**. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)

Consultez le tableau ci-dessous pour en savoir plus sur les fonctionnalités activées par les paramètres de réception.

|Propriété|Description|
|----|----|
|Override message properties (Remplacer les propriétés du message)|Sélectionnez cette option pour indiquer que les propriétés dans les messages reçus peuvent être remplacées |
|Le message doit être signé|Activez cette option pour exiger que les messages soient signés numériquement|
|Le message doit être chiffré|Activez cette option pour exiger que les messages soient chiffrés. Les messages non chiffrés seront rejetés.|
|Le message doit être compressé|Activez cette option pour exiger que les messages soient compressés. Les messages non compressés seront rejetés.|
|Texte du MDN|Il s’agit d’une notification d’état du message (MDN) par défaut à envoyer à l’expéditeur du message|
|Send MDN (Envoyer MDN)|Activez cette option pour autoriser l’envoi de MDN.|
|Send signed MDN (Envoyer MDN signé)|Activez cette option pour exiger que les MDN soient signés.|
|MIC Algorithm (Algorithme MIC)||
|Send asynchronous MDN (Envoyer MDN asynchrone)|Activez cette option pour exiger que les messages soient envoyés de manière asynchrone.|
|URL|Voici l’URL à laquelle les messages seront envoyés.|
Poursuivons :
8. Sélectionnez les **Paramètres d’envoi** pour configurer la façon dont les messages envoyés dans le cadre de ce contrat doivent être traités. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)

Consultez le tableau ci-dessous pour en savoir plus sur les fonctionnalités activées par les paramètres d’envoi.

|Propriété|Description|
|----|----|
|Enable message signing (Activer la signature des messages)|Cochez cette case pour que tous les messages envoyés à partir du contrat soient signés.|
|MIC Algorithm (Algorithme MIC)|Sélectionnez l’algorithme à utiliser lors de la signature du message|
|Certificat|Sélectionnez le certificat à utiliser lors de la signature du message|
|Enable message encryption (Activer le chiffrement du message)|Cochez cette case pour chiffrer tous les messages envoyés à partir de ce contrat.|
|Algorithme de chiffrement|Sélectionnez l’algorithme de chiffrement à utiliser dans le chiffrement du message|
|Dérouler les en-têtes HTTP|Cochez cette case pour dérouler l’en-tête de type de contenu HTTP sur une seule ligne.|
|Exiger le MDN|Cochez cette case pour demander un MDN pour tous les messages envoyés dans le cadre de ce contrat|
|Exiger le MDN signé|Activez cette option pour demander que tous les MDN envoyés à ce contrat soient signés|
|Exiger le MDN asynchrone|Activez cette option pour demander l’envoi d’un MDN asynchrone à ce contrat|
|URL|L’URL à laquelle les MDN seront envoyés|
|Enable NRR (Activer NRR)|Cochez cette case pour activer la non-répudiation de réception|
Nous avons presque terminé !
9. Sélectionnez la mosaïque **Contrats** dans le panneau du compte d’intégration pour afficher le nouveau contrat ajouté. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)



## En savoir plus
- [En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")

<!---HONumber=AcomDC_0706_2016-->