<properties 
   pageTitle="Extracteur XPath BizTalk" 
   description="Extracteur XPath BizTalk" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="rajram"/>

#Extracteur XPath BizTalk

Le connecteur Extracteur XPath BizTalk permet de rechercher votre application et d’extraire des données d’un contenu XML sur la base d’une expression XPath.

##Utilisation de l’Extracteur XPath BizTalk
1. Pour utiliser l'Extracteur Xpath BizTalk, vous devez d'abord créer une instance de l'application API d’extracteur Xpath BizTalk. Cela est possible inline, lors de la création d'une application logique, ou en sélectionnant l'application API Extracteur Xpath BizTalk dans Azure Marketplace.

		NOTE: There are no configuration settings associated with BizTalk Xpath Extractor.
2. Le concepteur affiche l'action associée à l’application API Extracteur XPath BizTalk.
	
![Choisir une action Extracteur XPath BizTalk][1]

3. Choisissez « Extraire avec XPath »

« Extraire avec XPath » évalue l’expression Xpath d'entrée sur un fichier XML d'entrée donné.

![Entrée Extracteur XPath BizTalk][2]

<table>
	<tr>
		<th>Paramètre</th>
		<th>Type</th>
		<th>Description du paramètre</th>
	</tr>
	<tr>
		<td>XPath</td>
		<td>string</td>
		<td>Chemin d'accès de requête dans un code XML.</td>
	</tr>
	<tr>
		<td>XML d'entrée</td>
		<td>string</td>
		<td>Contenu du XML d’entrée</td>
	</tr>
</table>

L'action retourne le résultat sous forme de chaîne - Result. Result contient la valeur du chemin d'accès de requête dans un code XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!---HONumber=58-->