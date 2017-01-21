---
title: "Utiliser VS Code avec les modèles Resource Manager | Microsoft Docs"
description: "Cet article explique comment configurer Visual Studio Code pour créer des modèles Azure Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: cmatskas
manager: timlt
editor: tysonn
ms.assetid: 78f2aa22-df1d-41bd-92ec-dabd1175db88
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: chmatsk;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 10c7051c9b1218081d95cb10403006bfd95126ba
ms.openlocfilehash: 2ac1c2cce7a9e045990894b0bbaa045df3d48954


---
# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Utiliser des modèles Azure Resource Manager dans Visual Studio Code
Les modèles Azure Resource Manager sont des fichiers JSON décrivant une ressource et ses dépendances associées. Ces fichiers peuvent parfois être volumineux et complexes, c’est la raison pour laquelle la prise en charge des outils est importante. Visual Studio Code est un nouvel éditeur de code multiplateforme, open source et léger. Il prend en charge la création et la modification des modèles Resource Manager via une [nouvelle extension](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). VS Code s’exécute n’importe où et ne nécessite un accès Internet que pour déployer vos modèles Resource Manager sur un abonnement Azure.

Si vous ne disposez pas de VS Code, vous pouvez l’installer à partir du site suivant : [https://code.visualstudio.com/](https://code.visualstudio.com/).

## <a name="install-the-resource-manager-extension"></a>Installer l’extension Resource Manager
Pour utiliser les modèles JSON dans VS Code, vous devez installer une extension. Les étapes suivantes permettent de télécharger et d’installer la prise en charge du langage pour les modèles JSON Resource Manager :

1. Lancer VS Code 
2. Ouvrir Quick Open (Ctrl+P) 
3. Exécutez la commande suivante : 
   
        ext install azurerm-vscode-tools
4. Redémarrez VS Code lorsque vous êtes invité à activer l’extension. 
   
   Le tour est joué !

## <a name="set-up-resource-manager-snippets"></a>Configurer les extraits de code Resource Manager
Les étapes précédentes ont permis d’installer la prise en charge des outils. À présent, nous avons besoin de configurer VS Code de manière à utiliser les extraits de code des modèles JSON.

1. Copiez le contenu du fichier à partir du référentiel [azure-xplat-arm-tooling](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) dans votre Presse-papiers.
2. Lancer VS Code 
3. Dans VS Code, vous pouvez ouvrir le fichier d’extraits de code JSON en sélectionnant **Fichier** -> **Préférences** -> **User Snippets (Extraits de code utilisateur)** -> **JSON**. L’autre possibilité consiste à sélectionner **F1**et à taper **préférences** jusqu’à pouvoir sélectionner **Préférences : extraits**.
   
    ![extraits de code de préférences](./media/resource-manager-vs-code/preferences-snippets.png)
   
    Sélectionnez **JSON**à partir des options.
   
    ![Sélectionner json](./media/resource-manager-vs-code/select-json.png)
4. Collez le contenu du fichier à l’étape 1 dans votre fichier d’extraits de code utilisateur avant la dernière « } » 
5. Assurez-vous que le JSON est correct et qu’il ne contient aucune tilde. 
6. Enregistrez et fermez le fichier d’extraits de code utilisateur.

C’est tout ce que vous devez faire pour commencer à utiliser les extraits de code Resource Manager. Ensuite, nous allons tester cette configuration.

## <a name="work-with-template-in-vs-code"></a>Utiliser des modèles dans VS Code
Le moyen le plus simple pour commencer à travailler avec un modèle consiste à utiliser un des modèles de démarrage rapide disponibles sur [Github](https://github.com/Azure/azure-quickstart-templates) ou à utiliser un de vos propres modèles. Vous pouvez facilement [exporter un modèle](resource-manager-export-template.md) pour un de vos groupes de ressources via le portail. 

1. Si vous avez exporté un modèle à partir d’un groupe de ressources, ouvrez les fichiers extraits dans VS Code.
   
    ![afficher les fichiers](./media/resource-manager-vs-code/show-files.png)
2. Ouvrez le fichier template.json afin de le modifier et ajoutez des ressources supplémentaires. Après `"resources": [`, appuyez sur Entrée pour aller à la ligne. Une liste d’options s’affiche si vous tapez **arm**. Ces options sont les extraits de code du modèle que vous avez installé. 
   
    ![afficher les extraits de code](./media/resource-manager-vs-code/type-snippets.png)
3. Choisissez l’extrait de code voulu. Pour cet article, j’ai choisi **arm-ip** pour créer une nouvelle adresse IP publique. Placez une virgule après l’accolade fermante « `}` » de la ressource créée pour vérifier que la syntaxe de votre modèle est correcte.
   
     ![ajouter une virgule](./media/resource-manager-vs-code/add-comma.png)
4. VS Code est doté d’IntelliSense. Lorsque vous modifiez vos modèles, VS Code suggère les valeurs disponibles. Par exemple, pour ajouter une section de variables à votre modèle, ajoutez `""` (deux guillemets doubles) et sélectionnez **Ctrl + Espace** entre ces guillemets. Des options comprenant des **variables** s’affichent.
   
    ![ajouter des variables](./media/resource-manager-vs-code/add-variables.png)
5. IntelliSense peut également suggérer des valeurs ou des fonctions disponibles. Pour attribuer une valeur de paramètre à une propriété, créez une expression avec `"[]"` et **Ctrl + Espace**. Vous pouvez commencer à taper le nom d’une fonction. Appuyez sur **Tabulation** lorsque vous avez trouvé la fonction souhaitée.
   
    ![ajouter un paramètre](./media/resource-manager-vs-code/select-parameters.png)
6. Pour afficher la liste des paramètres disponibles dans votre modèle, appuyez à nouveau sur **Ctrl + Espace** dans la fonction.
   
    ![ajouter un paramètre](./media/resource-manager-vs-code/select-avail-parameters.png)
7. Si vous rencontrez des problèmes de validation de schéma dans votre modèle, les tildes que vous connaissez s’affichent dans l’éditeur. Vous pouvez afficher la liste des erreurs et avertissements en tapant **Ctrl + Maj + M** ou en sélectionnant les glyphes dans la barre d’état inférieure gauche.
   
    ![errors](./media/resource-manager-vs-code/errors.png)
   
    La validation de votre modèle peut vous permettre de détecter les problèmes de syntaxe. Vous pourrez aussi voir des erreurs que vous pouvez ignorer. Dans certains cas, l’éditeur compare votre modèle à un schéma qui n’est pas à jour et, par conséquent, signale une erreur même si vous savez qu’il est correct. Par exemple, supposons qu’une fonction a été récemment ajoutée à Resource Manager, et que le schéma n’a pas été mis à jour. L’éditeur signale une erreur en dépit du fait que la fonction fonctionne correctement lors du déploiement.
   
    ![message d’erreur](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Déployer vos nouvelles ressources
Lorsque votre modèle est prêt, vous pouvez déployer les nouvelles ressources en suivant les instructions ci-dessous : 

### <a name="windows"></a>Windows
1. Ouvrez une invite de commandes PowerShell. 
2. Pour vous connecter, tapez : 
   
  ```powershell
  Login-AzureRmAccount
  ```

3. Si vous avez plusieurs abonnements, obtenez-en la liste à l’aide de la commande suivante :

  ```powershell 
  Get-AzureRmSubscription
  ```
   
    Sélectionnez ensuite l’abonnement à utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId <Subscription Id>
  ```

4. Mettez à jour les paramètres du fichier parameters.json.
5. Exécutez Deploy.ps1 pour déployer votre modèle sur Azure.

### <a name="osxlinux"></a>OSX/Linux
1. Ouvrez une fenêtre de terminal. 
2. Pour vous connecter, tapez :

  ```azurecli
  azure login
  ```

3. Sélectionnez l’abonnement approprié s’il en existe plusieurs :

  ```azurecli
  azure account set <subscriptionNameOrId> 
  ```

4. Mettez à jour les paramètres du fichier parameters.json.
5. Pour déployer le modèle, exécutez :

  ```azurecli 
  azure group deployment create -f <PathToTemplate>
  ``` 

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les modèles, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour en savoir plus sur les fonctions de modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour obtenir d’autres exemples de l’utilisation de Visual Studio Code, consultez la page [Build cloud apps with Visual Studio Code (Créer des applications cloud avec Visual Studio Code)](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) de la [démonstration](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) Connect 2015 [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz). Pour d’autres démarrages rapides à partir de la démonstration pour HealthClinic.biz, consultez [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)(Démarrages rapides avec les outils de développement Azure).




<!--HONumber=Jan17_HO1-->


