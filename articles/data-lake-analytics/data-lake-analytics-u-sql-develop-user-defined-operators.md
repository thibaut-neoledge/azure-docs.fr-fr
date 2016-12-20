---
title: "Développer des opérateurs définis par l’utilisateur U-SQL pour des travaux Azure Data Lake Analytics | Microsoft Docs"
description: "Apprenez à développer des opérateurs définis par l’utilisateur pour les utiliser et les réutiliser dans des travaux Data Lake Analytics. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 88a00c2b0a5aac85bbcaef5b21b10f44121c7d38


---
# <a name="develop-u-sql-user-defined-operators-for-azure-data-lake-analytics-jobs"></a>Développer des opérateurs définis par l’utilisateur U-SQL pour des travaux Azure Data Lake Analytics
Apprenez à développer des opérateurs définis par l’utilisateur pour les utiliser et les réutiliser dans des travaux Data Lake Analytics. Vous allez développer un opérateur personnalisé pour convertir des noms de pays.

## <a name="prerequisites"></a>Composants requis
* Visual Studio 2015, Visual Studio 2013 mise à jour 4 ou Visual Studio 2012 avec Visual C++.
* Kit de développement logiciel (SDK) Microsoft Azure pour .NET version 2.5 ou ultérieure.  Installez-le avec Web Platform Installer.
* Un compte Data Lake Analytics.  Consulter [Prise en main des analyses Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).
* Parcourez le didacticiel [Prise en main d’Azure Data Lake Analytics avec U-SQL Studio](data-lake-analytics-u-sql-get-started.md) .
* Connexion à Azure.
* Téléchargez la source de données, consultez [Prise en main d’Azure Data Lake Analytics avec U-SQL Studio](data-lake-analytics-u-sql-get-started.md). 

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>Définir et utiliser un opérateur défini par l’utilisateur en U-SQL
**Pour créer et soumettre un travail U-SQL**

1. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
2. Sélectionnez le type **Projet U-SQL** .

    ![nouveau projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Cliquez sur **OK**. Visual Studio crée une solution avec un fichier Script.usql.
4. À partir de **l’Explorateur de solutions**, développez Script.usql, puis double-cliquez sur **Script.usql.cs**.
5. Collez le code suivant dans le fichier :

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Ouvrez Script.usql et collez le script U-SQL suivant :

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Générer le script**.
8. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Soumettre le script**.
9. Si vous ne vous êtes pas connecté à votre abonnement Azure, vous serez invité à entrer vos informations d’identification de compte Azure.
10. Cliquez sur **Envoyer**. Les résultats de l’envoi et le lien vers le travail sont disponibles dans la fenêtre Résultats quand l’envoi est terminé.
11. Vous devez cliquer sur le bouton Actualiser pour afficher le dernier état du travail et actualiser l’écran.

**Pour voir la sortie du travail**

1. Dans **l’Explorateur de serveurs**, développez successivement **Azure**, **Data Lake Analytics**, votre compte Data Lake Analytics, **Comptes de stockage**, puis cliquez avec le bouton droit sur le stockage par défaut et sur **Explorer**.
2. Développez des exemples, des sorties, puis double-cliquez sur **Drivers.csv**.

## <a name="see-also"></a>Voir aussi
* [Prise en main de Data Lake Analytics à l’aide de PowerShell](data-lake-analytics-get-started-powershell.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Utiliser les outils Data Lake pour Visual Studio pour le développement d’applications U-SQL](data-lake-analytics-data-lake-tools-get-started.md)



<!--HONumber=Nov16_HO3-->


