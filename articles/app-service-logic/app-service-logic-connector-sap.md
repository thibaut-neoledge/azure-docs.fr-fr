<properties
   pageTitle="Connecteur SAP"
   description="Utilisation du connecteur SAP"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/02/2015"
   ms.author="sameerch"/>


# Connecteur SAP #

Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d’un flux. Dans certains scénarios, vous pouvez être amené à utiliser SAP, installé localement et derrière le pare-feu. En exploitant le connecteur SAP dans votre flux, vous pouvez couvrir un large éventail de scénarios. Quelques exemples :

1.	Exposer une section des données résidant dans votre SAP via un frontal web ou d'utilisateur mobile.
2.	Publier des données sur votre SAP après leur traitement approprié.
3.	Extraire des données de SAP pour les utiliser dans un processus d'entreprise.

Pour ces scénarios, vous devez effectuer ce qui suit :

1. Créez une instance de l'application API du connecteur SAP.
2. Établissez une connectivité hybride pour que l’application API puisse communiquer localement avec SAP.
3. Utilisez l’application API créée dans une application logique pour accomplir le processus d’entreprise souhaité.

## Créez une instance de l'application API du connecteur SAP. ##

Pour utiliser le connecteur SAP, vous devez créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l'aide de l'option « + NOUVEAU » en bas à gauche du portail Azure.
2. Accédez à « Web et mobile > API Apps » et recherchez « SAP Connector ».
3. Configurez-le comme suit :
	1. Fournissez les détails génériques comme le nom, le plan de service de l’application, etc. dans le premier panneau.

	2. Dans le cadre des paramètres du package, fournissez les informations d'identification SAP. Fournissez également une chaîne de connexion Azure Service Bus. Celle-ci sert à établir une connectivité hybride avec votre SAP local.

	3. Les RFC, TRFC, BAPI et IDOC doivent être configurés en fonction des besoins du scénario. Si plusieurs valeurs doivent être fournies, alors elles peuvent être séparées par des virgules.

![][1]

## Configurez l'application API du connecteur SAP que vous venez de créer. ##

Accédez à l’application API tout juste créée via Parcourir -> API Apps -> <Name of the API App just created>. Le comportement suivant est visible. L’installation n’est pas terminée puisque la connexion hybride n’est pas encore établie.

![][2]

Pour l’établir, procédez comme suit :

1. Copiez la chaîne de connexion principale.
2. Cliquez sur le lien Télécharger et configurer.
3. Suivez le processus d’installation qui se lance et fournissez la chaîne de connexion principale quand vous y êtes invité.
4. Une fois le processus d’installation terminé, une boîte de dialogue similaire à celle-ci apparaît.

![][3]

Maintenant, quand vous accédez de nouveau à l’application API créée, vous pouvez observer que l’état de la connexion hybride est Connecté.

![][4]

Remarque : si vous voulez basculer vers la chaîne de connexion secondaire, il vous suffit de recommencer la configuration et de fournir la chaîne de connexion secondaire à la place de la chaîne de connexion principale.

## Utilisation dans une application logique ##

Le connecteur SAP peut être utilisé en tant qu'action/étape uniquement dans une application logique.

Quand vous créez/modifiez une application logique, choisissez l'application API du connecteur SAP que vous avez créée ci-dessus. Toutes les actions permises que vous pouvez choisir sont alors répertoriées.

![][5]

Dès que vous sélectionnez une action, la liste des paramètres d'entrée de cette action apparaît. Fournissez les valeurs appropriées, puis cliquez sur l'icône représentant une coche.

![][6]

L'étape/action apparaît à présent comme étant configurée dans l'application logique. Les sorties de l'opération s'affichent et peuvent être utilisées comme entrées dans une étape ultérieure.

![][7]

Complétez l'application logique pour définir le processus d'entreprise, puis exécutez-la pour atteindre l'objectif voulu.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=July15_HO5-->