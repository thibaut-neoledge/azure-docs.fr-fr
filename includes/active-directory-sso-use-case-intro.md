Les organisations utilisent davantage de logiciels comme des applications [Software as a Service (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) pour augmenter la productivité car la technologie et les outils cloud deviennent de plus en plus disponibles. Le nombre d'applications SaaS ne cessant d’augmenter, il devient difficile pour les administrateurs de gérer les comptes et les droits d'accès, et pour les utilisateurs de se souvenir de leurs différents mots de passe. La gestion individuelle de ces applications augmente la charge de travail et constitue une tâche moins sécurisée.


- Les employés qui doivent effectuer le suivi de nombreux mots de passe ont tendance à utiliser des méthodes moins sécurisées pour les retenir, en écrivant leurs mots de passe ou en utilisant les mêmes mots de passe sur plusieurs comptes.

- Lors de l'arrivée d'un nouvel employé ou lorsqu’un employé quitte l’entreprise, l’approvisionnement de leur compte doit être activé ou désactivé individuellement.

- En outre, les employés peuvent commencer à utiliser des applications SaaS pour leur travail sans passer par le service informatique, ce qui signifie qu'ils créent leurs propres comptes sur des systèmes que les administrateurs système n'ont pas approuvés et qui ne sont pas surveillés.

L’authentification unique (SSO) constitue une solution pour répondre à tous ces défis. C’est le moyen le plus simple de gérer plusieurs applications et d’offrir aux utilisateurs une expérience de connexion homogène. Azure Active Directory (Azure AD) fournit une solution d'authentification unique robuste et propose de nombreuses applications préintégrées, ainsi que des didacticiels permettant aux administrateurs de rapidement configurer une nouvelle application et de commencer à approvisionner les utilisateurs.


## Intégration d'applications avec Azure Active Directory  

Azure AD vous permet d'intégrer vos applications et comptes approvisionnés. Pour cela, vous pouvez adopter l'une de ces deux approches.

- Si l'app est préintégrée dans la galerie d’applications, vous pouvez utiliser le portail pour configurer des applications et définir les paramètres nécessaires à l'authentification unique. Pour toute application de cette galerie, vous pouvez commencer par suivre les instructions détaillées présentées dans la galerie d’applications et dans le portail Azure pour activer l’authentification unique.

- Si l'app ne figure pas dans la galerie, vous pouvez toujours configurer la plupart des applications dans Azure AD en tant qu'app personnalisée. Cette configuration nécessite des connaissances techniques plus approfondies. Vous pouvez ajouter n’importe quelle application qui prend en charge SAML 2.0 comme application fédérée, ou bien toute application qui possède une page de connexion HTML comme étape d’authentification unique avec mot de passe.

Si les utilisateurs ont créé leurs propres comptes pour les applications SaaS qui ne sont pas gérées par le service informatique, l’outil de [Cloud App Discovery](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) offre une solution. Cet outil analyse le trafic web afin d'identifier les applications utilisées dans toute l'organisation ainsi que le nombre de personnes qui utilisent chacune de ces applications . Le service informatique peut utiliser ces informations pour identifier les applications préférées des utilisateurs et choisir ainsi celles à intégrer dans Azure AD pour l'authentification unique.

Lorsque vous intégrez une application dans Azure AD, vous pouvez mapper les identités d'application établies des utilisateurs à leurs identités Azure AD respectives.

<!---HONumber=AcomDC_0727_2016-->