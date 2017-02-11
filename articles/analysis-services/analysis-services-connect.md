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
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 6b65689c75247af81e916730f125ff39bb49489f


---
# <a name="get-data-from-azure-analysis-services"></a>Obtenir les données d’Azure Analysis Services
Une fois que vous avez créé un serveur dans Azure avant d’y déployer un modèle tabulaire, les utilisateurs de votre entreprise sont prêts à s’y connecter et à explorer les données.

Azure Analysis Services prend en charge les connexions client à l’aide de [modèles d’objet mis à jour](#client-libraries) (TOM, AMO, Adomd.Net ou MSOLAP) pour vous connecter au serveur via xmla. Exemple : Power BI, Power BI Desktop, Excel ou n’importe quelle application cliente tierce prenant en charge les modèles d’objet.

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
La connexion au serveur Azure Analysis Services dans Excel est prise en charge grâce à la fonction Obtenir les données dans Excel 2016 ou Power Query dans les versions antérieures. Le [fournisseur MSOLAP.7](https://aka.ms/msolap) est obligatoire. La connexion à l’aide de l’Assistant d’importation de table dans Power Pivot n’est pas prise en charge.

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

## <a name="client-libraries"></a>Bibliothèques clientes
Lorsque vous vous connectez à Azure Analysis Services à partir d’Excel ou d’autres interfaces telles que TOM, AsCmd, ADOMD.NET, vous devrez peut-être installer les dernières bibliothèques clientes du fournisseur. Obtenez les dernières bibliothèques suivantes :  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Étapes suivantes
[Gérer votre serveur](analysis-services-manage.md)




<!--HONumber=Nov16_HO3-->


