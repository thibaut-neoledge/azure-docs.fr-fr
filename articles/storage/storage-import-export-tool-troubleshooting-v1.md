---
title: "Résolution des problèmes de l’outil Azure Import-Export | Microsoft Docs"
description: "Découvrez les problèmes courants que rencontrent les utilisateurs avec l’outil Import-Export et comment les gérer."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 9aca8aad3f268bf21f3bad9fa22821f5d825f99d
ms.openlocfilehash: 53755e0048cfd80d9f930419a105893e5b34dc41
ms.lasthandoff: 02/16/2017


---

# <a name="troubleshooting-the-azure-import-export-tool"></a>Résolution des problèmes associés à l’outil d’importation/d’exportation Azure
L’outil Microsoft Azure Import/Export renvoie des messages d’erreur s’il rencontre des problèmes. Cette rubrique répertorie certains des problèmes courants que les utilisateurs peuvent rencontrer.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Une session de copie échoue, que faire ?  
 Lorsqu’une session de copie échoue, il existe deux possibilités :  
  
 Si une nouvelle tentative est possible, par exemple si le partage réseau était déconnecté pendant une courte période et qu’il est de nouveau en ligne, vous pouvez reprendre la session de copie. Si l’erreur n’est pas récupérable, par exemple si vous avez spécifié un mauvais répertoire de fichier source dans les paramètres de ligne de commande, vous devez abandonner la session de copie. Consultez la page [Préparer des disques durs pour un travail d’importation](storage-import-export-tool-preparing-hard-drives-import-v1.md) pour plus d’informations sur la reprise et l’abandon de sessions de copie.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Je n’arrive pas à reprendre ou à abandonner une session de copie.  
 S’il s’agit de la première session de copie d’un lecteur, le message d’erreur doit indiquer : « La première session de copie ne peut pas être reprise ou abandonnée ». Dans ce cas, vous pouvez supprimer l’ancien fichier journal et réexécuter la commande.  
  
 Si la session de copie n’est pas la première du lecteur, elle peut toujours être reprise ou abandonnée.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>J’ai perdu le fichier journal, est-il quand même possible de créer le travail ?  
 Le fichier journal d’un lecteur contient les informations complètes de copie des données sur ce lecteur ; il est nécessaire pour ajouter d’autres fichiers au lecteur et sera utilisé pour créer un travail d’importation. Si le fichier journal est perdu, vous devrez répéter toutes les sessions de copie du lecteur.  
  
## <a name="see-also"></a>Voir aussi  
 [Configuration de l’outil Azure Import/Export](storage-import-export-tool-setup-v1.md)   
 [Préparation des disques durs pour un travail d’importation](storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 [Consultation de l’état du travail avec les fichiers journaux de copie](storage-import-export-tool-reviewing-job-status-v1.md)   
 [Réparation d’un travail d’importation](storage-import-export-tool-repairing-an-import-job-v1.md)   
 [Réparation d’un travail d’exportation](storage-import-export-tool-repairing-an-export-job-v1.md)

