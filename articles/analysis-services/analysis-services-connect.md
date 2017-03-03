---
title: "Obtenir les données d’Azure Analysis Services | Microsoft Docs"
description: "Découvrez comment vous connecter à un serveur Analysis Services dans Azure et en obtenir les données."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/13/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 3992e327bbe887338234fc2d516b053f0750a151
ms.openlocfilehash: 118ea84abb0de095ac515ee98a643718ca54c043
ms.lasthandoff: 02/16/2017


---
# <a name="get-data-from-azure-analysis-services"></a>Obtenir les données d’Azure Analysis Services

Une fois que vous avez créé un serveur dans Azure avant d’y déployer un modèle tabulaire, les utilisateurs de votre entreprise sont prêts à s’y connecter et à explorer les données.

## <a name="data-providers-aka-client-libraries"></a>Fournisseurs de données (également appelés bibliothèques clientes)

Les applications clientes comme Power BI Desktop et Microsoft Excel recourent à des fournisseurs mis à jour AMO, ADOMD.NET et OLEDB pour se connecter à Analysis Services et interagir avec. Avec certaines versions plus anciennes d’Excel ou d’applications personnalisées, vous devrez éventuellement installer les fournisseurs de données les plus récents pour vous connecter à Azure Analysis Services. Pour plus d’informations, consultez [Fournisseurs de données](analysis-services-data-providers.md).

## <a name="server-name"></a>Nom du serveur

Lorsque vous créez un serveur Analysis Services dans Azure, vous spécifiez un nom unique ainsi que la région dans laquelle le serveur doit être créé. Lorsque vous spécifiez le nom du serveur dans une connexion, le schéma d’affectation de noms du serveur est le suivant :

```
<protocol>://<region>/<servername>
```
 Où protocole est la chaîne **asazure**, la région est l’URI de la région dans laquelle le serveur a été créé (par exemple, pour États-Unis de l'Ouest, westus.asazure.windows.net) et nomserveur est le nom de votre serveur unique au sein de la région.

## <a name="get-the-server-name"></a>Obtenir le nom du serveur

Avant de vous connecter, vous devez obtenir le nom du serveur. Dans **Portail Azure** > Serveur > **Présentation** > **Nom du serveur**, copiez le nom du serveur entier. Si d’autres utilisateurs de votre entreprise sont connectent également à ce serveur, vous souhaiterez partager ce nom de serveur avec eux. Lorsque vous spécifiez un nom de serveur, le chemin d’accès complet doit être utilisé.

![Obtenir le nom du serveur dans Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Se connecter à Power BI Desktop

> [!NOTE]
> Cette fonctionnalité est une version préliminaire.
> 
> 

1. Dans [Power BI Desktop](https://powerbi.microsoft.com/desktop/), cliquez sur **Obtenir les données** > **Bases de données** > **Azure Analysis Services**.
2. Dans **Serveur**, collez le nom du serveur à partir du Presse-papiers.
3. Dans **Base de données**, si vous connaissez le nom de la base de données de modèle tabulaire ou de la perspective à laquelle vous souhaitez vous connecter, collez-le ici. Sinon, vous pouvez laisser ce champ vide. Vous pouvez sélectionner une base de données ou une perspective dans l’écran suivant.
4. Laissez l’option par défaut **Connexion active** sélectionnée, puis appuyez sur **Se connecter**. Si vous êtes invité à entrer un compte, entrez votre compte professionnel.
5. Dans le **Navigateur**, développez le serveur, puis sélectionnez le modèle ou la perspective auquel vous souhaitez vous connecter, puis cliquez sur **Se connecter**. Un clic sur un modèle ou une perspective montre tous les objets de cette vue.

## <a name="connect-in-power-bi"></a>Se connecter à Power BI

1. Créez un fichier Power BI Desktop qui inclut une connexion active à votre modèle sur votre serveur.
2. Dans [Power BI](https://powerbi.microsoft.com), cliquez sur **Obtenir les données** > **Fichiers**. Recherchez et sélectionnez votre fichier.

## <a name="connect-in-excel"></a>Se connecter dans Excel

La connexion au serveur Azure Analysis Services dans Excel est prise en charge grâce à la fonction Obtenir les données dans Excel 2016 ou Power Query dans les versions antérieures. Le [fournisseur MSOLAP.7](analysis-services-data-providers.md) est obligatoire. La connexion à l’aide de l’Assistant d’importation de table dans Power Pivot n’est pas prise en charge.

> [!NOTE]
> Certaines organisations déploient les mises à jour Office 365 sur le canal différé, ce qui signifie que des mises à jour de version sont retardées jusqu'à quatre mois après la version actuelle. Pour la version d’Excel 2016 build 1609.7369.2115 et versions antérieures, ou Excel 2013, vous pouvez créer un fichier Office Data Connection (.odc) et mettre à jour manuellement le fournisseur MSOLAP.7 pour se connecter à un serveur Azure Analysis Services. Pour en savoir plus, consultez [Créer un fichier .odc](analysis-services-odc.md).
> 
> 

**Connexion à partir d'Excel 2016**

1. Dans Excel 2016, sur le ruban **Données**, cliquez sur **Obtenir les données externes** > **À partir d’autres sources** > **À partir d’Analysis Services**.
2. Dans l’Assistant de connexion de données, dans **Nom du serveur**, collez le nom du serveur à partir du Presse-papiers. Ensuite, dans **Informations d’identification d’ouverture de session**, sélectionnez **Utiliser le nom d’utilisateur et le mot de passe suivants**, puis tapez le nom d’utilisateur professionnel, par exemple nancy@adventureworks.com,, et le mot de passe.

    ![Se connecter dans l’ouverture de session Excel](./media/analysis-services-connect/aas-connect-excel-logon.png)
3. Dans **Sélectionner une base de données et une table**, sélectionnez la base de données et un modèle ou une perspective, puis cliquez sur **Terminer**.
   
    ![Se connecter dans le modèle de sélection Excel](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>Chaîne de connexion

Lorsque vous vous connectez à Azure Analysis Services à l’aide du modèle d’objet tabulaire, utilisez les formats de chaîne de connexion suivants :

###### <a name="integrated-azure-active-directory-authentication"></a>Authentification Azure Active Directory intégrée

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
L’authentification intégrée utilisera le cache d’informations d’identification Azure Active Directory s’il est disponible. Si ce n’est pas le cas, la fenêtre de connexion Azure s’affiche.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Authentification Active Directory Azure avec le nom d’utilisateur et le mot de passe

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="next-steps"></a>Étapes suivantes

[Gérer votre serveur](analysis-services-manage.md)


