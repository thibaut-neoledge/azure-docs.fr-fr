Les données dans votre compte de stockage sont répliquées pour garantir une durabilité, ainsi qu'une haute disponibilité, et répondre ainsi au [contrat de niveau de service Azure Storage][contrat de niveau de service Azure Storage], y compris face aux défaillances matérielles temporaires. Azure Storage est déployé dans quinze régions du monde et prend en charge la réplication des données d'une région à une autre. Plusieurs options s'offrent à vous pour répliquer les données dans votre compte de stockage :

-   *Le stockage localement redondant (LRS)* effectue trois copies de vos données. Le stockage LRS est répliqué trois fois par installation et par région. Il protège vos données des défaillances matérielles normales, mais pas des pannes susceptibles de survenir dans une installation donnée.

    Vous pouvez bénéficier d'une réduction pour le stockage LRS. Pour une durabilité maximale, nous vous recommandons d'utiliser le stockage géo-redondant décrit ci-dessous.

-   *Le stockage redondant dans une zone (ZRS)* effectue trois copies de vos données. Le stockage ZRS est répliqué trois fois dans deux ou trois installations au sein d'une même région ou dans deux régions distinctes, ce qui confère aux données une durabilité supérieure à celle offerte par le stockage LRS. Le stockage ZRS garantie la durabilité de vos données au sein d'une même région.

    Le stockage ZRS offre un niveau de durabilité supérieur à celui du stockage LRS ; toutefois, pour une durabilité maximale, nous vous recommandons d'utiliser le stockage géo-redondant décrit ci-dessous.

    > [WACOM.NOTE] Actuellement, le stockage ZRS est uniquement disponible pour les objets blob de blocs. Notez qu'une fois votre compte de stockage créé et la réplication ZRS sélectionnée, vous ne pouvez pas revenir en arrière et utiliser un autre type de réplication (et vice-versa).

-   *Le stockage géo-redondant (GRS)* est activé par défaut sur votre compte de stockage lors de la création de celui-ci. Le stockage GRS effectue six copies de vos données. Avec le stockage GRS, vos données sont répliquées trois fois dans la région principale et trois fois dans une région secondaire située à des centaines de kilomètres de la région principale, ce qui confère aux données le plus haut niveau de durabilité disponible. En cas de défaillance dans la région principale, Azure Storage bascule vers la région secondaire. Le stockage GRS assure la durabilité de vos données dans deux régions distinctes.

    > [WACOM.NOTE] Pour une durabilité maximale, nous vous recommandons d'utiliser le stockage GRS plutôt que le stockage ZRS ou LRS.

-   Le *stockage géo-redondant avec accès en lecture (RA-GRS)* offre tous les avantages du stockage géo-redondant indiqués plus haut, mais il autorise également l'accès en lecture aux données dans la région secondaire si la région principale devient indisponible. Le stockage géo-redondant avec accès en lecture est recommandé pour bénéficier d'une disponibilité maximale en plus de la durabilité.

Pour plus d'informations sur les options de réplication, consultez le [Blog de l'équipe Azure Storage][Blog de l'équipe Azure Storage] et la rubrique [Options de redondance d'Azure Storage][Options de redondance d'Azure Storage].

Les prix des différentes options de réplication sont disponibles sur la page [Tarification et stockage][Tarification et stockage].

  [contrat de niveau de service Azure Storage]: /fr-fr/support/legal/sla/
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
  [Options de redondance d'Azure Storage]: http://msdn.microsoft.com/fr-fr/library/azure/dn727290.aspx
  [Tarification et stockage]: /fr-fr/pricing/details/storage/
