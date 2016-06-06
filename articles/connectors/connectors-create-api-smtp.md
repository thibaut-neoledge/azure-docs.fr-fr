<properties
    pageTitle="Ajouter le connecteur SMTP à vos applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur SMTP avec les paramètres d’API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/19/2016"
   ms.author="mandia"/>

# Prise en main du connecteur SMTP
Se connecter à un serveur SMTP pour envoyer un courrier électronique. Le connecteur SMTP peut être utilisé dans :

- Applications logiques (abordées dans cette rubrique)
- PowerApps [consultez [PowerApps connections list](https://powerapps.microsoft.com/tutorials/connections-list/) (Liste des connexions PowerApps) pour obtenir la liste complète]

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec SMTP, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d'activité incluant l'envoi de courriers électroniques à l'aide du protocole SMTP. 
- Utiliser une action pour envoyer un courrier électronique. Cette action obtient une réponse, puis met la sortie à la disposition d'autres actions. Par exemple, s’il existe un nouveau fichier sur votre serveur FTP, vous pouvez prendre ce fichier et l’envoyer par courrier électronique en tant que pièce jointe à l'aide du protocole SMTP. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
Pour le connecteur SMTP, l’action suivante est disponible. Il n'y a aucun déclencheur.

|Déclencheurs | Actions|
|--- | ---|
|Aucun | Envoi de courrier électronique|

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## Créer une connexion à SMTP

>[AZURE.INCLUDE [Procédure de création d’une connexion SMTP](../../includes/connectors-create-api-smtp.md)]

Après avoir créé la connexion, vous entrez les propriétés SMTP, notamment les valeurs To ou CC. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion SMTP dans d'autres applications logiques.

## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### Envoyer un message électronique
Envoie un message électronique à un ou plusieurs destinataires. ```POST: /SendEmail```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|emailMessage| many|yes|body|(aucun) |Message électronique|

## Définitions d'objet

#### Email: SMTP email

| Nom | Type de données | Requis|
|---|---|---|
|À|string|no|
|CC|string|no|
|Objet|string|no|
|Corps|string|no|
|À partir d'une base|string|no|
|IsHtml|booléenne|no|
|Cci|string|no|
|Importance|string|no|
|Pièces jointes|array|no|


#### Attachment: Email attachment

| Nom | Type de données |Requis|
|---|---|---|
|FileName|string|no|
|ContentId|string|no|
|ContentData|string|yes|
|ContentType|string|yes|
|ContentTransferEncoding|string|yes|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0525_2016-->