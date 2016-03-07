<properties
   pageTitle="Utilisation du validateur XML BizTalk dans des applications logiques dans Azure App Service | Microsoft Azure"
   description="Validez des schémas à l’aide du validateur XML BizTalk dans votre application logique"
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
   ms.date="02/18/2016"
   ms.author="rajram"/>

# Validateur XML BizTalk

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Utilisez le connecteur Validateur XML BizTalk dans votre application pour valider les données XML par rapport à des schémas XML prédéfinis. Les utilisateurs peuvent utiliser les schémas existants ou en générer en fonction d’une instance de fichier plat, d’une instance JSON ou de connecteurs existants.

## Utilisation du Validateur XML BizTalk
Pour utiliser le Validateur XML BizTalk, créez d’abord une instance de l’application API du Validateur XML BizTalk. Cela est possible inline, lors de la création d'une application logique ou en sélectionnant l'application API du Validateur XML BizTalk à partir d'Azure Marketplace.

### Configurer le Validateur XML BizTalk
Le Validateur XML BizTalk utilise des schémas dans le cadre de sa configuration. Les utilisateurs peuvent lancer le panneau de configuration de l’application API soit en lançant directement l’application API à partir du portail Azure, soit en double-cliquant sur l’application API dans l’aire du concepteur :

![Configuration du Validateur XML BizTalk][1]

Dans le panneau de l’application API, les utilisateurs peuvent configurer des schémas en sélectionnant *Schémas* :

![Partie du schéma du Validateur XML BizTalk][2]

Les utilisateurs peuvent charger des schémas à partir d’un disque ou en générer un à partir d’une instance de fichier plat ou d’une instance JSON :

![Schémas du Validateur XML BizTalk][3]


### Utilisation de l’encodeur de fichier plat BizTalk dans l’aire de conception
Une fois celui-ci configuré, les utilisateurs peuvent sélectionner *->* et choisir une action dans une liste :

![Liste d'actions du Validateur XML BizTalk][4]

#### Valider Xml

L’action Valider Xml valide une entrée XML donnée par rapport à des schémas préconfigurés :

![Action Valider Xml du Validateur XML BizTalk][5]

Paramètre|Type|Description du paramètre
---|---|---
XML d'entrée|string|Xml d'entrée à valider

L'action renvoie la sortie sous forme d'objet. La sortie contient le modèle qui représente la réponse du validateur XML. Elle se compose du résultat, du nom de schéma, du nœud racine et de la description de l’erreur.


<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG

<!---HONumber=AcomDC_0224_2016-->