<properties
   pageTitle="Utilisation de l’Extracteur XPath BizTalk dans des applications logiques dans Azure App Service | Microsoft Azure"
   description="Extracteur XPath BizTalk"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="dwrede"
   editor=""/>


<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="12/07/2015"
   ms.author="rajram"/>


#Extracteur XPath BizTalk

Le connecteur Extracteur XPath BizTalk permet de rechercher votre application et d’extraire des données d’un contenu XML sur la base d’une expression XPath.

##Utilisation de l’Extracteur XPath BizTalk
1. Pour utiliser l'Extracteur Xpath BizTalk, vous devez d'abord créer une instance de l'application API d’extracteur Xpath BizTalk. Cela est possible inline, lors de la création d'une application logique, ou en sélectionnant l'application API Extracteur Xpath BizTalk dans Azure Marketplace.

	>[AZURE.NOTE]Aucun paramètre de configuration n'est associé à BizTalk Xpath Extractor.
2. [Créer une application logique]. Ouvrez « Déclencheurs et actions » dans votre application logique pour ouvrir le concepteur d’applications logiques afin de configurer votre flux.
3. Dans le concepteur, le volet de droite répertorie les applications API disponibles pour créer votre flux. Trouvez l'« Extracteur XPath BizTalk ». La sélection de cette option ajoute l’Extracteur XPath à votre flux et approvisionne une instance de celui-ci.
4. Une fois approvisionné, le concepteur affiche l’action associée à l’application API Extracteur XPath BizTalk : ![Choisir une action Extracteur XPath BizTalk][1]

5. Choisissez « Extraire avec XPath ». « Extraire avec XPath » évalue l’expression Xpath d’entrée sur un contenu XML d’entrée donné : ![Entrée Extracteur XPath BizTalk][2]


Paramètre|Type|Description du paramètre
---|---|---
XPath|string|Chemin d'accès de requête dans un code XML.
XML d'entrée|string|Contenu du XML d’entrée

L'action retourne le résultat sous forme de chaîne - Result. Le résultat contient la valeur du chemin d’accès de la requête dans le contenu XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[Créer une application logique]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_1210_2015-->