# Z33 Paris Transfert

- Z33 est un projet de transfert de fichier volumineux avec NextCloud

- Nous avons remplacé le connecteur S3 par un montage NFS. Les données ont été transférées sur Scality pour le POC. Nous avons 1To pour les tests.

```
Bonjour,

Nous avons envisagé une authentification WSSO mais elle ne semble pas applicable facilement dans le contexte.
C'est dommage car le WSSO avec la fédération d'identité aurait été idéale dans le contexte. Nous aurions pu exploiter plusieurs fournisseurs d'identité avec SAML V2.

Plusieurs choses s'y oppose pour le moment :

D'une part, le WSSO n'est présent que sur la partie back-end avec NetScaler. De fait, nous n'aurions pas pu exposer le service en externe.
Par aillieurs, les groupes AD ne sont pas synchronisés avec l'annuaire GID.

La seule solution WSSO exposée est celle du compte Parisien avec la solution RedHat mais c'est une solution dédiée.

Peut-être que vous utilisez la solution Microsoft ADFS basée sur SAML ?

Il faudra peut-être explorer davantage : Une solution EFSS qui ne se base pas sur une fédération risquerait de poser problème à terme. Il y a des services de la ville qui ne sont pas sur notre réseau interne.

Si nous partageons des données avec des utilisateurs externes authentifiés, ou allons-nous mettre les informations (OU dédiée ?) et surtout comment faire accepter l'enrolement automatique d'un point de vue de la sécurité ?

En attendant une réflexion sur le sujet, nous avons fait une demande de compte de service AD : Ticket SATIS D00180323.

Bien cordialement,

```
--- 
```


Bonjour,

Nous aurions besoin d'un compte technique AD pour le POC de Z33 ParisBox.

Ce compte technique doit permettre d'authentifier les utilisateurs internes et d'accéder à l'ensemble des groupes AD (autres que techniques, i.e groupes de travail uniquement).

Vous pouvez nommer l'utilisateur en fonction de votre nomenclature.

Pour limiter la complexité (et comme il n'y a qu'un seul AD pour l'ensemble des environnements), nous souhaiterions n'utiliser qu'un seul compte pour tous les environnements.

Merci.

Bien cordialement,
```

- Note de reunion

```
Bonjour,

Ci-dessous les notes prises en réunion pour le compte-rendu et la restitution prévue aujourd'hui concernant la gestion des comptes et les difficultés que nous rencontrons.

La décision ne sera pas prise en séance et fera certainement l'objet de réunions de travail avec les parties concernées et en particulier MGIS.

Concernant la gestion des comptes : 

Il est difficile de mettre au même niveau les comptes internes et des comptes externes pour lesquels nous ne serions pas en mesure de vérifier les identités de façon directe ou déléguée.
Ceci est d'autant plus problématique si le mode d'authentification pour tous les comptes est "basic" (i.e. par simple login, mot de passe) qu'il s'agisse de l'annuaire GID ou AD.

Le stockage des comptes dans l'application n'est pas en accord avec la politique de sécurité de la ville y compris pour les comptes internes et donc a fortiori pour les comptes externes. Il faut nécessairement s'appuyer sur un IDP ou annuaire au travers d'une fédération ou non.

A terme, nous serons probablement obligé de nous appuyer sur une fédération d'identité pour vérifier les identités notamment pour les personnes de la ville qui ne sont pas sur notre annuaire interne. Il serait également envisageable de l'élargir à des fédérations publiques (France Connect) et potentiellement privées (Google, Twitter etc.).

A court terme et pour ne pas mettre tous les comptes au même niveau, l'idéal serait d'avoir une authentification sur annuaire GID pour les personnes de la ville et un formulaire pour les personnes qui ne font pas partie de la ville.

Il faut le faire le plus en amont possible (i.e. au niveau du reverse proxy) qui présenterait un formulaire si le compte n'existe pas. A court terme, il faudrait voir dans quelle mesure nous pourrions le faire au niveau de l'application.

Bien cordialement, 

```
--- 
```
Nous avons un point à voir ensemble concernant le transfert des très gros fichiers (plusieurs Go).
Dans ce cas, l'architecture client léger est assez mal adaptée. Les limitations sont liées au timeout des navigateurs mais aussi de façon intrinsèque à PHP.
De fait, nous pensons que l'usage du client lourd s'impose dans ce cas.
Comme vous avez des tests de votre côté nous aimerions partager vos résultats.
```

--- 
```
Bonjour,

Nous avons mis en oeuvre le périmètre réduit (P1) sur la plateforme de développement.

Une gestion optimale du cache en PHP a été mise en oeuvre en développement et en recette.

L'optimisation avec REDIS a été mise en oeuvre en développement. Elle sera particulièrement utile sur le périmètre élargi  (P2).

La balance de charge en recette est parfaite sur 2 serveurs web avec le mode STICKY sur l'adresse IP source. Nous envisageons 4 serveurs en production (5000 utilisateurs).

Nous partageons également les configurations entre les différents noeuds de recette pour éviter les divergences de configurations.

Le BECID n'ayant pas de stockage S3 pérenne pour le moment, nous avons migré les données sur un serveur NFS (nouvelle VM) en attendant. Il est clair que c'est un SPOF dans cette architecture redondée.

Nous avons mis en oeuvre un compte de messagerie SMTPS avec authentification sur tous les environnements.

La documentation a été mise à jour DAT et DI. L'ensemble a été déposé sur le Git STIPS à l'adresse suivante :

```

```
Nous allons mettre fin aux POC sur le « HCP » à la Mairie De Paris ce 
vendredi
21 février.
Le service S3 qui y est associé sera coupé et les données seront inaccessibles
```

```
- La simplification a été appliquée.
- Le WSSO est opérationnel avec un groupe pour le provionning. Les autorisations sont gérées par un script. La cinématique a été proposée et validée par MGIS.
- La sauvegarde Veeam permet la sauvegarde à chaud et consistante des données NAS et du cluster PostgreSQL.
- Le cluster REDIS est monté pour le stockage des LOCKS.
- Les templates des mails pour la personnalisation ont été modifiés (quelques ajustements sont en cours).

Il n'est pas possible, en revanche, de bloquer la suppression du dossier @Transfert. Le skeleton a été mis à jour et il faudra glisser un best practice dans l'arborescence.

La pré-production a été provisionnée et l'installation est en cours. Le WSSO sera ainsi accessible en externe (ce n'est pas le cas en recette). La capacité de traitement avec 4 serveurs frontaux permettra de monter jusqu'à 5000 utilisateurs pour les tests de performance.

```

Les autorisations sont gérées par le groupe AD et les mises à jour des autorisations SSO sont quotidiennes.




