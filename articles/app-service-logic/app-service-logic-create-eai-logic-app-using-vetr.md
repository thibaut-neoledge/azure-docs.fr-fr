<properties
   pageTitle="Créer une application logique IAE à l’aide de VETR dans les applications logiques d’Azure App Service | Microsoft Azure"
   description="Fonctionnalités de validation, encodage et transformation des services XML BizTalk"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# Créer une application logique IAE à l'aide de VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

La plupart des scénarios d'Intégration d'Applications d'Entreprise (IAE) servent d'intermédiaire pour les données entre une source et une destination. Ces scénarios ont souvent un ensemble commun d'exigences :

- Garantir que les données de différents systèmes sont dans un format correct.
- Effectuer une « recherche » sur les données entrantes pour prendre des décisions.
- Convertir des données d’un format vers un autre. Par exemple, convertir les données d’un système CRM vers le format de données d’un système ERP.
- Acheminer les données vers l’application ou le système souhaité(e).

Cet article illustre un modèle d'intégration courant : la « médiation de message unidirectionnelle » ou VETR (Validation, Enrichissement, Transformation, Routage). Le modèle VETR sert d'intermédiaire pour les données entre une entité source et une entité de destination. La source et la destination sont généralement des sources de données.

Prenons comme exemple un site web qui accepte des commandes. Les utilisateurs envoient des commandes au système à l'aide du protocole HTTP. En arrière-plan, le système valide les données entrantes, les normalise et les stocke dans une file d'attente Service Bus pour un traitement ultérieur. Le système extrait les commandes de la file d'attente et s'attend à ce qu'elles soient dans un format particulier. Ainsi, le flux de bout en bout est le suivant :

**HTTP** → **Valider** → **Transformer** → **Service Bus**

![Flux de base VETR][1]

Les applications API BizTalk suivantes aident à créer ce modèle :

* **Déclencheur HTTP** : source pour déclencher un événement de message
* **Validation** : valide les données entrantes
* **Transformation** : transforme les données du format d'entrée au format requis par le système en aval
* **Connecteur Service Bus** : entité de destination où les données sont envoyées


## Construction du modèle VETR de base
### Concepts de base

Dans le portail Azure, sélectionnez **+Nouveau**, **Web + Mobile**, puis sélectionnez **Application logique**. Choisissez un nom, un emplacement, un abonnement et un groupe de ressources qui vous conviennent. Les groupes de ressources jouent le rôle de conteneurs pour vos applications ; toutes les ressources de votre application vont dans le même groupe de ressources.

Ensuite, nous allons ajouter des déclencheurs et des actions.


## Ajouter un déclencheur HTTP
1. Dans **Modèles d’application logique**, sélectionnez **Créer intégralement**.
1. Sélectionnez **Écouteur HTTP** dans la galerie pour créer un écouteur. Nommez-le **HTTP1**.
2. Définissez la valeur False pour le paramètre **Envoyer la réponse automatiquement**. Configurez l’action du déclencheur en affectant la valeur _POST_ à _Méthode HTTP_ et la valeur _/OneWayPipeline_ à _URL relative_ : 
	![Déclencheur HTTP][2]
3. Cliquez sur la coche verte pour exécuter le déclencheur.

## Ajouter une action de validation

Nous allons maintenant entrer des actions qui s’exécutent chaque fois que le déclencheur est activé, autrement dit chaque fois qu’un appel est reçu sur le point de terminaison HTTP.

1. Ajoutez le **Validateur XML BizTalk** à partir de la galerie et nommez-le _(Validate1)_ pour créer une instance.
2. Configurez un schéma XSD pour valider les messages XML entrants. Sélectionnez l’action _Validation_ et sélectionnez _triggers(’httplistener’).outputs.Content_ comme valeur pour le paramètre _inputXml_.

À présent, l'action de validation est la première action après l'écouteur HTTP :

![Validateur XML BizTalk][3]

En procédant de même, nous allons ajouter le reste des actions.

## Ajouter une action de transformation
Nous allons maintenant configurer des transformations pour normaliser les données entrantes.

1. Sélectionnez **Service de transformation BizTalk** dans la galerie.
2. Pour configurer une transformation de manière à transformer les messages XML entrants, sélectionnez l’action **Transformer** comme action à exécuter lors de l’appel de cette API. Sélectionnez ```triggers(‘httplistener’).outputs.Content``` comme valeur pour _inputXml_. *Map* est un paramètre facultatif, car les données entrantes sont mises en correspondance avec toutes les transformations configurées et seules celles qui correspondent au schéma sont appliquées.
3. Pour finir, la transformation s'exécute uniquement si la validation réussit. Pour configurer cette condition, cliquez sur l’icône d’engrenage dans le coin supérieur droit et sélectionnez _Ajouter une condition à remplir_. Définissez la condition sur ```equals(actions('xmlvalidator').status,'Succeeded')``` :  

![Transformations BizTalk][4]


## Ajouter le connecteur Service Bus
Ensuite, nous allons ajouter la destination (une file d’attente Service Bus) où écrire les données.

1. Ajoutez un **Connecteur Service Bus** à partir de la galerie. Affectez la valeur _Servicebus1_ à **Name**, affectez la chaîne de connexion à votre instance Service Bus comme valeur **Connection String**, affectez la valeur **Queue** à _Entity Name_ et ignorez **Subscription name**.
2. Sélectionnez l’action **Envoyer un message** et affectez la valeur _actions(’transformservice’).outputs.OutputXml_ au champ **Contenu**.
3. Définissez **Type de contenu** sur *application/xml* :  

![Service Bus][5]


## Envoyer une réponse HTTP
Une fois le traitement du pipeline terminé, renvoyez une réponse HTTP pour la réussite et l'échec en procédant comme suit :

1. Ajoutez un **Écouteur HTTP** à partir de la galerie et sélectionnez l’action **Envoyer une réponse HTTP**.
2. Définissez **ID de réponse** sur *Envoyer un message*.
2. Définissez **Contenu de la réponse** sur *Traitement du pipeline terminé*.
3. **Code d’état de la réponse** sur *200* pour indiquer HTTP 200 OK.
4. Cliquez sur la liste déroulante en haut à droite et sélectionnez **Ajouter une condition à remplir**. Définissez la condition sur l’expression suivante : ```@equals(actions('azureservicebusconnector').status,'Succeeded')``` <br/>
5. Répétez ces étapes pour envoyer aussi une réponse HTTP en cas d'échec. Modifiez la **condition** sur l’expression suivante : ```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Sélectionnez **OK** puis **Créer**.



## Achèvement
Chaque fois que quelqu'un envoie un message au point de terminaison HTTP, l'application est déclenchée et les actions que vous venez de créer sont exécutées. Pour gérer n’importe quelle application logique que vous créez, cliquez sur **Parcourir** dans le portail Azure et sélectionnez **Logic Apps**. Sélectionnez votre application pour afficher plus d'informations.

Rubriques utiles :

[Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md) <br/> [Analyser vos applications logiques](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

<!---HONumber=AcomDC_0420_2016-->
