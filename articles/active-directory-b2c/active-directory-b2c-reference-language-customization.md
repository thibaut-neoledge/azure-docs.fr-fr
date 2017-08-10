---
title: "Azure Active Directory B2C : utilisation de la personnalisation de la langue | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: sama
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.contentlocale: fr-fr
ms.lasthandoff: 08/08/2017

---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C : utilisation de la personnalisation de la langue

>[!NOTE] 
>Cette fonctionnalité est en version préliminaire publique.  Il est recommandé d’utiliser un client de test lors de l’utilisation de cette fonctionnalité.  Nous ne prévoyons pas d’apporter d’autres modifications entre la version préliminaire et la version mise à la disposition générale, mais nous nous réservons le droit d’en apporter pour améliorer la fonctionnalité.  Une fois que vous aurez eu l’occasion d’essayer cette fonctionnalité, donnez-nous votre avis sur cette expérience et comment nous pouvons l’améliorer.  Vous pouvez nous faire part de vos commentaires via le portail Azure avec l’outil symbolisé par une émoticône en haut à droite.   Si votre entreprise a des exigences particulières pour que vous utilisiez cette fonctionnalité en version préliminaire, communiquez-nous vos scénarios pour que nous vous proposions l’assistance et les conseils adéquats.  Vous pouvez nous contacter à l’adresse [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com).

La personnalisation de la langue vous permet de changer la langue de votre parcours utilisateur pour répondre aux besoins de votre client.  Nous fournissons des traductions pour 36 langues (voir [Informations supplémentaires](#additional-information)).  Même si votre expérience est disponible dans une seule langue, vous pouvez personnaliser n’importe quel texte sur les pages en fonction de vos besoins.  

## <a name="how-does-language-customization-work"></a>Fonctionnement de la personnalisation de la langue
La personnalisation de la langue vous permet de sélectionner les langues dans lesquelles votre parcours utilisateur est disponible.  Une fois que la fonctionnalité est activée, vous pouvez fournir le paramètre de chaîne de requête, ui_locales, de votre application.  Lorsque vous appelez dans Azure AD B2C, nous traduisons votre page en fonction du paramètre régional que vous avez indiqué.  L’utilisation du type de configuration vous permet de contrôler intégralement les langues de votre parcours utilisateur et ignore les paramètres de langue du navigateur du client. Vous pouvez également ne pas avoir besoin de ce niveau de contrôle sur les langues que votre client voit.  Si vous n’indiquez pas de paramètre ui_locales, l’expérience du client est régie par les paramètres de son navigateur.  Vous pouvez toujours contrôler les langues dans lesquelles votre parcours utilisateur est traduit en les ajoutant en tant que langues prises en charge.  Si le navigateur d’un client est défini pour afficher une langue que vous ne souhaitez pas prendre en charge, c’est la langue sélectionnée par défaut dans les cultures prises en charge qui est affichée à la place.

1. **Langue spécifiée par le paramètre ui_locales** : après avoir activé la personnalisation de la langue, votre parcours utilisateur est traduit dans la langue indiquée ici.
2. **Langue demandée par le navigateur** : si aucun paramètre ui_locales n’a été spécifié, la page est traduite dans la langue demandée par le navigateur, **si elle a été ajoutée en tant que langue prise en charge**.
3. **Langue par défaut de la stratégie** : si le navigateur ne spécifie aucune langue ou s’il en spécifie une qui n’est pas prise en charge, la page est traduite dans la langue par défaut de la stratégie.

>[!NOTE]
>Si vous utilisez des attributs utilisateur personnalisés, vous devez fournir vos propres traductions. Consultez « [Personnaliser vos chaînes](#customize-your-strings) » pour plus d’informations.
>

## <a name="support-uilocales-requested-languages"></a>Prendre en charge les langues demandées par le paramètre ui_locales 
En activant la personnalisation de la langue sur une stratégie, vous pouvez désormais contrôler la langue du parcours utilisateur en ajoutant le paramètre ui_locales.
1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Accédez à une stratégie pour laquelle vous souhaitez activer la traduction.
3. Cliquez sur **Personnalisation de la langue**.
4. Lisez attentivement l’avertissement.  Une fois activée, la personnalisation de la langue ne peut pas être désactivée.
5. Activez la fonctionnalité, puis cliquez sur **OK**.
6. Enregistrez votre stratégie dans l’angle supérieur gauche de votre panneau **Modifier une stratégie**.

## <a name="select-which-languages-your-user-journey-supports"></a>Sélectionner les langues prises en charge par votre parcours utilisateur 
Créez une liste des langues dans lesquelles votre parcours utilisateur peut être traduit lorsque le paramètre ui_locales n’est pas fourni.
1. Vérifiez que la personnalisation de la langue est activée dans votre stratégie, comme vu précédemment.
2. Dans votre panneau **Modifier une stratégie**, sélectionnez **Personnalisation de la langue**.
3. Vous êtes redirigé vers votre panneau **Langues prises en charge**.  D’ici, vous pouvez sélectionner **Ajouter une langue**.
4. Sélectionnez toutes les langues que vous souhaitez prendre en charge.  

>[!NOTE]
>Si aucun paramètre ui_locales n’est fourni, la page est traduite dans la langue du navigateur de l’utilisateur uniquement si elle figure dans la liste.
>

5. Cliquez sur **OK** en bas.
6. Fermez le panneau **Personnalisation de la langue** et **enregistrez** votre stratégie.

## <a name="customize-your-strings"></a>Personnaliser vos chaînes
La personnalisation de la langue vous permet de personnaliser les chaînes de votre parcours utilisateur.
1. Vérifiez que la personnalisation de la langue est activée dans votre stratégie, comme vu précédemment.
2. Dans votre panneau **Modifier une stratégie**, sélectionnez **Personnalisation de la langue**.
3. Dans le menu de navigation de gauche, sélectionnez **Télécharger le contenu**.
4. Sélectionnez la page que vous souhaitez modifier.
5. Dans la liste déroulante, sélectionnez la langue concernée par la modification.
6. Cliquez sur **Télécharger**. 

À la suite de ces opérations, vous obtenez un fichier JSON que vous pouvez utiliser pour commencer à modifier vos chaînes.

### <a name="changing-any-string-on-the-page"></a>Modification de chaînes sur la page
1. Ouvrez le fichier JSON téléchargé précédemment dans un éditeur JSON.
2. Recherchez l’élément que vous souhaitez modifier.  Vous pouvez trouver le `StringId` de la chaîne que vous recherchez, ou recherchez la `Value` que vous souhaitez modifier.
3. Mettez à jour l’attribut `Value` avec ce que vous souhaitez afficher.
4. Enregistrez le fichier et chargez vos modifications.

### <a name="changing-extension-attributes"></a>Modification des attributs d’extension
Si vous souhaitez modifier la chaîne d’un attribut utilisateur personnalisé, ou en ajouter un au fichier JSON, le format suivant est utilisé :
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>Si vous devez remplacer une chaîne, veillez à définir la valeur `Override` sur `true`.  Si la valeur n’est pas modifiée, l’entrée est ignorée. 
>

Remplacez `<ExtensionAttribute>` par le nom de votre attribut utilisateur personnalisé.  
Remplacez `<ExtensionAttributeValue>` par la nouvelle chaîne à afficher.

### <a name="using-localizedcollections"></a>Utilisation de LocalizedCollections
Si vous souhaitez fournir une liste définie de valeurs pour les réponses, vous devez créer un élément `LocalizedCollections`.  Un élément `LocalizedCollections` est un tableau de paires `Name` et `Value`.  Le `Name` est ce qui est affiché et la `Value` est ce qui est renvoyé dans la revendication.  Pour ajouter un élément `LocalizedCollections`, le format utilisé est le suivant :

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": false,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```
>[!IMPORTANT]
>Si vous devez remplacer une chaîne, veillez à définir la valeur `Override` sur `true`.  Si la valeur n’est pas modifiée, l’entrée est ignorée. 
>

* `ElementId` est l’attribut utilisateur auquel cet élément `LocalizedCollections` apporte une réponse.
* `Name` est la valeur affichée à l’utilisateur.
* `Value` est ce qui est renvoyé dans la revendication lorsque cette option est sélectionnée.

### <a name="upload-your-changes"></a>Charger vos modifications
1. Une fois que vous avez terminé de modifier votre fichier JSON, accédez à votre client B2C.
2. Dans votre panneau **Modifier une stratégie**, sélectionnez **Personnalisation de la langue**.
3. Dans le menu de navigation de gauche, sélectionnez **Charger le contenu**.
4. Sélectionnez la page vers laquelle vous souhaitez charger vos modifications.
5. Si vous souhaitez charger une langue pour laquelle vous avez précédemment fourni un fichier JSON, vous devez supprimer l’ancienne entrée.  Vous pouvez la supprimer en cliquant sur `...` à droite de cette langue, puis en sélectionnant **Supprimer**.
6. Cliquez sur **Ajouter** dans l’angle supérieur gauche.
7. Sélectionnez la langue de votre fichier JSON.
8. Cliquez sur le bouton de dossier à droite et accédez à votre fichier JSON.
9. Cliquez sur le bouton **Charger** en bas du panneau.
10. Revenez au panneau **Modifier une stratégie** et cliquez sur **Enregistrer**.

## <a name="additional-information"></a>Informations supplémentaires
### <a name="recommendations-for-language-customization"></a>Recommandations pour la personnalisation de la langue
Nous vous recommandons d’ajouter uniquement des entrées à vos ressources de langue pour les chaînes que vous voulez remplacer de façon explicite.  Nous appliquons une limite de taille au fichier qui est compilé en dehors de toutes vos traductions JSON.  Si vos fichiers deviennent trop volumineux, cela aura une répercussion sur les performances de votre parcours utilisateur.
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>Les étiquettes de personnalisation de l’interface utilisateur de la page sont supprimées une fois que la personnalisation de la langue est activée.
Lorsque vous activez la personnalisation de la langue, les modifications précédemment appliquées aux étiquettes via la personnalisation de l’interface utilisateur de la page sont supprimées, à l’exception des attributs utilisateur personnalisés.  Cette modification est effectuée pour éviter les conflits aux endroits où vous pouvez modifier vos chaînes.  Vous pouvez continuer à modifier vos étiquettes et les autres chaînes en chargeant des ressources de langues dans « Personnalisation de la langue ».
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft s’engage à fournir des traductions à jour pour que vous puissiez les utiliser.
Nous ne cesserons d’améliorer nos traductions pour qu’elles soient conformes à vos attentes.  Nous identifierons les bogues et les modifications dans la terminologie globale et créerons les mises à jour qui s’adapteront en toute transparence à votre parcours utilisateur.
### <a name="support-for-right-to-left-languages"></a>Prise en charge des langues s’écrivant de droite à gauche
Il n’y a aucune prise en charge des langues de droite à gauche. Si vous avez besoin de cette fonctionnalité, votez pour elle dans la page de [Commentaires Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traductions des fournisseurs d’identité de réseaux sociaux
Nous proposons le paramètre OIDC ui_locales pour les connexions aux réseaux sociaux, mais il n’est pas honoré par certains fournisseurs d’identité de réseaux sociaux, notamment Facebook et Google. 
### <a name="browser-behavior"></a>Comportement du navigateur
Chrome et Firefox exigent tous deux leur langue définie et s’il s’agit d’une langue prise en charge, elle sera affichée avant la valeur par défaut.  Actuellement, Edge n’exige pas de langue particulière et affiche directement la langue par défaut.
### <a name="known-issues"></a>Problèmes connus
* Le chargement de ressources de langue pour la page MFA dans une stratégie de modification de profil est actuellement indisponible.
* L’élément `LocalizedCollections` n’est pas généré pour les valeurs lorsqu’il est requis par le type de réponse.
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>Que se passe-t-il si je veux une langue qui n’est pas prise en charge ?
Nous prévoyons de fournir une extension de cette fonctionnalité pour vous permettre de charger une ressource JSON pour des langues personnalisées.  Cette fonctionnalité vous permet de spécifier le nom et le code de langue pour n’importe quelle langue et de fournir *toutes* les traductions pour cette langue.  Si vous avez besoin de cette fonctionnalité, envoyez-nous votre scénario à l’adresse [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com).  

### <a name="what-languages-are-supported"></a>Quelles sont les langues prises en charge ?

| language              | Code de langue |
|-----------------------|---------------|
| Bangla                | bn            |
| Tchèque                 | cs            |
| Danois                | da            |
| Allemand                | de            |
| Grec                 | el            |
| Français               | en            |
| Espagnol               | es            |
| Finnois               | fi            |
| Français                | fr            |
| Goudjrati              | gu            |
| Hindi                 | hi            |
| Croate              | hr            |
| Hongrois             | hu            |
| Italien               | it            |
| Japonais              | ja            |
| Kannada               | kn            |
| Coréen                | ko            |
| Malayalam             | ml            |
| Marathi               | mr            |
| Malais                 | ms            |
| Norvégien Bokmål      | nb            |
| Néerlandais                 | nl            |
| Pendjabi               | pa            |
| Polonais                | pl            |
| Portugais - Brésil   | pt-br         |
| Portugais - Portugal | pt-pt         |
| Roumain              | ro            |
| Russe               | ru            |
| Slovaque                | sk            |
| Suédois               | sv            |
| Tamoul                 | ta            |
| Télougou                | te            |
| Thaï                  | th            |
| Turc               | tr            |
| Chinois - simplifié  | zh-hans       |
| Chinois - traditionnel | zh-hant       |

