<properties
	pageTitle="Exporter un modèle Azure Resource Manager | Microsoft Azure"
	description="Utilisez Azure Resource Manage pour exporter un modèle à partir d’un groupe de ressources existant."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/10/2016"
	ms.author="tomfitz"/>

# Exporter un modèle Azure Resource Manager à partir de ressources existantes

Comprendre comment construire des modèles Azure Resource Manager peut être décourageant. Heureusement, Resource Manager vous aide à cette tâche, car vous pouvez exporter un modèle à partir de ressources existantes dans votre abonnement. Vous pouvez utiliser le modèle généré pour découvrir la syntaxe du modèle, ou pour automatiser le redéploiement de votre solution en fonction des besoins.

Dans ce didacticiel, vous allez vous connecter au portail Azure, créer un compte de stockage et exporter le modèle pour ce dernier. Vous allez ajouter un réseau virtuel pour modifier le groupe de ressources. Enfin, vous allez exporter un nouveau modèle qui représente son état actuel. Bien que cet article présente le cas d’une infrastructure simplifiée, vous pouvez suivre les mêmes étapes pour exporter un modèle dédié à une solution plus complexe.

## Créez un compte de stockage.

1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez **Nouveau** > **Données + Stockage** > **Compte de stockage**.

      ![créer le stockage](./media/resource-manager-export-template/create-storage.png)

2. Créez un compte de stockage portant le nom **stockage**, vos initiales et la date. Le nom du compte de stockage doit être unique dans Azure. Si vous avez essayé un nom déjà utilisé, essayez une variante. Pour le groupe de ressources, utilisez **ExportGroup**. Vous pouvez utiliser les valeurs par défaut des autres propriétés. Sélectionnez **Créer**.

      ![fournir des valeurs pour le stockage](./media/resource-manager-export-template/provide-storage-values.png)

Une fois le déploiement terminé, votre abonnement contient le compte de stockage.

## Exporter le modèle pour le déploiement

1. Accédez au panneau de groupe de ressources de votre nouveau groupe de ressources. Vous pouvez voir que le résultat du dernier déploiement est répertorié. Sélectionnez ce lien.

      ![panneau du groupe de ressources](./media/resource-manager-export-template/resource-group-blade.png)

2. Un historique des déploiements du groupe s’affiche. Dans votre cas, il n’y a probablement qu’un seul déploiement répertorié. Sélectionnez ce déploiement.

     ![dernier déploiement](./media/resource-manager-export-template/last-deployment.png)

3. Un résumé du déploiement s’affiche. Le résumé inclut l’état du déploiement et ses opérations, ainsi que les valeurs de paramètres que vous avez fournies. Pour voir le modèle qui a été utilisé pour le déploiement, sélectionnez **Afficher le modèle**.

     ![afficher le résumé du déploiement](./media/resource-manager-export-template/deployment-summary.png)

4. Resource Manager récupère pour vous les cinq fichiers ci-dessous :

   - Le modèle définissant l’infrastructure pour votre solution. Lorsque vous avez créé le compte de stockage via le portail, Resource Manager a utilisé un modèle pour le déployer et a enregistré ce modèle pour référence ultérieure.

   - Un fichier de paramètres que vous pouvez utiliser pour transmettre des valeurs au cours du déploiement. Il contient les valeurs que vous avez fournies lors du premier déploiement, mais vous pouvez modifier ces valeurs lors du redéploiement du modèle.

   - Un fichier de script Azure PowerShell que vous pouvez utiliser pour déployer le modèle.

   - Un fichier de script d’interface de ligne de commande Azure que vous pouvez utiliser pour déployer le modèle.

   - Une classe .NET que vous pouvez utiliser pour déployer le modèle.

     Les fichiers sont disponibles via des liens dans le panneau. Par défaut, le modèle est sélectionné.

       ![afficher le modèle](./media/resource-manager-export-template/view-template.png)

     Regardons de plus près le modèle. Ce modèle doit ressembler à ce qui suit :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "name": {
              "type": "String"
            },
            "accountType": {
              "type": "String"
            },
            "location": {
              "type": "String"
            },
            "encryptionEnabled": {
              "defaultValue": false,
              "type": "Bool"
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "sku": {
                "name": "[parameters('accountType')]"
              },
              "kind": "Storage",
              "name": "[parameters('name')]",
              "apiVersion": "2016-01-01",
              "location": "[parameters('location')]",
              "properties": {
                "encryption": {
                  "services": {
                    "blob": {
                      "enabled": "[parameters('encryptionEnabled')]"
                    }
                  },
                  "keySource": "Microsoft.Storage"
                }
              }
            }
          ]
        }

     Notez que le modèle définit les paramètres du nom, du type et de l’emplacement du compte de stockage. Un paramètre indique également si le chiffrement est activé et si la valeur par défaut est **false**. Dans la section **resources**, vous verrez la définition du compte de stockage à déployer.

L’expression contenue entre crochets est évaluée pendant le déploiement. Les expressions entre crochets illustrées dans le modèle sont utilisées pour obtenir les valeurs des paramètres pendant le déploiement. Vous pouvez utiliser bien d’autres expressions que vous pouvez utiliser. Vous en verrez d’autres exemples plus loin dans cet article. Pour obtenir la liste complète, consultez [Fonctions du modèle Azure Resource Manager](resource-group-template-functions.md).

Pour plus d’informations sur la structure d’un modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

## Ajouter un réseau virtuel

Le modèle que vous avez téléchargé dans la section précédente représentait l’infrastructure de ce déploiement d’origine, mais il ne sera pas pris en compte dans les modifications que vous apporterez après le déploiement. Pour illustrer ce problème, modifions le groupe de ressources en ajoutant un réseau virtuel via le portail.

1. Dans le panneau du groupe de ressources, sélectionnez **Ajouter** et choisissez **réseau virtuel** à partir des ressources disponibles.

2. Nommez votre réseau virtuel **VNET** et utilisez les valeurs par défaut pour les autres propriétés. Sélectionnez **Créer**.

      ![définir une alerte](./media/resource-manager-export-template/create-vnet.png)

3. Une fois le réseau virtuel correctement déployé vers votre groupe de ressources, examinez à nouveau l’historique de déploiement. Vous voyez maintenant deux déploiements. Sélectionnez un déploiement plus récent.

      ![historique des déploiements](./media/resource-manager-export-template/deployment-history.png)

4. Examinez le modèle pour ce déploiement. Notez qu’il définit uniquement les modifications apportées pour ajouter le réseau virtuel.

Il est généralement recommandé de travailler avec un modèle qui déploie l’ensemble de l’infrastructure de votre solution en une seule opération, plutôt qu’en mémorisant de nombreux modèles différents à déployer.


## Exporter le modèle pour le groupe de ressources

Bien que chaque déploiement affiche seulement les modifications apportées à votre groupe de ressources, vous pouvez à tout moment exporter un modèle pour afficher les attributs de votre groupe de ressources complet.

1. Pour afficher le modèle pour un groupe de ressources, sélectionnez **Exporter le modèle**.

      ![exporter un groupe de ressources](./media/resource-manager-export-template/export-resource-group.png)

2. Vous verrez à nouveau les 5 fichiers que vous pouvez utiliser pour redéployer la solution, mais cette fois le modèle est un peu différent. Ce modèle comporte seulement 2 paramètres : un pour le nom du compte de stockage et un pour le nom du réseau virtuel.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     Resource Manager n’a pas récupéré les modèles utilisés pendant le déploiement. Au lieu de cela, il a généré un nouveau modèle basé sur la configuration actuelle des ressources. Resource Manager ne connaît pas les valeurs que vous voulez transmettre en tant que paramètres, donc il code en dur la plupart des valeurs en fonction de la valeur dans le groupe de ressources. Par exemple, l’emplacement et la valeur de réplication du compte stockage sont définis sur :

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Téléchargez le modèle afin de pouvoir travailler dessus en local.

      ![télécharger un modèle](./media/resource-manager-export-template/download-template.png)

4. Recherchez le fichier .zip que vous avez téléchargé et extrayez le contenu. Vous pouvez utiliser ce modèle téléchargé pour redéployer votre infrastructure.

## Étapes suivantes

Félicitations ! Vous avez appris comment exporter un modèle à partir des ressources que vous avez créées dans le portail.

- Dans la deuxième partie de ce didacticiel, vous allez personnaliser le modèle que vous venez de télécharger en ajoutant des paramètres et en le redéployant dans un script. Consultez [Personnaliser et redéployer le modèle exporté](resource-manager-customize-template.md).
- Pour savoir comment exporter un modèle via PowerShell, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager.md).
- Pour savoir comment exporter un modèle via l’interface de ligne de commande Azure, consultez [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0518_2016-->