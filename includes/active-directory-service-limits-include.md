Présentation des contraintes d'utilisation et autres limites de service pour le service Azure Active Directory.

| Catégorie | Limites |
| --- | --- |
| Répertoires |Un utilisateur ne peut être associé qu’à un maximum de 20 répertoires Azure Active Directory.<br />Exemples de combinaisons possibles : <ul> <li>Un seul utilisateur crée 20 répertoires.</li><li>Un seul utilisateur est ajouté à 20 répertoires en tant que membre.</li><li>Un seul utilisateur crée 10 répertoires et est ajouté ultérieurement par d'autres à 10 autres répertoires.</li></ul> |
| Objets |<ul><li>L’édition gratuite d’Azure Active Directory permet d’utiliser 500 000 objets au maximum dans un répertoire.</li><li>Un utilisateur non administrateur peut créer jusqu’à 250 objets.</li></ul> |
| Extensions de schéma |<ul><li>Les extensions de type chaîne peuvent avoir jusqu’à 256 caractères. </li><li>Les extensions de type binaire sont limitées à 256 octets.</li><li>100 valeurs d'extension (pour TOUT type et TOUTE application) peuvent être écrites pour un objet unique.</li><li>Actuellement, seules les entités « Utilisateur », « Groupe », « TenantDetail », « Périphérique », « Application » et « ServicePrincipal » peuvent être étendues avec des attributs à valeur unique de type « binaire » ou « chaîne ».</li><li>Les extensions de schéma sont disponibles uniquement dans l’aperçu de la version 1.21 de l’API Graph. L’accès en écriture pour enregistrer une extension doit être accordé à l’application.</li></ul> |
| Applications |10 utilisateurs au maximum peuvent être propriétaires d'une seule application. |
| Groupes |<ul><li>10 utilisateurs au maximum peuvent être propriétaires d'un seul groupe.</li><li>Un nombre quelconque d'objets peuvent être membre d'un groupe unique dans Azure Active Directory.</li><li>Le nombre de membres dans un groupe, que vous pouvez synchroniser à partir de votre annuaire Active Directory local vers Azure Active Directory est limité à 15 000, avec Azure Active Directory Synchronization (DirSync).</li><li>Le nombre de membres dans un groupe, que vous pouvez synchroniser à partir de votre annuaire Active Directory local vers Azure Active Directory à l’aide d’Azure AD Connect, est limité à 50 000.</li></ul> |
| Volet d'accès |<ul><li>Le nombre d’applications affichables dans le Panneau d’accès par utilisateur final pour les abonnés à Azure Active Directory Premium ou Enterprise Mobility Suite est illimité.</li><li>Un maximum de 10 vignettes d’application (exemples : Box, Salesforce, ou Dropbox) peuvent être consultées dans le Panneau d’accès pour chaque utilisateur disposant d’une licence de l’édition gratuite ou de base d’Azure Active Directory. Cette limite ne s'applique pas aux comptes d'administrateur.</li></ul> |
| Rapports | 1 000 lignes au maximum peuvent être affichées ou téléchargées dans un rapport. Toutes les données supplémentaires sont tronquées. |
| Unités administratives | Un objet peut faire partie de 30 unités administratives maximum. |


<!--HONumber=Feb17_HO2-->


