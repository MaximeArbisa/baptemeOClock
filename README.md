# Connection à une Base De Données (BDD) PostgreSQL depuis une application Node.js

## Plan de séance  
  
**Objectif principal**   

A la fin de ce cours, vous serez capables d'insérer, modifier, et lire les données d'une base de données PostgreSQL depuis une application Node.js.  

**Déroulé du cours (2h)**  
  
Le cours se déroule en 2 parties de 1h chacune.  
Les supports utilisés seront Google Meet, GitHub, VSCode et DBeaver.  

**1ère partie (1h)**  
A la fin de cette partie, l'apprenant:  
- saura ce qu'est une BDD relationnelle, et à quoi correspondent les tables, lignes et colonnes  
- saura comment lancer une BDD PostgreSQL depuis son ordinateur  
- saura communiquer avec sa BDD via `psql`  
- connaitra les bases du fonctionnement du langage de programmation SQL  
- saura créer un tableau, y insérer des données, les modifier et les requêter   

Cette 1ère partie se composera de 40 minutes d'introduction théorique et de présentation des commandes par le formateur, suivi d'un exercice pratique de 20 minutes en autonomie où l'apprenant devra:  
- créer un tableau à partir d'un modèle de données, et vérifier son existence  
- y insérer des lignes  
- les modifier  
- les requêter  
  
**2ème partie (2h)**  
A la fin de cette partie, l'apprenant:  
- saura installer le package node.js `pg` pour communiquer avec sa BDD PostgreSQL  
- connaitre la différence entre une opération synchrone et asynchrone, savoir ce qu'est une promesse et comment l'utiliser avec `async` et `await`  
- savoir comment catch et afficher les erreurs de son application lors de son exécution  
- saura se connecter à la BDD depuis son application Node.js avec une fonction asynchrone  
- saura insérer des données depuis son application Node.js avec une fonction asynchrone  
- saura requêter des données depuis son application Node.js avec une fonction asynchrone  

Cette 2ème partie se composera de 40 minutes de démonstration par le formateur, suivi d'un exercice pratique de 20 minutes en autonomie où l'apprenant devra installer le package `pg`, et écrire les fonctions asynchrones permettant la connexion à sa BDD en local, l'insertion et la lecture de données. La vérification se fera par l'apprenant directement, via `psql` ou via des `console.log`. L'apprenant pourra fork un repo GitHub avec un carcan classique d'application Node pour commencer plus vite.  

<br>
<br>
<br>  

## Cours  
  
## 1ère partie: Introduction aux Base De Données (BDD) relationnelles et au langage de programmation SQL  

**Objectif**  
Connaitre les bases du langage SQL et insérer, modifier et requêter des données dans votre base de données PostgreSQL.  
  
**Objectifs clés**  
A la fin de ce cours, vous saurez:  
- ce qu'est une BDD relationnelle, et à quoi correspondent les tables, lignes et colonnes  
- comment lancer une BDD PostgreSQL depuis votre ordinateur  
- communiquer avec votre BDD via `psql`  
- les bases du fonctionnement du langage de programmation SQL  
- créer un tableau, y insérer des données, les modifier et les requêter   
  
### Bases de données relationnelles  
  
Les bases de données relationnelles représentent les données sous forme de tableaux ou tables.  
  
Les tableaux ou tables représentent les modèles de votre base de données.  
Les colonnes les propriétés de chaque modèle, et les lignes chaque entité de ce modèle.    

**Plusieurs types de BDD relationnelles**  
- Oracle DB
- mySQL    
- MariaDB, utilisée par exemple par Wordpress  
- PostgreSQL  
- ...
  
> PostgreSQL est une BDD gratuite, open source, compatible avec SQL, avec de nombreuses options, et très performante. Elle est utilisée par des milliers d'entreprises.  
<br>
En savoir plus: https://www.ionos.com/digitalguide/server/know-how/postgresql/  

<br>
<br>  

**Exemple de table dans PostgreSQL**   
  
  
> La clé primaire ou primary key en anglais est indissiociable d'une entité dans un tableau. Elle doit être unique, définie dès la création de la ligne, et ne jamais changer. C'est la référence de notre entité.  

<br>
<br>  

### Démarrer sa BDD PostgreSQL et s'y connecter
**Démarrer le serveur PostgreSQL**  
En fonction de votre système d'exploitation, les commandes pour lancer le serveur changent. Depuis un terminal, lancer la commande:  
- Ubuntu: `sudo systemctl start postgresql.service`      
- MacOS et brew: `brew services start postgresql`  
  <br>  
Lors de son installation, PostgreSQL a crée une database par défaut: `postgres` qu'on va utiliser.  
  
<br>  

**Se connecter à PostgreSQL via le client psql**   
`psql` est un client frontend qui vous permet d'effectuer des commandes sur la BDD PostgreSQL depuis votre terminal.  
<br>
Pour se connecter à la BDD `postgres`, on lance `psql -d postgres`.   
<br>
Quelques commandes utiles:
- `\conninfo` affiche les informations de connexion (BDD, port, user, ...)  
- `\du`: les utilisateurs actifs sur la BDD  
- `CREATE ROLE postgres;` crée un nouvel utilisateur postgres  
- `ALTER ROLE postgres WITH PASSWORD 'new_password';` pour set un mdp à un user  
- `\quit` permet de clore la connexion du client `psql` avec la BDD    
  
### Manipuler les données dans la BDD  
**Langage de programmation SQL**  
SQL (Structured Query Langage) est un langage de programmation permettant de manipuler les données et les BDD relationnelles. Ce langage permet entre autre de créer des base de données, des tableaux, d'insérer, de modifier et se supprimer des lignes, de définir les colonnes, et bien plus encore.  
  
<br>  

**Créer son tableau (modèle de données)**   
On veut créer une table pour des livres, qui devront avoir ces propriétés:  
- Clé primaire  
- Nom du livre  
- Nom de l'auteur  
- Date de publication  
- Prix  
- Nombre d'exemplaires en stock  
- Date de créatiuon de la ligne  
- Date de modification de la ligne  
  
<br>

```
CREATE TABLE book(
    id bigserial PRIMARY KEY,
    name text NOT NULL,
    "authorName" text NOT NULL,
    "publishedAt" date NOT NULL,
    price float8 NOT NULL,
    stock int NOT NULL,
    "createdAt" timestamp NOT NULL DEFAULT now(),
    "updatedAt" timestamp NOT NULL DEFAULT now()
);
```  

Verifiez depuis psql que votre table a bien été créée: `\dt`  

<br>  

🙌 BRAVO ! Vous venez de créer votre 1er tableau.  
Pour en savoir plus sur les différents types de données, lisez la documentation PostgreSQL: https://www.postgresql.org/docs/current/datatype.html   

> La convention de nommage reste au choix, on peut choisir de nommer ses BDD, tables et colonnes en `camelCase` ou `snake_case`, mais il faut que la convention soit appliquée partout ! 
  
**Insérer des données dans la BDD**  
```
INSERT INTO book
VALUES (DEFAULT, 'Harry Potter et la chambre des secrets', 'J.K. Rowling', '2000-08-03', 29.99, 148);
```  
  
**Insérer plusieurs données dans la BDD**  
```  
INSERT INTO book
VALUES 
(DEFAULT, 'Harry Potter et la chambre des secrets', 'J.K. Rowling', '2000-08-03', 29.99, 148),
(DEFAULT, 'Farenheit 451', 'Ray Bradbury', '1953-10-03', 19.99, 12),
(DEFAULT, 'Merci la vie', 'Jean Mich Mich', '2022-02-02', 49.99, 234),
(DEFAULT, 'Le far west, quel pied', 'John Wright', '1987-12-03', 16, 108);
```

<br>

**Requêter des données depuis la BDD**  
```
SELECT * FROM book;
SELECT id, name, price FROM book;
SELECT id, name, price FROM book WHERE price > 20 ORDER BY price desc;
```
<br>  

**Modifier des données dans la BDD**  
```
UPDATE book
SET price = 65
WHERE stock < 5;
```  

Une fois la modification effectuée, vous pouvez les requêter avec un SELECT pour observer si la modification a bien eu lieu.  
  
### Autres clients pour PostgreSQL  
- DBeaver (https://dbeaver.io/download/)  
- PgAdmin (https://www.pgadmin.org/download/)

<br>
<br>  

## A vous de jouer !  
**Enoncé**
- Créez une table pour les utilisateurs d'une application qui contient le prénom, nom, date de naissance, email, numéro de téléphone et l'âge. Definissez les bons types pour chaque colonne et créez la table via `psql`.  
- Créez 4 utilisateurs avec les valeurs suivantes:
  - John, Waserback, 22/11/1992, john.waserback@yahoo.fr, 0662371237, 30 ans  
  - Mathieu, Bouvier, 04/05/1998, hellomatt@gmail.com, 0672539382, 24 ans  
  - Michael, Gondry, 12/03/1987, michael55@hotmail.fr, 0602938463, 35 ans  
  - Ron, Bett, 09/12/1968, ronnybett@gmail.com, 0602938325, 54 ans  
- Affichez le nom, l'email et le numéro de téléphone de utilisateurs de plus de 32 ans  
- Une erreur a été commise en rentrant les données de Michael, il est né le 27/03/1987. Modifiez la donnée dans la BDD  
<br>
<br>
<br>
<br>  

**Solution**  

- Créez une table pour les utilisateurs d'une application qui contient le prénom, nom, date de naissance, email, numéro de téléphone, l'age, la date de création de la ligne et de sa dernière modification. L'email, la date de naissance et l'âge peuvent être nuls mais les autres champs sont obligatoires. Definissez les bons types pour chaque colonne et créez la table via `psql`.  
  
```
CREATE TABLE customer(
    id bigserial PRIMARY KEY,
    "firstName" text NOT NULL,
    "lastName" text NOT NULL,
    "birthDate" date,
    email text,
    "phoneNumber" text NOT NULL,
    age int,
    "createdAt" timestamp NOT NULL DEFAULT now(),
    "updatedAt" timestamp NOT NULL DEFAULT now()
);
```
  
- Créez 4 utilisateurs avec les valeurs suivantes:

```
INSERT INTO customer
VALUES
  (DEFAULT, 'John', 'Waserback', '1992-11-22', 'john.waserback@yahoo.fr', '0662371237', 30), 
  (DEFAULT, 'Mathieu', 'Bouvier', '1998-05-04', 'hellomatt@gmail.com', '0672539382', 24), 
  (DEFAULT, 'Michael', 'Gondry', '1987-03-12', 'michael55@hotmail.fr', '0602938463', 35),  
  (DEFAULT, 'Ron', 'Bett', '1968-12-09', 'ronnybett@gmail.com', '0602938325', 54);
```  

- Affichez le nom, l'email et le numéro de téléphone des utilisateurs de plus de 32 ans   

```
SELECT "lastName", email, "phoneNumber"
FROM customer
WHERE age >= 32;
```  

- Une erreur a été commise en rentrant les données de Michael, il est né le 27/03/1987. Modifiez la donnée dans la BDD.  
  
```
UPDATE customer
SET "birthDate" = '1987-03-27'
WHERE id = 3;
```


