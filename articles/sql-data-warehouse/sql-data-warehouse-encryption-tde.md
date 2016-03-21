<properties
   pageTitle="Mise en route avec le chiffrement transparent des données (TDE) dans SQL Data Warehouse | Microsoft Azure"
   description="Mise en route avec le chiffrement transparent des données (TDE) dans SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/03/2016" 
   ms.author="mausher;barbkess;sonyama"/>

# Mise en route avec le chiffrement transparent des données (TDE) dans SQL Data Warehouse
> [AZURE.SELECTOR]
- [Portail Azure Classic](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

La fonction de chiffrement transparent des données (TDE) d’Azure SQL Data Warehouse protège le système contre toute menace d’activité malveillante, en effectuant un chiffrement et un déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions au repos, sans exiger de modification de l’application.

Le chiffrement transparent des données chiffre le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. Dans la base de données SQL, la clé de chiffrement de base de données est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur de base de données SQL. Microsoft alterne automatiquement ces certificats au moins tous les 90 jours. Pour obtenir une description générale du chiffrement transparent des données, consultez [Chiffrement transparent des données (TDE)].

##Activation du chiffrement

Pour activer le chiffrement transparent des données pour SQL Data Warehouse, procédez comme suit :

1. Ouvrez la base de données dans le [Portail Azure Classic](https://portal.azure.com)
2. Dans le panneau de la base de données, cliquez sur le bouton **Paramètres**
3. Sélectionnez l’option **Chiffrement transparent des données** ![][1]
4. Sélectionnez le paramètre **Activé** ![][2]
5. Sélectionnez **Enregistrer** ![][3]  

##Désactivation du chiffrement

Pour désactiver le chiffrement transparent des données pour SQL Data Warehouse, procédez comme suit :

1. Ouvrez la base de données dans le [Portail Azure Classic](https://portal.azure.com)
2. Dans le panneau de la base de données, cliquez sur le bouton **Paramètres**
3. Sélectionnez l’option **Chiffrement transparent des données** ![][1]
4. Sélectionnez le paramètre **Désactivé** ![][4]
5. Sélectionnez **Enregistrer** ![][5]  




<!--Anchors-->
[Chiffrement transparent des données (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0309_2016-->