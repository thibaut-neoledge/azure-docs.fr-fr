Les données de votre compte de stockage Microsoft Azure sont toujours répliquées pour garantir une durabilité et une haute disponibilité, conformément au [contrat de niveau de service (SLA) Azure Storage](http://azure.microsoft.com/support/legal/sla/), même en cas de défaillances matérielles temporaires. Lorsque vous créez un compte de stockage, vous devez sélectionner une des options de réplication suivantes :

- **Stockage localement redondant (LRS).** Le stockage localement redondant effectue trois copies de vos données. Le stockage LRS est répliqué trois fois par installation et par région. Il protège vos données des défaillances matérielles normales, mais pas des pannes susceptibles de survenir dans une installation donnée.  
  
	Vous pouvez bénéficier d'une réduction pour le stockage LRS. Pour une durabilité maximale, nous vous recommandons d’utiliser le stockage géo-redondant décrit ci-dessous.


- **Stockage redondant dans une zone (ZRS).** Le stockage redondant dans une zone effectue trois copies de vos données. Le stockage ZRS est répliqué trois fois dans deux ou trois installations au sein d’une même région ou dans deux régions distinctes, ce qui confère aux données une durabilité supérieure à celle offerte par le stockage LRS. Le stockage ZRS garantie la durabilité de vos données au sein d'une même région.

	Le stockage ZRS offre un niveau de durabilité supérieur à celui du stockage LRS ; toutefois, pour une durabilité maximale, nous vous recommandons d'utiliser le stockage géo-redondant décrit ci-dessous.

	> [AZURE.NOTE]Actuellement, le stockage ZRS est uniquement disponible pour les objets blob de blocs.
	> 
	> Une fois votre compte de stockage créé et la réplication ZRS sélectionné, vous ne pouvez plus revenir en arrière et choisir un autre type de réplication (ou inversement).

- **Stockage géo-redondant (GRS)**. Le stockage géo-redondant est activé par défaut sur votre compte de stockage lors de la création de celui-ci. Le stockage GRS effectue six copies de vos données. Avec le stockage GRS, vos données sont répliquées trois fois dans la région principale et trois fois dans une région secondaire située à des centaines de kilomètres de la région principale, ce qui confère aux données le plus haut niveau de durabilité disponible. En cas de défaillance dans la région principale, Azure Storage bascule vers la région secondaire. Le stockage GRS assure la durabilité de vos données dans deux régions distinctes.


- **Stockage géo-redondant avec accès en lecture (RA-GRS)**. Le stockage géo-redondant avec accès en lecture réplique vos données dans un emplacement secondaire et permet d’y accéder en lecture. Le stockage géo-redondant avec accès en lecture offre un accès aux données, qu’elles se situent dans l’emplacement principal ou secondaire, si l’un des deux n’est pas disponible.

	> [AZURE.IMPORTANT]Vous pouvez modifier le mode de réplication de vos données après la création de votre compte de stockage, sauf si vous avez défini la réplication ZRS à cette étape. Toutefois, notez qu’il vous faudra éventuellement subir un coût forfaitaire supplémentaire lié au transfert de données si vous passez de LRS à GRS ou RA-GRS.
 
Pour en savoir plus sur les options de réplication du stockage, consultez la page [Options de redondance d’Azure Storage](../articles/storage/storage-redundancy.md).

Pour plus d’informations sur la tarification relative à la réplication du compte de stockage, consultez la page [Tarification – Stockage](http://azure.microsoft.com/pricing/details/storage/).

Pour plus d’informations sur la durabilité avec Azure Storage, consultez la page [Document SOSP sur Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

<!---HONumber=July15_HO1-->