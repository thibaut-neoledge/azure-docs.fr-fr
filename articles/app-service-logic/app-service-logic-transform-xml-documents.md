<properties 
	pageTitle="Transformation BizTalk" 
	description="Découvrez comment transformer des documents XML d'un schéma vers un autre." 
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="anuragdalmia"/>

#Transformation BizTalk


## Vue d'ensemble
L'application API Transformation BizTalk convertit des données d'un format vers un autre. Par exemple, vous pourriez prendre les adresses d'expédition et de facturation d'un bon de commande et les insérer dans un document de facturation. Ou vous pourriez avoir un message entrant qui contient la date du jour au format *AnnéeMoisJour*. Vous souhaitez remettre en forme la date au format *AnnéeMoisJour* .

Vous pouvez pour cela utiliser l'application API Transformation BizTalk dans Microsoft Azure App Service. Une transformation ou un mappage est composé d'un schéma XML source (l'entrée) et d'un schéma XML cible (la sortie). Vous pouvez utiliser différentes fonctions intégrées pour aider à manipuler ou à contrôler les données, y compris les manipulations de chaînes, les affectations conditionnelles, les expressions arithmétiques, les formateurs d'heure et de date et même les constructions de bouclage.

Vous pouvez créer des mappages dans Visual Studio à l’aide du [Kit de développement logiciel (SDK) Microsoft Azure BizTalk Services](http://www.microsoft.com/download/details.aspx?id=39087). Une fois que vous avez fini de créer et de tester le mappage, vous téléchargez le mappage (.trfm) dans l'application API Transformation BizTalk.

Les fonctionnalités supplémentaires sont les suivantes :

- La transformation créée dans un mappage peut être simple, par exemple la copie d'un nom et de l'adresse d'un document vers un autre. Vous pouvez aussi créer des transformations plus complexes à l'aide des opérations de mappage prêtes à l'emploi.
- Plusieurs fonctions ou opérations de mappage sont disponibles, y compris des chaînes, des fonctions de date et d'heure, et ainsi de suite.
- Possibilité d'effectuer une copie de données directe entre les schémas. Dans le Mappeur BizTalk, il suffit de dessiner une ligne qui relie les éléments dans le schéma source à leurs équivalents dans le schéma de destination.
- Lors de la création d'un mappage, sa représentation graphique est affichée, y compris toutes les relations et les liens que vous créez.
- Utilisez la fonctionnalité **Tester le mappage** pour ajouter un exemple de message XML. Avec un simple clic, vous pouvez tester le mappage que vous avez créé et afficher la sortie générée.
- Téléchargez des mappages Azure BizTalk Services existants (.trfm) et exploitez tous les avantages offerts par l'application API Transformation.
- Lorsque vous créez le mappage, vous n’avez pas besoin d’ajouter un schéma. Lorsque votre mappage est prêt, ajoutez-le à l’application API Transformation. 
- Comprend la prise en charge du format XML.


## Télécharger des schémas à partir d'applications API de connecteurs
Vous pouvez télécharger les schémas XML pour des connecteurs tels que SQL, SAP et SharePoint à partir de la page de résumé de l'application API. Par exemple, si vous souhaitez télécharger des schémas XML pour une application API de connecteur SAP spécifique, accédez à l'application API et ouvrez la page de résumé. Sélectionnez **Télécharger les schémas** pour télécharger sur votre ordinateur un fichier zip contenant tous les schémas correspondant aux actions SAP. Vous pouvez utiliser les schémas pour créer un mappage (.trfm) dans Visual Studio.


## Créer et ajouter le mappage
Vous pouvez créer des transformations ou des mappages dans Visual Studio à l’aide du [Kit de développement logiciel (SDK) Microsoft Azure BizTalk Services](http://www.microsoft.com/download/details.aspx?id=39087), qui est téléchargeable gratuitement.

Pour obtenir de l’aide pour la création d’un mappage, consultez la page [Créer un mappage dans Visual Studio](http://aka.ms/createamapinvs). Une fois le mappage créé et prêt pour la production, vous pouvez l'ajouter (fichier .trfm) à l'application API Transformation BizTalk que vous avez créée dans le portail de gestion Azure.

Si le mappage change ou est modifié après son téléchargement, vous pouvez télécharger le mappage mis à jour. Il remplacera le mappage existant dans l'application API Transformation.

1.	Sélectionnez **Parcourir** dans le portail de gestion Azure (sur la gauche de l’écran) et sélectionnez **API Apps**. Si **API Apps** n’est pas visible, sélectionnez **Tout**, puis **API Apps** dans la liste disponible :

	![][7]

2.	La liste de toutes les **API Apps** créées dans votre abonnement Azure est affichée :

	![][8]

3.	Sélectionnez l'application API Transformation BizTalk que vous avez créée dans la section précédente.

4.	Le panneau de configuration de l'application API s'ouvre. La mention **Mappages** est visible dans la section Composants :

	![][9]

5.	Sélectionnez **Mappages** pour ouvrir le nouveau panneau avec la liste des mappages.

6.	Sélectionnez l’icône **Ajouter un mappage** dans la partie supérieure pour ouvrir le panneau **Ajouter un mappage** :

	![][10]

7.	Sélectionnez l'icône Fichier et accédez à un fichier de mappage (.trfm) sur votre ordinateur local.

8.  Sélectionnez **OK** pour créer un mappage. Celui-ci est affiché dans la liste des mappages.


## Utiliser une application API Transformation BizTalk dans une application logique
Une fois le mappage créé et testé, il est prêt pour la consommation.

1. Dans l'application logique, Transformation BizTalk est disponible dans la galerie à droite. Sélectionnez **Service de transformation BizTalk** dans la galerie. La transformation est ajoutée au flux :

	![][11]

2. Sélectionnez l’action **Transformation**. Les paramètres d'entrée sont affichés :

	![][12]

3. Entrez les paramètres suivants pour effectuer la configuration de l’action **Transformation** :
		 
	- XML d'entrée
		- Entrez le contenu XML valide conforme au schéma source d'un mappage dans l'application API Transformation. Il peut s'agir de la sortie d'une action précédente dans l'application logique, comme « Appeler RFC – SAP » ou « Insérer dans le tableau – SQL ».
		
	- Nom de mappage (facultatif)
		- Entrez un nom de mappage valide qui est déjà téléchargé dans votre application API Transformation. Si vous n'entrez aucun mappage, il est sélectionné automatiquement en fonction du schéma source auquel le XML d'entrée est conforme.

	![][13]

4. La sortie de l'action « XML de sortie » peut être utilisée dans les actions ultérieures dans vos applications logiques.

<!--Image references-->
[1]: ./media/app-service-logic-transform-xml-documents/Create_Everything.png
[2]: ./media/app-service-logic-transform-xml-documents/Create_Marketplace.png
[4]: ./media/app-service-logic-transform-xml-documents/Search_TransformAPIApp.png
[5]: ./media/app-service-logic-transform-xml-documents/Transform_APIApp_Landing_Page.png
[6]: ./media/app-service-logic-transform-xml-documents/New_TransformAPIApp_Blade.png
[7]: ./media/app-service-logic-transform-xml-documents/Browse_APIApps.png
[8]: ./media/app-service-logic-transform-xml-documents/Select_APIApp_List.png
[9]: ./media/app-service-logic-transform-xml-documents/Configure_Transform_APIApp.png
[10]: ./media/app-service-logic-transform-xml-documents/Add_Map.png
[11]: ./media/app-service-logic-transform-xml-documents/Transform_action_flow.png
[12]: ./media/app-service-logic-transform-xml-documents/Transform_Inputs.png
[13]: ./media/app-service-logic-transform-xml-documents/Transform_configured.png
[14]: ./media/app-service-logic-transform-xml-documents/Download_Schemas.png



 

<!---HONumber=July15_HO5-->