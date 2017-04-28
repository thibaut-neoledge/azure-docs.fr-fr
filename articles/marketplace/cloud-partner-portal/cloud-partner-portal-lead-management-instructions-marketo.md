---
title: "Instructions de gestion des prospects pour Marketo sur la Place de marché Microsoft Azure | Microsoft Docs"
description: "Cet article guide les éditeurs pas à pas dans la procédure de configuration de la gestion des prospects avec Marketo."
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 77c9bdf6b2634e222592ac0818f608ee3cb3fe09
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-marketo"></a>Instructions de gestion des prospects pour Marketo

Ce document fournit des instructions sur la configuration de votre système Marketo afin que Microsoft puisse vous fournir des prospects. 

1. Accédez à « Design Studio » dans Marketo. <br/>
![Image d’installation de Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

2. Cliquez sur « Nouveau formulaire » <br/>
![Image de nouveau formulaire Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

3. Complétez les champs de la fenêtre contextuelle Nouveau formulaire <br/>
![Image de création de formulaire Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4. Cliquez sur « Terminer » dans le formulaire suivant. Ne vous souciez pas de la mise en forme ou des champs. <br/>
![Image d’achèvement de formulaire Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5. Approuvez et fermez.

6. Dans la page suivante, accédez à la section Code incorporé.<br/>
![Image de code incorporé Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7. Vous pouvez voir un code incorporé semblable à l’exemple ci-dessous : 

   ` <script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    `<form id="mktoForm_1179"></form> `

    `<script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>`

8. Utilisez les valeurs disponibles dans le code incorporé pour renseigner les champs <b>Server Id, Munchkin Id et Form Id</b> de Marketo sur le portail Microsoft Cloud Partner. <br\> <br/>

  L’exemple suivant vous aide à déterminer les ID à partir des exemples de données ci-dessus. <br\>

  serverId = **ys12** <br\>

 munchkinId = **123-PQR-789** <br\>

 formId = **1179** <br\> <br/>

