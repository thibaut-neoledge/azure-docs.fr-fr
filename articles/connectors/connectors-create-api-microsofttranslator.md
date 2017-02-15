---
title: "Ajouter Microsoft Translator à des applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur Microsoft Translator avec les paramètres de l’API REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: da782baf-8bf8-4973-8238-e469865f5328
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: add307420b9e16a60b0e57b0ba08188b2509978e


---
# <a name="get-started-with-the-microsoft-translator-connector"></a>Prise en main du connecteur Microsoft Translator
Connectez-vous à Microsoft Translator pour traduire un texte, détecter une langue, et bien plus encore. Avec Microsoft Translator, vous pouvez : 

* Créer votre flux d'activité en fonction des données que vous obtenez de Microsoft Translator. 
* Utiliser des actions pour traduire un texte, détecter une langue, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Par exemple, lorsqu'un fichier est créé dans Dropbox, vous pouvez traduire le texte qu’il contient dans une autre langue à l'aide de Microsoft Translator.

Pour ajouter une opération à des applications logiques, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Microsoft Translator inclut les actions suivantes. Il n'y a aucun déclencheur.

| Déclencheurs | Actions |
| --- | --- |
| Aucun |<ul><li>Détecter la langue</li><li>Synthèse vocale</li><li>Traduire le texte</li><li>Obtenir les langues</li><li>Obtenir les langues de synthèse vocale</li></ul> |

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## <a name="create-a-connection-to-microsoft-translator"></a>Créer une connexion à Microsoft Translator
> [!INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## <a name="swagger-rest-api-reference"></a>Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### <a name="detect-language"></a>Détecter la langue
Détecte la langue source du texte.  
```GET: /Detect```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte dont la langue sera identifiée |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="text-to-speech"></a>Synthèse vocale
Effectue la synthèse vocale d’un texte sous forme d’un flux audio au format wave.  
```GET: /Speak```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte à convertir |
| language |string |yes |query |(aucun) |Code de la langue pour la synthèse vocale (exemple : « en-us ») |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="translate-text"></a>Traduire le texte
Traduit le texte dans une langue spécifique à l’aide de Microsoft Translator.  
```GET: /Translate```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte à traduire |
| languageTo |string |yes |query |Aucun |Code de la langue cible (exemple : « fr ») |
| languageFrom |string |no |query |Aucun |Langue source ; si omis, Microsoft Translator tente de la détecter automatiquement. (exemple : en) |
| category |string |no |query |général |Catégorie de traduction (par défaut : « général ») |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-languages"></a>Obtenir les langues
Extrait toutes les langues prises en charge par Microsoft Translator.  
```GET: /TranslatableLanguages```

Il n'existe aucun paramètre pour cet appel. 

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-speech-languages"></a>Obtenir les langues de synthèse vocale
Extrait les langues disponibles pour la synthèse vocale.  
```GET: /SpeakLanguages``` 

Il n'existe aucun paramètre pour cet appel.

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d'objet
#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Langue : modèle de langage pour les langues traduisibles par Microsoft Translator
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Code |string |no |
| Nom |string |no |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png



<!--HONumber=Feb17_HO2-->


