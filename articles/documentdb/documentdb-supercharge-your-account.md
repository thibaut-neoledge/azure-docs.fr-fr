---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-performance-levels
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 94ae24e605f99e23766702a3954db567bb64bb51
ms.openlocfilehash: 1c055187405b9c858676b2b80e4bc5c4157ef580


---
# <a name="migrate-your-documentdb-s1-s2-or-s3-account"></a>Migrer votre compte DocumentDB S1, S2 ou S3
Suivez ces étapes pour tirer parti d’un débit plus élevé pour votre compte Azure DocumentDB en migrant vers le niveau tarifaire Standard. Avec peu ou pas de frais supplémentaires, vous pouvez augmenter le débit de votre compte S1 existant 250 à 400 [unités de requête/s](documentdb-request-units.md) ou plus ! Tous les comptes Standard ont la possibilité de mettre à l’échelle le débit en fonction des besoins des applications. Vous ne devez plus choisir parmi des options de débit prédéfinies. Vous pouvez effectuer une mise à l’échelle à chaque fois que cela est nécessaire pour atteindre le débit requis par votre application. 

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Passer aux performances définies par l’utilisateur dans le portail Azure
1. Dans votre navigateur, accédez au [**portail Azure**](https://portal.azure.com). 
2. Dans le menu gauche, cliquez sur **NoSQL (DocumentDB)** ou sur **Autres services**, accédez à **Bases de données**, puis sélectionnez **NoSQL (DocumentDB)**.   
3. Dans le panneau **NoSQL (DocumentDB)** , sélectionnez le compte à modifier.
4. Dans le nouveau panneau, dans le menu situé sous **Collections**, cliquez sur **Mettre à l’échelle**. 

      ![Capture d’écran des panneaux Paramètres DocumentDB et Choisir votre niveau tarifaire](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)
5. Procédez comme indiqué dans la capture d’écran suivante : 

 - Dans le nouveau panneau, utilisez le menu déroulant pour sélectionner la collection avec le niveau tarifaire S1, S2 ou S3. 
 - Cliquez sur **Niveau tarifaire S1**, **S2** ou **S3**.
 - Dans le panneau **Choisir votre niveau tarifaire**, cliquez sur **Standard**, puis cliquez sur **Sélectionner** pour enregistrer vos modifications.
   
6. De retour dans le panneau **Mettre à l’échelle**, la zone **Débit (UR/s)** est affichée avec la valeur par défaut 400 et le **niveau tarifaire** est défini sur **Standard**.  Vous pouvez définir le débit sur la valeur requise par votre application. Nous vous recommandons d’utiliser l’[outil de planification de la capacité DocumentDB](https://www.documentdb.com/capacityplanner) pour déterminer le nombre [d’unités de requête](documentdb-request-units.md) par seconde dont votre application a besoin. L’**Estimation de la facture mensuelle** au bas de la page se met à jour automatiquement pour donner une estimation du coût mensuel. Cliquez sur **Enregistrer** pour enregistrer vos modifications. 
      
    ![Capture d’écran du panneau Paramètres montrant où modifier la valeur de débit](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)
7. La collection est mise à l’échelle de façon à répondre aux nouveaux besoins et un message de réussite s’affiche.  
   
    ![Capture d’écran d’une collection modifiée dans le panneau Base de données](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Pour plus d’informations sur les modifications liées aux débits définis par l’utilisateur ou prédéfinis, consultez l’article de blog [DocumentDB : tout ce que vous devez savoir sur l’utilisation des nouvelles options de tarification](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon de partitionner les données et d’atteindre une échelle globale avec DocumentDB, consultez [Partitionnement et mise à l’échelle dans Azure DocumentDB](documentdb-partition-data.md).



<!--HONumber=Feb17_HO2-->


