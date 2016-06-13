<properties
	pageTitle="Galeries de runbooks et de modules pour Azure Automation | Microsoft Azure"
	description="Des runbooks et des modules de Microsoft et de la communauté sont disponibles pour l’installation et l’utilisation dans votre environnement Azure Automation. Cet article décrit comment accéder à ces ressources et comment partager vos runbooks dans la galerie."
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="stevenka"
	editor="tysonn" />
<tags
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="03/02/2016"
	ms.author="magoedte;bwren" />


# Galeries de runbooks et de modules pour Azure Automation

Au lieu de créer vos propres runbooks et modules dans Azure Automation, vous pouvez accéder à un large éventail de solutions déjà générées par Microsoft et la communauté. Vous pouvez utiliser ces solutions sans les modifier ou les utiliser comme point de départ, puis les modifier selon vos besoins spécifiques.

Vous pouvez obtenir des runbooks à partir de la [galerie de runbooks](#runbooks-in-runbook-gallery) et des modules à partir de la [PowerShell Gallery](#modules-in-powerShell-gallery). Vous pouvez également contribuer à la communauté en partageant les solutions que vous développez.

## Runbooks dans la galerie de runbooks

La [galerie de runbooks](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=RootCategory&f[0].Value=WindowsAzure&f[1].Type=SubCategory&f[1].Value=WindowsAzure_automation&f[1].Text=Automation) fournit un éventail de runbooks de Microsoft et de la communauté que vous pouvez importer dans Azure Automation. Vous pouvez télécharger un runbook à partir de la galerie qui est hébergée dans le [Centre de scripts TechNet](http://gallery.technet.microsoft.com/), ou vous pouvez importer directement les runbooks de la galerie à partir du portail Azure Classic ou du portail Azure.

Vous pouvez uniquement importer directement à partir de la galerie de runbooks à l’aide du portail Azure Classic ou du portail Azure. Vous ne pouvez pas exécuter cette fonction à l’aide de Windows PowerShell.

>[AZURE.NOTE] Vous devez valider le contenu de tous les runbooks que vous obtenez de la galerie de runbooks et vous montrer très vigilant lors de leur installation et de leur exécution dans un environnement de production. |

### Pour importer un runbook à partir de la galerie de runbooks avec le portail Azure Classic

1. Dans le portail de gestion Azure, cliquez sur **Nouveau**, **Services d’application**, **Automation**, **Runbook**, **À partir de la galerie**.
2. Sélectionnez une catégorie pour afficher les runbooks associés, puis sélectionnez un runbook pour afficher les informations le concernant. Une fois que vous avez sélectionné le runbook souhaité, cliquez sur la flèche droite.

    ![Galerie de runbooks](media/automation-runbook-gallery/runbook-gallery.png)

3. Passez en revue le contenu du runbook et prenez note des conditions requises dans la description. Cliquez sur la flèche droite lorsque vous avez terminé.
4. Entrez les détails du runbook, puis cliquez sur la coche. Le nom du runbook est déjà renseigné.
5. Le runbook apparaît sur l’onglet **Runbooks** pour le compte Automation.

### Pour importer un runbook à partir de la galerie de runbooks avec le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Cliquez sur le bouton **Parcourir la galerie**.

    ![Bouton Parcourir la galerie](media/automation-runbook-gallery/browse-gallery-button.png)

4. Recherchez l’élément de la galerie qui vous intéresse et sélectionnez-le pour afficher les informations le concernant.

    ![Parcourir la galerie](media/automation-runbook-gallery/browse-gallery.png)

4. Cliquez sur **Afficher le projet source** pour afficher l’élément dans le [Centre de scripts TechNet](http://gallery.technet.microsoft.com/).
5. Pour importer un élément, cliquez dessus pour afficher les informations le concernant, puis cliquez sur le bouton **Importer**.

    ![Bouton Importer](media/automation-runbook-gallery/gallery-item-detail.png)

6. Vous pouvez également modifier le nom du runbook, puis cliquer sur **OK** pour importer le runbook.
5. Le runbook apparaît sur l’onglet **Runbooks** pour le compte Automation.


### Ajout d’un runbook à la galerie de runbooks

Microsoft vous invite à ajouter à la galerie de runbooks des runbooks dont vous pensez qu’ils pourraient être utiles à d’autres utilisateurs. Vous pouvez ajouter un runbook en [le téléchargeant vers le Centre de scripts](http://gallery.technet.microsoft.com/site/upload) en tenant compte des informations suivantes.

- Vous devez spécifier *Microsoft Azure* comme **Catégorie** et *Automation* comme **Sous-catégorie** pour le runbook à afficher dans l’Assistant.  

- Le téléchargement doit être un seul fichier .ps1 ou .graphrunbook. Si le runbook nécessite des modules, des runbooks enfants ou des ressources, vous devez répertorier ceux figurant dans la description de l’envoi et dans la section commentaires du runbook. Si vous disposez d’une solution nécessitant plusieurs runbooks, téléchargez-les séparément et répertoriez les noms des runbooks associés dans chacune de leurs descriptions. Assurez-vous que vous utilisez les mêmes balises afin qu’elles s’affichent dans la même catégorie. Un utilisateur doit lire la description pour savoir que les autres runbooks sont requis pour que la solution fonctionne.

- Ajoutez la balise « GraphicalPS » si vous publiez un **Runbook graphique** (et non un workflow graphique).

- Insérer un extrait de code PowerShell ou PowerShell Workflow dans la description à l’aide de l’icône **Insérer la section de code**.

- Le résumé pour le téléchargement s’affiche dans les résultats de la galerie de runbooks, vous devez donc fournir des informations détaillées qui aideront un utilisateur à identifier la fonctionnalité du runbook.

- Vous devez attribuer une à trois des balises suivantes pour le téléchargement. Le runbook est répertorié dans l’Assistant sous les catégories qui correspondent à ses balises. Toutes les balises ne figurant pas sur cette liste seront ignorées par l’Assistant. Si vous ne spécifiez pas les balises correspondantes, le runbook est répertorié dans la catégorie Autre.

 - Sauvegarde
 - Gestion de la capacité
 - Contrôle des modifications
 - Conformité
 - Environnements de développement / test
 - Récupération d’urgence
 - Analyse
 - Application de correctifs
 - Approvisionnement
 - Correction
 - Gestion du cycle de vie des machines virtuelles


- Automation met à jour la galerie toutes les heures, donc vous ne verrez pas vos contributions immédiatement.

## Modules dans PowerShell Gallery

Les modules PowerShell contiennent des applets de commande que vous pouvez utiliser dans vos runbooks ; des modules existants que vous pouvez installer dans Azure Automation sont disponibles dans [PowerShell Gallery](http://www.powershellgallery.com). Vous pouvez lancer cette galerie à partir du portail Azure et les installer directement dans Azure Automation, ou vous pouvez les télécharger et les installer manuellement. Vous ne pouvez pas installer les modules directement depuis le portail Azure Classic, mais vous pouvez les télécharger et les installer comme vous le feriez avec tout autre module.

### Pour importer un module à partir d’Automation Module Gallery avec le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Cliquez sur la vignette **Ressources** pour ouvrir la liste des ressources.
3. Cliquez sur la vignette **Modules** pour ouvrir la liste des modules.
4. Cliquez sur le bouton **Parcourir la galerie**. Le panneau Parcourir la galerie s’affiche.

    ![Galerie du module](media/automation-runbook-gallery/modules-blade.png) <br>
5. Une fois que vous avez lancé le panneau Parcourir la galerie, vous pouvez exécuter une recherche à l’aide des champs suivants :

   - Nom du module
   - Balises
   - Auteur
   - Nom Applet de commande/Ressource DSC

6. Recherchez un module qui vous intéresse et sélectionnez-le pour en afficher les détails. Lorsque vous explorez un module spécifique, vous pouvez afficher plus d’informations le concernant, y compris un lien vers PowerShell Gallery, les dépendances requises et toutes les applets de commande et/ou les ressources DSC que contient le module.

    ![Détails du module PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>

7. Pour installer le module directement dans Azure Automation, cliquez sur le bouton **Importer**.

    ![Bouton Importer le module](media/automation-runbook-gallery/module-import-button.png)

8. Lorsque vous cliquez sur le bouton Importer, le nom du module que vous êtes sur le point d’importer s’affiche. Si toutes les dépendances sont installées, le bouton **OK** est actif. S'il manque des dépendances, vous devrez les importer avant de pouvoir importer ce module.
9. Cliquez sur **OK** pour importer le module et le panneau du module est lancé. Quand Azure Automation importe un module dans votre compte, il extrait les métadonnées sur le module et les applets de commande.

    ![Panneau Importer le module](media/automation-runbook-gallery/module-import-blade.png)

    Cette opération peut prendre quelques minutes, car chaque activité doit être extraite.
10. Vous recevrez une notification indiquant que le module est en cours de déploiement et une notification indiquant que le processus est terminé.
11. Une fois le module importé, les activités disponibles s’affichent et vous pouvez en utiliser les ressources dans vos runbooks et la Configuration d’état souhaité.

## Demande d’un runbook ou d’un module

Vous pouvez envoyer des demandes à [User Voice](https://feedback.azure.com/forums/246290-azure-automation/). Si vous avez besoin d’aide pour écrire un runbook ou si vous avez une question à propos de PowerShell, publiez une question sur notre [forum](http://social.msdn.microsoft.com/Forums/windowsazure/fr-FR/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## Articles connexes

- [Création ou importation d’un runbook dans Azure Automation](automation-creating-importing-runbook.md)
- [Apprentissage du workflow PowerShell](automation-powershell-workflow.md)

<!---HONumber=AcomDC_0601_2016-->