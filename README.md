# Mongodb6

![image](https://github.com/user-attachments/assets/fd4682b2-f49f-4700-8a98-e3759f8434d0)

![image](https://github.com/user-attachments/assets/34cf83c6-399d-4216-9d03-c1a676867499)

![image](https://github.com/user-attachments/assets/1a82aec3-c3ae-47f8-9c36-9310c4a39f6f)

![image](https://github.com/user-attachments/assets/0012b6b2-5485-4c4b-9e8b-603c51fb5d01)

![image](https://github.com/user-attachments/assets/427cca5b-a0e9-4315-97a7-3a8157e2b6ff)

Voici une explication détaillée et étape par étape pour utiliser MongoDB avec Docker et réaliser le TP.

---

## **Étape 1 : Installer Docker**

1. **Vérifiez si Docker est installé :**
   ```bash
   docker --version
   ```
   Si Docker n’est pas installé, téléchargez-le depuis [Docker](https://www.docker.com/) et suivez les instructions pour votre système d’exploitation.

2. **Installer Docker Compose (facultatif) :**
   Si vous prévoyez d’utiliser Docker Compose, installez-le également.

---

## **Étape 2 : Démarrer MongoDB avec Docker**

1. **Téléchargez et exécutez MongoDB dans un conteneur :**
   Exécutez la commande suivante pour démarrer MongoDB :
   ```bash
   docker run --name mongodb -d -p 27017:27017 -v $(pwd)/data:/data/db mongo:latest
   ```

   - **`--name mongodb`** : Donne un nom au conteneur.
   - **`-d`** : Lance le conteneur en arrière-plan.
   - **`-p 27017:27017`** : Mappe le port 27017 du conteneur au port 27017 de la machine hôte.
   - **`-v $(pwd)/data:/data/db`** : Monte un répertoire local (`data`) pour persister les données.

2. **Vérifiez que MongoDB est en cours d’exécution :**
   ```bash
   docker ps
   ```
   Vous devriez voir une ligne avec le conteneur MongoDB.

---

## **Étape 3 : Préparer les données**

1. **Créez ou téléchargez le fichier `films.json`.**
   Placez les données dans un fichier appelé `films.json` dans votre répertoire de travail. Par exemple :
   ```json
   [
     {
       "title": "Film Action 1",
       "year": 1963,
       "genre": "Action",
       "country": "France",
       "grades": [12, 15],
       "artists": ["artist:4", "artist:18"]
     },
     {
       "title": "Film Action 2",
       "year": 1997,
       "genre": "Action",
       "country": "USA",
       "grades": [9, 8],
       "artists": ["Leonardo DiCaprio"]
     }
   ]
   ```

---

## **Étape 4 : Importer les données dans MongoDB**

1. **Copiez le fichier dans le conteneur Docker :**
   ```bash
   docker cp films.json mongodb:/films.json
   ```

2. **Connectez-vous au conteneur :**
   ```bash
   docker exec -it mongodb bash
   ```

3. **Importez les données avec `mongoimport` :**
   ```bash
   mongoimport --db lesfilms --collection films --file /films.json --jsonArray
   ```
   - **`--db lesfilms`** : Crée ou sélectionne la base de données `lesfilms`.
   - **`--collection films`** : Crée ou sélectionne la collection `films`.
   - **`--file /films.json`** : Chemin vers le fichier JSON.
   - **`--jsonArray`** : Indique que les données sont un tableau JSON.

---

## **Étape 5 : Accéder à MongoDB**

1. **Connectez-vous au shell MongoDB :**
   ```bash
   docker exec -it mongodb mongosh
   ```

2. **Sélectionnez la base de données `lesfilms` :**
   ```javascript
   use lesfilms
   ```

---

## **Étape 6 : Réaliser les requêtes**

Voici les requêtes MongoDB demandées dans le TP :

### 1. **Vérifiez que les données sont importées :**
   ```javascript
   db.films.count()
   ```
   Cela retourne le nombre total de documents dans la collection `films`.

---

### 2. **Examiner un document :**
   ```javascript
   db.films.findOne()
   ```
   Cela affiche un document aléatoire de la collection pour comprendre sa structure.

---

### 3. **Afficher les films d’action :**
   ```javascript
   db.films.find({ genre: "Action" })
   ```

---

### 4. **Compter les films d’action :**
   ```javascript
   db.films.count({ genre: "Action" })
   ```

---

### 5. **Afficher les films d’action produits en France :**
   ```javascript
   db.films.find({ genre: "Action", country: "France" })
   ```

---

### 6. **Afficher les films d’action produits en France en 1963 :**
   ```javascript
   db.films.find({ genre: "Action", country: "France", year: 1963 })
   ```

---

### 7. **Afficher les films d’action en France sans les notes (grades) :**
   ```javascript
   db.films.find({ genre: "Action", country: "France" }, { grades: 0 })
   ```

---

### 8. **Afficher les films sans identifiants (_id) :**
   ```javascript
   db.films.find({ genre: "Action", country: "France" }, { _id: 0, grades: 0 })
   ```

---

### 9. **Afficher les titres et grades des films d’action en France :**
   ```javascript
   db.films.find({ genre: "Action", country: "France" }, { _id: 0, title: 1, grades: 1 })
   ```

---

### 10. **Afficher les films avec une note > 10 :**
   ```javascript
   db.films.find({ genre: "Action", country: "France", grades: { $gt: 10 } }, { _id: 0, title: 1, grades: 1 })
   ```

---

### 11. **Afficher les films ayant toutes les notes > 10 :**
   ```javascript
   db.films.find({ genre: "Action", grades: { $not: { $elemMatch: { $lte: 10 } } } }, { _id: 0, title: 1, grades: 1 })
   ```

---

### 12. **Lister les genres :**
   ```javascript
   db.films.distinct("genre")
   ```

---

### 13. **Lister les grades :**
   ```javascript
   db.films.distinct("grades")
   ```

---

### 14. **Films avec des artistes spécifiques :**
   ```javascript
   db.films.find({ artists: { $in: ["artist:4", "artist:18", "artist:11"] } })
   ```

---

### 15. **Films sans résumé :**
   ```javascript
   db.films.find({ summary: { $exists: false } })
   ```

---

### 16. **Films avec Leonardo DiCaprio en 1997 :**
   ```javascript
   db.films.find({ artists: "Leonardo DiCaprio", year: 1997 })
   ```

---

### 17. **Films avec Leonardo DiCaprio ou en 1997 :**
   ```javascript
   db.films.find({ $or: [{ artists: "Leonardo DiCaprio" }, { year: 1997 }] })
   ```

---

## **Étape 7 : Arrêter MongoDB après le TP**

Pour arrêter MongoDB :
```bash
docker stop mongodb
```

Pour supprimer le conteneur :
```bash
docker rm mongodb
```
