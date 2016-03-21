<properties
	pageTitle="Ajouter l’API Microsoft Translator à PowerApps Enterprise ou des applications logiques | Microsoft Azure"
	description="Vue d'ensemble de l'API Microsoft Translator avec les paramètres de l'API REST"
	services=""
    suite=""
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
   ms.date="03/02/2016"
   ms.author="mandia"/>

# Prendre en main l'API Microsoft Translator
Connectez-vous à Microsoft Translator pour traduire un texte, détecter une langue, et bien plus encore. L’API Microsoft Translator peut être utilisée à partir de :

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

Avec Microsoft Translator, vous pouvez :

- Créer votre flux d'activité en fonction des données que vous obtenez de Microsoft Translator. 
- Utiliser des actions pour traduire un texte, détecter une langue, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Par exemple, lorsqu'un fichier est créé dans Dropbox, vous pouvez traduire le texte qu’il contient dans une autre langue à l'aide de Microsoft Translator.
- Ajouter l’API Microsoft Translator à PowerApps Enterprise Ensuite, vos utilisateurs peuvent utiliser cette API dans leurs applications. 

Pour plus d’informations sur l’ajout d’une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
Microsoft Translator inclut les actions suivantes. Il n'y a aucun déclencheur.

Déclencheurs | Actions
--- | ---
Aucun | <ul><li>Détecter la langue</li><li>Synthèse vocale</li><li>Traduire le texte</li><li>Obtenir les langues</li><li>Obtenir les langues de synthèse vocale</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.


## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### Détecter la langue    
Détecte la langue source du texte. ```GET: /Detect```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|(aucun) |Texte dont la langue sera identifiée|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Synthèse vocale    
Effectue la synthèse vocale d’un texte sous forme d’un flux audio au format wave. ```GET: /Speak```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|(aucun) |Texte à convertir|
|language|string|yes|query|(aucun) |Code de la langue pour la synthèse vocale (exemple : « fr-FR »)|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Traduire le texte    
Traduit le texte dans une langue spécifique à l’aide de Microsoft Translator. ```GET: /Translate```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|(aucun) |Texte à traduire|
|languageTo|string|yes|query| (aucun)|Code de la langue cible (exemple : « fr »)|
|languageFrom|string|no|query|(aucun) |Langue source ; si omis, Microsoft Translator tente de la détecter automatiquement. (exemple : en)|
|category|string|no|query|général |Catégorie de traduction (par défaut : « général »)|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les langues    
Extrait toutes les langues prises en charge par Microsoft Translator. ```GET: /TranslatableLanguages```

Il n'existe aucun paramètre pour cet appel.

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les langues de synthèse vocale    
Extrait les langues disponibles pour la synthèse vocale. ```GET: /SpeakLanguages```

Il n'existe aucun paramètre pour cet appel.

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

## Définitions d'objet

#### Langue : modèle de langage pour les langues traduisibles par Microsoft Translator

|Nom de la propriété | Type de données | Requis|
|---|---|---|
|Code|string|no|
|Nom|string|no|


## Étapes suivantes

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenez à la [liste des API](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/create-api-microsofttranslator/register-your-application.png

<!------HONumber=AcomDC_0309_2016-->