<properties 
	pageTitle="Vue d’ensemble des schémas et d’Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
	description="Découvrez comment utiliser les schémas avec Enterprise Integration Pack et vos applications logiques" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2016" 
	ms.author="deonhe"/>

# En savoir plus sur les schémas et Enterprise Integration Pack  

## Pourquoi utiliser un schéma ?
Vous utilisez des schémas pour confirmer que les documents XML que vous recevez sont valides, ce qui signifie que les documents contiennent les données attendues dans un format prédéfini.

## Ajout d’un schéma
À partir du portail Azure :
1. Sélectionnez **Parcourir** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Sélectionnez le **compte d’intégration** auquel vous ajouterez le schéma ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Sélectionnez la mosaïque **Schémas** ![](./media/app-service-logic-enterprise-integration-schemas/schema-1.png)
5. Sélectionnez le bouton **Ajouter** dans le panneau Schémas qui s’affiche ![](./media/app-service-logic-enterprise-integration-schemas/schema-2.png)
6. Entrez un **nom** pour votre schéma, puis sélectionnez l’icône de dossier située à droite de la zone de texte **Schéma** pour télécharger le fichier de schéma. Une fois le processus de téléchargement terminé, sélectionnez le bouton **OK**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-3.png)
7. Sélectionnez l’icône de notification en forme de *cloche* pour afficher la progression du processus de téléchargement du schéma. ![](./media/app-service-logic-enterprise-integration-schemas/schema-4.png)
8. Sélectionnez la mosaïque **Schémas**. Cette action actualise la mosaïque. Le nombre de schémas doit augmenter, indiquant que le nouveau schéma a été ajouté avec succès. Une fois la mosaïque **Schémas** sélectionnée, le nouveau schéma ajouté doit également s’afficher dans le panneau Schémas à droite. ![](./media/app-service-logic-enterprise-integration-schemas/schema-5.png)


## Utilisation des schémas
- Les schémas sont utilisés pour valider les messages échangés dans un scénario d’entreprise à entreprise (B2B).

## Modification des schémas
1. Sélectionnez la mosaïque **Schémas**
2. Sélectionnez le schéma que vous souhaitez modifier dans le panneau Schémas qui s’affiche
3. Sélectionnez le lien **Télécharger** dans le panneau Schémas ![](./media/app-service-logic-enterprise-integration-schemas/edit-1.png)
4. Sélectionnez le fichier de schéma que vous souhaitez télécharger à l’aide de la boîte de dialogue de sélection de fichier qui s’ouvre.
5. Sélectionnez **Ouvrir** dans le sélecteur de fichiers ![](./media/app-service-logic-enterprise-integration-schemas/edit-2.png)
6. Vous recevrez une notification indiquant que le téléchargement a réussi ![](./media/app-service-logic-enterprise-integration-schemas/edit-3.png)

## Suppression de schémas
1. Sélectionnez la mosaïque **Schémas**
2. Sélectionnez le schéma que vous souhaitez supprimer dans le panneau Schémas qui s’affiche
3. Sélectionnez le lien **Supprimer** dans la barre de menu du panneau Schémas ![](./media/app-service-logic-enterprise-integration-schemas/delete-1.png)
4. Si vous voulez vraiment supprimer le schéma sélectionné, choisissez **Oui** dans la boîte de dialogue Supprimer un schéma pour confirmer votre choix ![](./media/app-service-logic-enterprise-integration-schemas/delete-2.png)
5. Enfin, notez que la liste des schémas dans le panneau Schémas s’actualise et que le schéma que vous avez supprimé n’est plus répertorié ![](./media/app-service-logic-enterprise-integration-schemas/delete-3.png)

## Étapes suivantes

- [En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")

      

<!---HONumber=AcomDC_0803_2016-->