---
title: "Modèle de boucle séquentielle dans les modèles Azure | Microsoft Docs"
description: "Explique comment étendre les fonctionnalités des modèles Azure Resource Manager pour implémenter un modèle de boucle séquentielle lors du déploiement de plusieurs instances d’un type de ressource."
services: guidance, azure-resource-manager
documentationcenter: na
author: petertaylor9999
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 04/18/2017
ms.author: petertay
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 44d59488600e05d6ae80a169b5d682e8fa94ccc5
ms.lasthandoff: 04/18/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---sequential-looping"></a>Modèles pour étendre les fonctionnalités des modèles Azure Resource Manager - boucle séquentielle

Les modèles Azure Resource Manager prennent en charge le déploiement d’un groupe de ressources similaires via une boucle de copie. Une boucle de copie dans un objet de ressource peut être utilisée pour itérer un tableau de chaînes utilisées ensuite afin de générer des noms uniques pour les ressources. Une boucle de copie dans l’objet de ressource peut également servir à effectuer une itération sur un tableau de variables qui décrivent la ressource.

Tous ces modèles fonctionnent bien mais uniquement lorsqu’il n’existe aucune dépendance entre chaque membre du groupe. Au cours d’une boucle d’itération, Resource Manager tente de déployer des ressources en parallèle. Si une première ressource dépend d’une seconde, le déploiement peut échouer si Resource Manager déploie la seconde ressource avant la première.

Le problème vient du fait que Resource Manager ne prend actuellement pas en charge `dependsOn` dans une boucle d’itération. Toutefois, cette fonctionnalité peut être implémentée en utilisant la fonctionnalité Resource Manager existante et en modifiant le nom de certaines ressources. 

## <a name="sequential-looping-pattern"></a>Modèle de boucle séquentielle

Le modèle est le suivant : une première ressource est nommée en utilisant une concaténation du préfixe d’un nom et `0`, ou le premier index de la boucle. Une seconde ressource inclut une boucle de copie et, dans la boucle copie, le nom de la ressource suivante est une concaténation du préfixe de nom et le résultat de la fonction `copyIndex(1)`, qui ajoute 1 à la valeur `copyIndex()` actuelle. La seconde ressource inclut également un élément `dependsOn` qui fait référence à la concaténation du préfixe de nom et le résultat de la fonction `copyIndex()`. Cette approche crée une relation `dependsOn` entre la ressource suivante à la ressource précédente. Avant de déployer la ressource suivante, Resource Manager attend que la ressource précédente soit déployée.

Le modèle suivant illustre ce modèle. Le type de ressource Microsoft.Resources/deployments n’est qu’un modèle imbriqué qui ne déploie réellement aucune ressource.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "variables": {
    "count": "[sub(parameters('numberToDeploy'), 1)]"
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "loop-0 "
            }
          }
        }
      }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex(1))]",
      "dependsOn": [
        "[concat('loop-', copyIndex())]"
      ],
      "copy": {
        "name": "iterator",
        "count": "[variables('count')]"
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "[concat(reference(concat('loop-',copyIndex())).outputs.collection.value,'loop-',copyIndex(1), ' ')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference(concat('loop-',variables('count'))).outputs.collection.value]"
    }
  }
}

```
Dans ce modèle, le premier objet de ressource est nommé `loop-0`. Puis, dans le second objet de ressource, le nom de la ressource suivante est une concaténation du mot `loop-` et du résultat de la fonction `copyIndex(1)` : `loop-1`. L’élément `dependsOn` fait référence à la ressource précédente car il s’agit d’une concaténation du mot `loop-` et du résultat de la `copyIndex()` fonction : `loop-0`. Le modèle dans le deuxième objet de ressource est répété jusqu'à atteindre `count`, en terminant par une ressource nommée `loop-4` qui `dependsOn` `loop-3`. Notez que `count` est une variable qui soustrait `1` du `numberToDeploy` paramètre pour conserver le nombre de base zéro correct.

## <a name="try-the-template"></a>Essayer le modèle

Si vous souhaitez effectuer des tests avec ce modèle, procédez comme suit :

1.    Accédez au portail Azure, sélectionnez l’icône « + » puis recherchez le type de ressource « déploiement de modèle ». Lorsque cette ressource apparaît dans les résultats de la recherche, sélectionnez-la.
2.    Lorsque vous accédez à la page « déploiement de modèle », sélectionnez le bouton « Créer ». Ouvrez le panneau « déploiement personnalisé » pour constater que le modèle n’a aucune ressource.
3.    Sélectionnez l’icône « Éditer ».
4.    Supprimez le modèle vide dans le volet de droite.
5.    Copiez et collez l’exemple de modèle précédent dans le volet de droite.
6.    Sélectionnez le bouton « Enregistrer ».
7.    Vous revenez au volet « déploiement personnalisé », mais cette fois-ci, quelques menus déroulants apparaissent. Sélectionnez votre abonnement, créez un nouveau groupe de ressources ou utilisez un groupe de ressources existant, puis sélectionnez un emplacement. Passez en revue les Conditions générales, puis cliquez sur le bouton « J’accepte ».
8.    Sélectionnez le bouton « Acheter ».

Pour vérifier que les ressources sont déployées séquentiellement, sélectionnez « groupes de ressources », puis choisissez le groupe de ressources que vous avez sélectionné précédemment. Cliquez sur le bouton « déploiements » dans le volet du groupe de ressources : les ressources sont déployées séquentiellement avec l’horodatage correspondant.

![Déploiement d’un modèle de boucle séquentielle dans Azure Resource Manager](./media/resource-manager-sequential-loop/deployments.png)

## <a name="next-steps"></a>Étapes suivantes

Utilisez ce modèle dans vos modèles en ajoutant vos ressources au modèle imbriqué. Vous pouvez soit les créer directement dans l’élément de modèle de la ressource `Microsoft.Resources/deployments` ou établir un lien vers eux à l’aide de l’élément `templateLink`. Le type de ressource dans l’exemple est un modèle imbriqué, mais n’importe quel type de ressource peut être déployé. Seule exception : les ressources enfants ne peuvent pas être référencées dans une boucle d’itération.

* Pour une introduction à la création de plusieurs instances d’une ressource, consultez [Déployer plusieurs instances de ressources dans des modèles Azure Resource Manager](resource-group-create-multiple.md).
* Ce modèle est également implémenté dans le [modèle de projet de blocs de construction](https://github.com/mspnp/template-building-blocks) et les [architectures de référence Azure](/azure/architecture/reference-architectures/).
