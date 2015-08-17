<properties 
   pageTitle="Créer une application logique IAE à l'aide de VETR" 
   description="Cette rubrique traite des fonctionnalités de validation, encodage et transformation de BizTalk services." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/24/2015"
   ms.author="rajram"/>


# Créer une application logique IAE à l'aide de VETR

La plupart des scénarios d'Intégration d'Applications d'Entreprise (IAE) servent d'intermédiaire pour les données entre une source et une destination. Ces scénarios ont souvent un ensemble commun d'exigences :

- Garantir que les données de différents systèmes sont dans un format correct.
- Effectuer une « recherche » sur les données entrantes pour prendre des décisions.
- Convertir les données d'un format vers un autre (par exemple, du format de données d'un système CRM vers le format de données d'un système ERP).
- Acheminer les données vers l'application ou le système souhaité.

Dans cet article, nous allons examiner un modèle d’intégration courant : la « médiation de message unidirectionnelle » ou VETR (Validation, Enrichissement, Transformation, Routage.) Le modèle VETR sert d'intermédiaire pour les données entre une entité source et une entité de destination. La source et la destination sont généralement des sources de données.

Prenons comme exemple un site web qui accepte des commandes. Les utilisateurs envoient des commandes au système à l'aide du protocole HTTP. En arrière-plan, le système valide les données entrantes, les normalise et les stocke dans une file d'attente Service Bus pour un traitement ultérieur. Le système extrait les commandes de la file d'attente et s'attend à ce qu'elles soient dans un format particulier. Ainsi, le flux de bout en bout est le suivant :

HTTP -> Validation -> Transformation -> Service Bus

![Flux de base VETR][1]

Les applications API BizTalk suivantes aident à créer ce modèle :

*Déclencheur HTTP* : source du déclenchement de l’événement de message *Validation* : valide l’exactitude des données entrantes *Transformation* : transforme les données d’entrée au format requis par le système en aval *Connecteur Service Bus* : entité de destination où les données sont envoyées


## Construction du modèle VETR de base
### Concepts de base

Dans le portail de gestion Azure, cliquez sur le bouton **+ Nouveau** en bas à gauche de l’écran, puis cliquez sur Application logique. Choisissez un nom, un emplacement, un abonnement et un groupe de ressources qui vous conviennent. Les groupes de ressources jouent le rôle de conteneurs pour vos applications et toutes les ressources de votre application vont dans le même groupe de ressources.

Ensuite, nous allons ajouter des déclencheurs et des actions.


## Ajouter un déclencheur HTTP

1. Sélectionnez **Écouteur HTTP** dans la galerie pour créer un écouteur. Nommez-le **HTTP1**.
2. Conservez la valeur False pour le paramètre **Envoyer la réponse automatiquement**. Configurez l’action du déclencheur en affectant la valeur _POST_ à _HTTP Method_ et la valeur _/OneWayPipeline_ à _Relative URL_.

![Déclencheur HTTP][2]


## Ajouter une action de validation

Maintenant, nous allons entrer des actions qui s'exécutent chaque fois que le déclencheur est activé, autrement dit chaque fois qu'un appel est reçu sur le point de terminaison HTTP.

1. Ajoutez le **Validateur XML BizTalk** à partir de la galerie et nommez-le _(Validate1)_ pour créer une instance.
2. Configurez un schéma XSD pour valider les messages XML entrants. Sélectionnez l’action _Validation_ et sélectionnez _triggers(’httplistener’).outputs.Content_ comme valeur pour le paramètre _inputXml_.

À présent, l'action de validation est la première action après l'écouteur HTTP. En procédant de même, nous allons ajouter le reste des actions.

![Validateur XML BizTalk][3]


## Ajouter une action de transformation
Nous allons maintenant configurer des transformations pour normaliser les données entrantes.

1. Ajoutez **Transformation** à partir de la galerie. 
2. Pour configurer une transformation pour transformer les messages XML entrants, sélectionnez l’action **Transformer** comme action à exécuter quand cette API est appelée et sélectionnez ```triggers(‘httplistener’).outputs.Content``` comme valeur pour _inputXml_. Map est un paramètre facultatif car les données entrantes sont mises en correspondance avec toutes les transformations configurées et seules celles qui correspondent au schéma sont appliquées.
3. Pour finir, la transformation s'exécute uniquement si la validation réussit. Pour configurer cette condition, sélectionnez l’icône d’engrenage dans le coin supérieur droit pour _Ajouter une condition à remplir_. Définissez la valeur suivante pour la condition : ```equals(actions('xmlvalidator').status,'Succeeded')```


![Transformations BizTalk][4]


## Ajouter le connecteur Service Bus
Ensuite, nous allons ajouter la destination (une file d'attente Service Bus) où écrire les données.

1. Ajoutez un **Connecteur Service Bus** à partir de la galerie. Affectez la valeur _Servicebus1_ à _Name_, affectez la chaîne de connexion à votre instance Service Bus comme valeur _Connection String_, affectez la valeur _Queue_ à _Entity Name_ et ignorez _Subscription name_. 
2. Sélectionnez l’action **Envoyer un message** et affectez la valeur _actions(’transformservice’).outputs.OutputXml_ au champ _Message_.

![Service Bus][5]


## Envoyer une réponse HTTP
Une fois le traitement du pipeline terminé, nous renvoyons une réponse HTTP pour la réussite et l'échec en procédant comme suit :

1. Ajoutez un **Écouteur HTTP** à partir de la galerie et sélectionnez l’action **Envoyer une réponse HTTP**.
2. Affectez la valeur « Traitement de pipeline terminé » à _Response Content_, la valeur « 200 » à _Response Status Code_ pour indiquer HTTP 200 OK, et la condition de l’expression ```@equals(actions('servicebusconnector').status,'Succeeded')```
	
Répétez les étapes ci-dessus pour envoyer aussi une réponse HTTP en cas d'échec. Modifiez la valeur de la condition en ```@not(equals(actions('servicebusconnector').status,'Succeeded')).```


## Achèvement
Chaque fois que quelqu'un envoie un message au point de terminaison HTTP, l'application est déclenchée et les actions que nous venons de créer sont exécutées. Pour gérer le genre d’application logique que vous créez, cliquez sur **Parcourir** dans le portail de gestion Azure et cliquez sur **Logic Apps**. Cliquez sur votre application pour afficher plus d'informations.


<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

 

<!---HONumber=August15_HO6-->