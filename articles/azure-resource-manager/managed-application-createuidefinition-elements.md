---
title: "Fonctions CreateUiDefinition des applications gérées Azure | Microsoft Docs"
description: "Décrit les fonctions à utiliser lors de l’élaboration de définitions d’interface utilisateur pour les applications gérées Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.openlocfilehash: 69c06c9607e13c2bcffa946d6bbff9bad310ccf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="createuidefinition-elements"></a>Éléments de CreateUiDefinition
Cet article décrit le schéma et les propriétés de tous les éléments pris en charge d’une fonction CreateUiDefinition. Vous utilisez ces éléments lors de la [création d’une application gérée Azure](managed-application-publishing.md). Le schéma pour la plupart des éléments est le suivant :

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Keep calm and visit the [Azure Portal](portal.azure.com).",
  "constraints": {},
  "options": {},
  "visible": true
}
```
| Propriété | Requis | Description |
| -------- | -------- | ----------- |
| name | Oui | Identificateur interne pour faire référence à l’instance spécifique d’un élément. L’utilisation la plus courante du nom d’élément est dans `outputs`, où les valeurs de sortie des éléments spécifiés sont mises en correspondance avec les paramètres du modèle. Vous pouvez également l’utiliser pour lier la valeur de sortie d’un élément à la `defaultValue` d’un autre élément. |
| type | Oui | Contrôle de l’interface utilisateur pour effectuer le rendu de l’élément. Pour obtenir la liste des types pris en charge, consultez [Éléments](#elements). |
| label | Oui | Texte d’affichage de l’élément. Certains types d’éléments contiennent plusieurs étiquettes. Par conséquent, la valeur pourrait être un objet contenant plusieurs chaînes. |
| defaultValue | Non | Valeur par défaut de l’élément. Certains types d’élément prennent en charge des valeurs par défaut complexes. Par conséquent, la valeur pourrait être un objet. |
| toolTip | Non | Texte à afficher dans l’info-bulle de l’élément. Comme pour `label`, certains éléments prennent en charge plusieurs chaînes d’info-bulle. Les liens inline peuvent être intégrés à l’aide de la syntaxe Markdown.
| constraints | Non | Une ou plusieurs propriétés permettant de personnaliser le comportement de validation de l’élément. Les propriétés prises en charge pour constraints varient selon le type d’élément. Certains types d’éléments ne prennent pas en charge la personnalisation du comportement de validation et n’ont donc aucune propriété constraints. |
| options | Non | Propriétés supplémentaires qui personnalisent le comportement de l’élément. À l’instar de `constraints`, les propriétés prises en charge varient selon le type d’élément. |
| visible | Non | Indique si l’élément est affiché. Si la valeur est `true`, l’élément et les éléments enfants applicables sont affichés. La valeur par défaut est `true`. Utilisez les [fonctions logiques](managed-application-createuidefinition-functions.md#logical-functions) pour contrôler de manière dynamique cette valeur de propriété.

## <a name="elements"></a>Éléments

La documentation de chaque élément contient un exemple d’interface utilisateur, un schéma, des remarques sur le comportement de l’élément (en général concernant la validation et la personnalisation prise en charge) et un exemple de sortie.

- [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
- [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
- [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](managed-application-overview.md).
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
