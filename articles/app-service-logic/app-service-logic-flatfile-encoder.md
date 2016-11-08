---
title: Utilisation de l’encodeur de fichier plat BizTalk dans une application logique| Microsoft Docs
description: Application API de l’encodeur de fichier plat BizTalk ou connecteur
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajram
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 04/20/2016
ms.author: rajram

---
# Encodeur de fichier plat BizTalk
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Utilisez le connecteur encodeur de fichier plat BizTalk pour faciliter les interactions entre des données de fichiers plats (par exemple, des fichiers Excel ou csv) et des données au format XML. Il peut convertir une instance de fichier plat donnée au format XML et vice versa.

## Utilisation de l’encodeur de fichier plat BizTalk
Pour utiliser l'encodeur de fichier plat BizTalk, vous devez d'abord créer une instance de l'application API d’encodeur de fichier plat BizTalk. Cela est possible inline, lors de la création d'une application logique, ou en sélectionnant l'application API d’encodeur de fichier plat BizTalk dans Azure Marketplace. Procédez comme suit pour créer une application API de l'encodeur de fichier plat BizTalk à partir d'Azure Marketplace :

1. Connectez-vous au portail Azure (http://portal.azure.com)
2. Sélectionnez New (Nouveau) > Marketplace > All (Tout)
3. Recherchez « BizTalk Flat File Encode » (Encodeur de fichier plat BizTalk) dans la zone de recherche
4. Sélectionnez l'encodeur de fichier plat BizTalk dans la liste de résultats
5. Sélectionnez « Create (Créer) », puis indiquez un nom et les autres détails obligatoires.
6. Sélectionnez « Create (Créer) ». Vous serez redirigé vers la page de démarrage où vous pouvez afficher la progression de la création. Cette opération peut prendre un certain temps. Vous recevrez une notification lorsqu'elle sera terminée.

### Configurer l’encodeur de fichier plat BizTalk
Une fois l'API App créée, vous pouvez la lancer directement à partir de la page de démarrage du portail Azure ou à partir de la surface du concepteur lors de la création d'une application logique.

Pour la lancer à partir de la page de démarrage Azure, vous pouvez la rechercher en tapant le nom que vous avez donné à l'encodeur de fichier plat BizTalk lors de sa création. Pour cela, procédez comme suit :

1. Saisissez le nom de votre encodeur de fichier plat BizTalk dans la zone de recherche du portail Azure et effectuez une recherche
2. Sélectionnez ensuite votre encodeur de fichier plat BizTalk dans la liste de résultats. Le volet API App dans lequel vous pouvez configurer votre application API de l'encodeur de fichier plat BizTalk s'ouvre. Pour démarrer la configuration, ajoutez un schéma :
3. en sélectionnant le composant « Schemas (Schémas) » ![Partie du schéma de l’encodeur de fichier plat BizTalk][2] ;
4. puis en sélectionnant « Add New (Ajouter un nouveau schéma) » dans le panneau de schémas qui s'ouvre ![Liste d’actions de l’encodeur de fichier plat BizTalk][7].
5. Sélectionnez l'une des trois options pour spécifier votre schéma. Les options sont UPLOAD NEW SCHEMA (CHARGER UN NOUVEAU SCHÉMA), GENERATE FROM JSON (GÉNÉRER DEPUIS JSON) et GENERATE FROM FLAT FILE (GÉNÉRER DEPUIS UN FICHIER PLAT)![Liste d’actions de l’encodeur de fichier plat BizTalk][8].
6. Suivez ces étapes pour spécifier votre schéma en fonction de votre sélection au cours de l'étape précédente. Vous verrez ensuite que le schéma a été chargé : ![Liste d’actions de l’encodeur de fichier plat BizTalk][9]

### Utilisation de l’encodeur de fichier plat BizTalk dans l’aire de conception
Maintenant que vous avez configuré l'encodeur de fichier plat Biztalk, vous pouvez l'utiliser dans une application logique. Pour commencer, créez une application logique, ou lancez une application que vous avez créée au préalable, puis procédez comme suit :

1. Dans la zone « Start logic (Démarrer la logique) », cliquez sur « Run this logic manually (Exécuter cette logique manuellement) ».
2. Sélectionnez l'application API de l'encodeur de fichier plat Biztalk que vous avez créée précédemment dans la galerie (vous trouverez l'encodeur de fichier plat Biztalk que vous avez créé dans la liste API Apps à droite de votre écran).
3. Sélectionnez la flèche noire vers la droite. Les deux actions disponibles (fichier plat à Xml et Xml à fichier plat) sont présentées : ![Liste d’actions de l’encodeur de fichier plat BizTalk][1] ![Liste d’actions de l’encodeur de fichier plat BizTalk][4]

Procédez comme suit en fonction de l'action sélectionnée.

#### Convertir un fichier plat en fichier XML
![Liste d’actions de l’encodeur de fichier plat BizTalk][5]

| Paramètre | Type | Description du paramètre |
| --- | --- | --- |
| Fichiers plats |string |Contenu du fichier plat d'entrée |
| Nom du schéma |string |Nom du schéma qui représente le fichier plat d'entrée |
| Nom de la racine |string |Nom du nœud racine du schéma de fichier plat |

L'action retourne le résultat sous forme de chaîne - XML de sortie. Le XML de sortie contient la représentation XML du contenu du fichier plat d'entrée.

#### Convertir un fichier XML dans un format de fichier plat
![Liste d’actions de l’encodeur de fichier plat BizTalk][6]

| Paramètre | Type | Description du paramètre |
| --- | --- | --- |
| XML d'entrée |string |Contenu du XML d’entrée |

L'action retourne le résultat sous forme de chaîne - fichier plat. La sortie contient la représentation du fichier plat du contenu du fichier XML d'entrée.

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
[7]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.addschema.PNG
[8]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.selectschemauploadoption.PNG
[9]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.shemauploaded.PNG



<!---HONumber=AcomDC_0803_2016-->