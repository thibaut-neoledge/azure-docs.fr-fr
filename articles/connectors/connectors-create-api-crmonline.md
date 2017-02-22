---
title: "Ajouter le connecteur Dynamics 365 (Online) à vos applications Azure Logic Apps | Microsoft Docs"
description: "Créez des applications logiques avec Azure App Service. Le fournisseur de connexion Dynamics 365 (Online) fournit une API permettant de travailler avec les entités sur Dynamics 365 (Online)."
services: logic-apps
cloud: Azure Stack
documentationcenter: 
author: Mattp123
manager: anneta
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp
translationtype: Human Translation
ms.sourcegitcommit: fa426f474f4efd4023da5dfd9954dacf96f635ab
ms.openlocfilehash: 99d5379ad1e6965dd9ed88de456cc850d7e40d5a


---
# <a name="create-a-logic-app-with-the-dynamics-365-connector"></a>Créer une application logique avec le connecteur Dynamics 365

Avec les applications Logic Apps, vous pouvez vous connecter à Dynamics 365 (Online) et créer des flux d’activité utiles qui génèrent de nouveaux enregistrements, mettent à jour les éléments ou renvoient une liste d’enregistrements. Avec le connecteur Dynamics 365, vous pouvez effectuer les opérations suivantes :

* Créer votre flux d’activité en fonction des données que vous obtenez de Dynamics 365 (Online).
* Utiliser les actions qui obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand un élément est mis à jour dans Dynamics 365 (Online), vous pouvez envoyer un courrier électronique à l’aide d’Office 365.

Cette rubrique vous explique comment créer une application logique qui génère une tâche dans Dynamics 365 lorsqu’un prospect est créé dans Dynamics 365.

## <a name="prerequisites"></a>Composants requis
* Un compte Azure.
* Un compte Dynamics 365 (Online).

## <a name="walkthrough-create-a-task-whenever-a-new-lead-is-created-in-dynamics-365"></a>Procédure pas à pas : Créer une tâche lorsqu’un prospect est créé dans Dynamics 365
1.    [Connectez-vous à Azure](https://portal.azure.com).
2.    Saisissez *Logic Apps* dans la zone de **recherche**, puis appuyez sur ENTRÉE.
3.    Dans la zone de service d’application logique, cliquez sur **Ajouter**.

  ![LogicApp - Ajouter](./media/connectors-create-api-crmonline/add-logic-app.png)

4.    Renseignez les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement** pour créer l’objet d’application logique, puis cliquez sur **Créer**.

5.    Sélectionnez la nouvelle application logique. Lorsque vous recevez la notification **Déploiement réussi**, cliquez sur **Actualiser**.

6.    Sous Outils de développement, cliquez sur **Concepteur d’application logique**, puis cliquez sur **LogicApp vide** dans la liste des modèles disponibles.

7.    Saisissez *Dynamics 365*. Plusieurs déclencheurs Dynamics 365 se trouvent dans la liste, cliquez sur **Dynamics 365 – L’enregistrement est créé**.

8.    Si vous êtes invité à vous connecter à Dynamics 365, faites-le maintenant.

9.    Entrez les informations suivantes dans les détails du déclencheur.

  * **Nom de l’organisation**. Sélectionnez l’instance de Dynamics 365 que l’application logique doit écouter.

  * **Nom de l’entité**. Sélectionnez l’entité que vous souhaitez écouter, qui se comportera comme un déclencheur pour lancer l’application logique. Dans cette procédure pas à pas, **Prospects** est sélectionné.

  * **How often do you want to check for items?** (À quelle fréquence souhaitez-vous rechercher des éléments ?) Ces valeurs définissent la fréquence à laquelle l’application logique recherche des mises à jour pour le déclencheur. Le paramètre par défaut consiste à rechercher des mises à jour toutes les trois minutes.

    * **Fréquence**. Sélectionnez les secondes, les minutes, les heures ou les jours.

    * **Intervalle**. Entrez un chiffre qui indique le nombre de secondes, de minutes, d’heures ou de jours avant la recherche suivante.

    ![Application logique - Détails sur le déclencheur](./media/connectors-create-api-crmonline/trigger-details.png)

10.    Cliquez sur **Nouvelle étape**, puis sur **Ajouter une action**.

11.    Saisissez *Dynamics 365* et dans la liste, cliquez sur **Dynamics 365 – Créer un enregistrement**.

12.    Entrez les informations ci-après.
  * **Nom de l’organisation**. Sélectionnez l’instance de Dynamics 365 dans laquelle vous souhaitez que le flux crée l’enregistrement. Notez qu’il ne s’agit pas nécessairement de la même instance que celle depuis laquelle l’évènement est déclenché.
  * **Nom de l’entité**. Sélectionnez l’entité dans laquelle vous souhaitez créer un enregistrement lorsque l’événement est déclenché. Dans cette procédure pas à pas, **Tâches** est sélectionné.

13.    Une zone Objet apparaît. Lorsque vous cliquez dans la zone, un volet de contenu dynamique s’affiche et vous pouvez sélectionner l’un des champs suivants.
  * **Nom**. Sélectionner ce champ permettra d’insérer le nom du prospect dans le champ Objet de la tâche, lorsque l’enregistrement de la tâche est créé.
  * **Rubrique**. Sélectionner ce champ permettra d’insérer le champ Rubrique pour le prospect dans le champ Objet de la tâche, lorsque l’enregistrement de la tâche est créé.
Cliquez sur **Rubrique** pour l’ajouter à la zone **Objet**.

  ![Application logique - Détails Créer un enregistrement](./media/connectors-create-api-crmonline/create-record-details.png)

14.    Cliquez sur **Enregistrer** dans la barre d’outils du Concepteur d’application logique.

  ![Barre d’outils du Concepteur d’application logique - Enregistrer](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15.    Pour démarrer l’application logique, cliquez sur **Exécuter**.

  ![Barre d’outils du Concepteur d’application logique - Enregistrer](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Créez maintenant un enregistrement de prospect dans Dynamics 365 pour les ventes et observez votre flux en action !

## <a name="using-advanced-options"></a>Utilisation des options avancées
Lorsque vous ajoutez une étape à une application logique, cliquer sur **Afficher les options avancées** vous permet de contrôler comment les données sont filtrées pour l’étape en ajoutant un filtre ou une requête de tri.

Par exemple, vous pouvez utiliser une requête de filtre pour récupérer uniquement les comptes actifs et trier par nom du compte. Pour ce faire, entrez la requête de filtre OData **statuscode eq 1** et sélectionnez **Nom de compte** à partir du volet de contenu dynamique. Plus d’informations : [MSDN : $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) et [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

  ![LogicApp - Options avancées](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Meilleures pratiques lors de l’utilisation des options avancées
Notez que lorsque vous ajoutez une valeur à un champ, vous devez faire correspondre le type de champ si vous saisissez une valeur ou le sélectionner dans le contenu dynamique qui est affiché.

Type de champ  |Utilisation  |Comment y accéder  |Nom  |Type de données  
---------|---------|---------|---------|---------
Champs de texte|Les champs de texte nécessitent une seule ligne de texte ou du contenu dynamique qui est un champ de type texte. Exemples : Catégorie et Sous-catégorie.|Paramètres > Personnalisations > Personnaliser le système > Entités > Tâche > Champs |category |Ligne de texte unique.       
Champs de type entier | Certains champs nécessitent un entier ou un contenu dynamique qui est un champ de type entier. Exemples : Pourcentage achevé et Durée. |Paramètres > Personnalisations > Personnaliser le système > Entités > Tâche > Champs |percentcomplete |Nombre entier         
Champs de date | Certains champs nécessitent qu’une date soit saisie au format mm/jj/aaaa ou un contenu dynamique qui est un champ de type date. Exemples : Créé le, Date de début, Début réel, Dernière durée de suspension, Fin réelle et Date d’échéance. | Paramètres > Personnalisations > Personnaliser le système > Entités > Tâche > Champs |createdon |Date et heure         
Champs qui nécessitent à la fois un ID d’enregistrement et un type de recherche |Certains champs qui font référence à un autre enregistrement d’entité nécessitent l’ID d’enregistrement et le type de recherche. |Paramètres > Personnalisations > Personnaliser le système > Entités > Compte > Champs  | accountid   | Clé primaire

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Exemples de champs supplémentaires nécessitant un ID d’enregistrement et un type de recherche
Pour compléter le tableau précédent, voici des exemples de champs supplémentaires qui ne fonctionnent pas avec les valeurs sélectionnées dans la liste de contenu dynamique. À la place, ces champs nécessitent un ID d’enregistrement et un type de recherche saisis dans les champs de PowerApps.  
*  Propriétaire et Type de propriétaire. Le champ Propriétaire doit être un ID d’enregistrement d’utilisateur ou d’équipe valide. Le champ Type de propriétaire doit être **systemusers** ou **équipes**.
* Client et Type de client. Le champ Client doit être un ID d’enregistrement de compte ou de contact valide. Le champ Type de propriétaire doit être **comptes** ou **contacts**.
* Concernant et Type Concernant. Le champ Concernant doit être un ID d’enregistrement valide, tel qu’un ID d’enregistrement de contact ou de compte. Le champ Type Concernant doit être le type de recherche pour l’enregistrement, tel que **comptes** ou **contacts**.

L’exemple d’action de création de tâche suivant ajoute un enregistrement de compte qui correspond à l’ID d’enregistrement en l’ajoutant au champ Concernant de la tâche.

  ![Flux : ID d’enregistrement et type de compte](./media/connectors-create-api-crmonline/recordid-type-account.png)

Cet exemple affecte également la tâche à un utilisateur spécifique en fonction de l’ID d’enregistrement d’utilisateur.
  ![Flux : ID d’enregistrement et type de compte](./media/connectors-create-api-crmonline/recordid-type-user.png)

Pour rechercher un ID d’enregistrement, consultez la section *Recherche de l’ID d’enregistrement* ci-dessous.

## <a name="find-the-record-id"></a>Recherche de l’ID d’enregistrement
1. Ouvrez un enregistrement, comme un enregistrement de compte.

2. Dans la barre d’outils d’actions, cliquez sur **Ouvrir dans une nouvelle fenêtre** ![enregistrement - fenêtre indépendante](./media/connectors-create-api-crmonline/popout-record.png).
Vous pouvez également cliquer sur **ENVOYER UN LIEN PAR COURRIER ÉLECTRONIQUE** dans la barre d’outils d’actions pour copier l’URL complète dans votre programme de messagerie par défaut.

3. L’ID d’enregistrement est affiché entre les caractères d’encodage %7b et %7d de l’URL.

  ![Flux : ID d’enregistrement et type de compte](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Résolution des problèmes
Pour résoudre les problèmes qui peuvent se produire lors d’une étape dans une application logique, affichez les détails de l’état de l’événement.

1. Dans la zone des applications logiques, cliquez sur votre application logique, puis sur **Vue d’ensemble**. La zone Résumé s’affiche et indique l’état d’exécution de l’application logique. En cas de problèmes lors des exécutions, cliquez sur l’événement ayant rencontré des problèmes pour lequel vous souhaitez avoir plus d’informations.

  ![LogicApp - Résoudre les problèmes - Étape 1](./media/connectors-create-api-crmonline/tshoot1.png)

2. Cliquez sur l’étape lors de laquelle des problèmes se sont présentés pour la développer.

  ![LogicApp - Résoudre les problèmes - Étape 2](./media/connectors-create-api-crmonline/tshoot2.png)

3. Les détails de l’étape s’affichent et peuvent aider à résoudre la cause du problème.

    ![LogicApp - Résoudre les problèmes - Étape 2](./media/connectors-create-api-crmonline/tshoot3.png)

Pour plus d’informations sur la résolution des problèmes relatifs aux applications logiques, consultez [Diagnostic des échecs d’applications logiques](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="technical-details"></a>Détails techniques
## <a name="triggers"></a>Déclencheurs
| Déclencheur | Description |
| --- | --- |
| Lorsqu’un enregistrement est créé |Déclenche un flux quand un objet est créé dans Dynamics 365. |
| Lorsqu’un enregistrement est mis à jour |Déclenche un flux quand un objet est modifié dans Dynamics 365. |
| Lorsqu’un enregistrement est supprimé |Déclenche un flux quand un objet est supprimé dans Dynamics 365. |

## <a name="actions"></a>Actions
| Action | Description |
| --- | --- |
| Répertorier les enregistrements |Cette opération récupère les enregistrements d’une entité. |
| Créer un enregistrement |Cette opération crée un enregistrement pour une entité. |
| Obtenir l’enregistrement |Cette opération récupère l’enregistrement spécifié pour une entité. |
| Supprimer un enregistrement |Cette opération supprime un enregistrement à partir d’une collection d’entités. |
| Mise à jour d’un enregistrement |Cette opération met à jour un enregistrement existant pour une entité. |

### <a name="trigger-and-action-details"></a>Détail des déclencheurs et des actions
Dans cette section, consultez les détails relatifs à chacun des déclencheurs et actions, y compris toutes les propriétés d’entrée requises ou facultatives et toute sortie correspondante associée au connecteur.

#### <a name="when-a-record-is-created"></a>Lorsqu’un enregistrement est créé
Déclenche un flux quand un objet est créé dans Dynamics 365.

| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| dataset* |Nom de l’organisation |Nom de l’organisation Dynamics 365, comme Contoso |
| table* |Nom de l’entité |Nom de l’entité |
| $filter |Requête de filtre |Requête de filtre ODATA pour limiter le nombre d’entrées renvoyées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-updated"></a>Lorsqu’un enregistrement est mis à jour
Déclenche un flux quand un objet est modifié dans Dynamics 365.

| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| dataset* |Nom de l’organisation |Nom de l’organisation Dynamics 365, comme Contoso |
| table* |Nom de l’entité |Nom de l’entité |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-deleted"></a>Lorsqu’un enregistrement est supprimé
Déclenche un flux quand un objet est supprimé dans Dynamics 365.

| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| dataset* |Nom de l’organisation |Nom de l’organisation Dynamics 365, comme Contoso |
| table* |Nom de l’entité |Nom de l’entité |


Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

#### <a name="list-records"></a>Répertorier les enregistrements
Cette opération récupère les enregistrements d’une entité.

| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| dataset* |Nom de l’organisation |Nom de l’organisation Dynamics 365, comme Contoso |
| table* |Nom de l’entité |Nom de l’entité |
| $filter |Requête de filtre |Requête de filtre ODATA pour limiter le nombre d’entrées renvoyées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

#### <a name="create-a-new-record"></a>Créer un enregistrement
Cette opération crée un enregistrement pour une entité.

| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| dataset* |Nom de l’organisation |Nom de l’organisation Dynamics 365, comme Contoso |
| table* |Nom de l’entité |Nom de l’entité |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="get-record"></a>Obtenir l’enregistrement
Cette opération récupère l’enregistrement spécifié pour une entité.

| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| dataset* |Nom de l’organisation |Nom de l’organisation Dynamics 365, comme Contoso |
| table* |Nom de l’entité |Nom de l’entité |
| id* |Identificateur d’élément |Indiquez l’identificateur de l’enregistrement |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="delete-a-record"></a>Supprimer un enregistrement
Cette opération supprime un enregistrement à partir d’une collection d’entités.

| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| dataset* |Nom de l’organisation |Nom de l’organisation Dynamics 365, comme Contoso |
| table* |Nom de l’entité |Nom de l’entité |
| id* |Identificateur d’élément |Indiquez l’identificateur de l’enregistrement |

Un astérisque (*) signifie que la propriété est requise.

#### <a name="update-a-record"></a>Mise à jour d’un enregistrement
Cette opération met à jour un enregistrement existant pour une entité.

| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| dataset* |Nom de l’organisation |Nom de l’organisation Dynamics 365, comme Contoso |
| table* |Nom de l’entité |Nom de l’entité |
| id* |Identificateur de l’enregistrement |Indiquez l’identificateur de l’enregistrement |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

## <a name="http-responses"></a>Réponses HTTP
Les actions et déclencheurs peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants :

| Name | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="next-steps"></a>Étapes suivantes
Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).



<!--HONumber=Feb17_HO2-->


