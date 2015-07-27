Présentation des contraintes d'utilisation et autres limites de service pour le service Azure Active Directory.

### Répertoires

Un utilisateur ne peut être associé qu’à un maximum de 20 répertoires Azure Active Directory. Cette limite peut être atteinte dans un des exemples suivants :

- Un seul utilisateur crée 20 répertoires.
- Un seul utilisateur est ajouté à 20 répertoires en tant que membre.
- Un seul utilisateur crée 10 répertoires et est ajouté ultérieurement par d'autres à 10 autres répertoires.

### Objets

- Il n’existe pas de limite pour les abonnés d’Azure Active Directory Premium ou Azure Active Directory de base, Enterprise Mobility Suite, Office 365, Microsoft Intune ou tout autre service en ligne Microsoft associé à Azure Active Directory pour les services de répertoire.
- 500 000 objets au maximum peuvent être utilisés dans un répertoire unique avec l'édition gratuite d'Azure Active Directory.
- Un utilisateur non administrateur peut créer jusqu'à 250 objets.

###Extensions de schéma

Actuellement, les entités « Utilisateur », « Groupe », « TenantDetail », « Périphérique », « Application » et « ServicePrincipal » peuvent être étendues avec des attributs à valeur unique de type « binaire » ou « chaîne ». Ces entités incluent les limitations suivantes :

- Les extensions de type chaîne peuvent avoir jusqu'à 256 caractères.
- Les extensions de type binaire sont limitées à 256 octets.
- 100 valeurs d'extension (pour TOUT type et TOUTE application) peuvent être écrites pour un objet unique.
- Les extensions de schéma sont disponibles uniquement dans l’aperçu de la version 1.21 de l’API Graph. L’accès en écriture pour enregistrer une extension doit être accordé à l'application.

### Applications

10 utilisateurs au maximum peuvent être propriétaires d'une seule application.

### Groupes

- 10 utilisateurs au maximum peuvent être propriétaires d'un seul groupe.
- Un nombre quelconque d'objets peuvent être membre d'un groupe unique dans Azure Active Directory.


> [AZURE.NOTE]
>
> Il existe une limite pour le nombre d’objets que vous pouvez synchroniser à partir de votre Active Directory local vers Azure Active Directory.
>
> - Si vous utilisez DirSync, la limite se situe à 15 000 utilisateurs.
> - Si vous utilisez Azure AD Connect, la limite se situe à 50 000 utilisateurs.

<br/>
### Panneau d'accès

- Il n’existe pas de limite au nombre d’applications par utilisateur final qui peuvent être affichées dans le Panneau d’accès pour les abonnés à Azure Active Directory Premium ou Enterprise Mobility Suite.
- Un maximum de 10 vignettes de l’application (exemples : Box, Salesforce, ou Dropbox) peuvent être consultées dans le Panneau d’accès pour chaque utilisateur final avec l’édition gratuite ou de base d’Azure Active Directory. Cette limite ne s'applique pas aux comptes d'administrateur.

### Rapports

1 000 lignes au maximum peuvent être affichées ou téléchargées dans un rapport. Toutes les données supplémentaires sont tronquées.

<!---HONumber=July15_HO3-->