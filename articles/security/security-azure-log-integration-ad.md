---
title: "Intégration des journaux Azure (AZLOG) avec les journaux d’audit Active Directory | Microsoft Docs"
description: "Découvrez comment installer le service d’intégration des journaux Azure et intégrer les journaux à partir des journaux d’audit Azure."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/08/2017
ms.author: barclayn
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 9a4fa6ea1784ab0247659d5498c6fe3ae30355c4
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---

#<a name="integrate-azure-active-directory-audit-logs"></a>Intégrer des journaux d’audit Azure Active Directory

Les événements d’audit Azure Active Directory vous aident à identifier les actions privilégiées survenues dans Azure Active Directory. Vous pouvez voir les types d’événements que vous pouvez suivre en examinant [les événements du rapport d’audit Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)

>[!NOTE]
Vous devez lire l’article [Bien démarrer](security-azure-log-integration-get-started.md) et effectuez les étapes qui y sont citées avant d’effectuer les étapes décrites dans cet article.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Procédure d’intégration des journaux d’audit Azure Active Directory

1. Ouvrez l’invite de commandes et cd dans **c:\Program Files\Microsoft Azure Log Integration**
2. Exécutez la commande suivante :

 ``azlog createazureid``

 Cette commande vous invite à entrer votre nom d’utilisateur Azure. La commande crée ensuite un principal du service Azure Active Directory dans les clients Azure AD qui hébergent les abonnements Azure dans lesquels l’utilisateur connecté est un administrateur, un coadministrateur ou un propriétaire. La commande échouera si l’utilisateur connecté est seulement un utilisateur invité dans le locataire Azure AD. L’authentification à Azure s’effectue par le biais d’Azure Azure Active Directory (AD). La création d’un principal du service pour l’intégration de journaux Azure crée l’identité Azure AD qui reçoit l’accès en lecture aux abonnements Azure.

3. Exécutez la commande avec votre ID de locataire. Vous devez être membre du rôle administrateur de locataire pour exécuter la commande.

``Azlog.exe authorizedirectoryreader tenantId``

Exemple

``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

Vérifiez les dossiers suivants pour confirmer que les fichiers JSON de journaux d’audit Azure Active Directory sont créés dans :

* **C:\Users\azlog\AzureActiveDirectoryJson**
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Vous trouverez ci-dessous une vidéo répertoriant les étapes décrites dans cet article.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


>[!NOTE]
Vous devez contacter votre fournisseur SIEM pour obtenir des instructions précises sur l’ajout des informations de vos fichiers JSON dans votre système SIEM.

L’aide de la Communauté est disponible via le [forum MSDN d’intégration des journaux Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Le forum permet aux membres de la communauté AzLog de s’entraider, grâce à des questions, des réponses, des conseils et des astuces, afin de profiter au mieux de l’intégration des journaux Azure. En outre, l’équipe d’intégration des journaux Azure supervise ce forum et apporte son aide lorsque cela est possible.

Vous pouvez également ouvrir une [demande de support](../azure-supportability/how-to-create-azure-support-request.md). Pour ce faire, sélectionnez **intégration du journal** comme service pour lequel vous demandez de l’aide.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’intégration des journaux Azure, consultez les documents suivants :

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) : Centre de téléchargement pour plus d’informations, la configuration système requise et les instructions d’installation sur l’intégration des journaux Azure.
* [Introduction à l’intégration de journaux Azure](security-azure-log-integration-overview.md) : ce document présente l’intégration des journaux Azure, ses principales fonctionnalités et son fonctionnement.
* [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : ce billet de blog vous montre comment configurer l’intégration des journaux Azure pour travailler avec des solutions de partenaires Splunk, HP ArcSight et IBM QRadar.
* [FAQ de l’intégration des journaux Azure](security-azure-log-integration-faq.md) : ce forum aux questions répond aux questions sur l’intégration des journaux Azure.
* [Intégration des alertes du Security Center avec les journaux Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) : ce document montre comment synchroniser les alertes du Security Center, ainsi que les événements de sécurité des machines virtuelles collectés par Azure Diagnostics et les journaux d’audit Azure dans leur solution SIEM ou Log Analytics.
* [Nouvelles fonctionnalités des diagnostics Azure et des journaux d’Audit Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : ce billet de blog présente les journaux d’Audit Azure et autres fonctionnalités pour vous permettre de mieux connaître les opérations de vos ressources Azure.

