# Mongodb
---

## **Étape 1 : Installer Docker**

1. **Vérifiez si Docker est installé :**
   ```bash
   docker --version
   ```
   Si Docker n’est pas installé, téléchargez-le depuis [Docker](https://www.docker.com/) et suivez les instructions pour votre système d’exploitation.

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

1. **Téléchargez le fichier `films.json`.**
   Placez les données dans un fichier appelé `films.json` dans votre répertoire de travail.

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
   db.films.find({ genre: "Action", country: "FR" })
   ```

---

### 6. **Afficher les films d’action produits en France en 1963 :**
   ```javascript
   db.films.find({ genre: "Action", country: "FR", year: 1963 })
   ```

---

### 7. **Afficher les films d’action en France sans les notes (grades) :**
   ```javascript
   db.films.find({ genre: "Action", country: "FR" }, { grades: 0 })
   ```

---

### 8. **Afficher les films sans identifiants (_id) :**
   ```javascript
   db.films.find({ genre: "Action", country: "FR" }, { _id: 0, grades: 0 })
   ```

---

### 9. **Afficher les titres et grades des films d’action en France :**
   ```javascript
   db.films.find({ genre: "Action", country: "FR" }, { _id: 0, title: 1, grades: 1 })
   ```

---

### 10. **Afficher les films avec une note > 10 :**
   ```javascript
   db.films.find({ genre: "Action", country: "FR", "grades.note": { $gt: 10 } }, { _id: 0, title: 1, grades: 1 })
   ```

---

### 11. **Afficher les films ayant toutes les notes > 10 :**
   ```javascript
   db.films.find({ genre: "Action", country: "FR", grades: { $not: { $elemMatch: { note: { $lte: 10 } } } } }, { _id: 0, title: 1, grades: 1 })
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
   db.films.find({ "actors.first_name": "Leonardo", "actors.last_name": "DiCaprio", year: 1997 })
   ```

---

### 17. **Films avec Leonardo DiCaprio ou en 1997 :**
   ```javascript
   db.films.find({ $or: [{ "actors.first_name": "Leonardo", "actors.last_name": "DiCaprio"}, { year: 1997 }] })
   ```

---

## **Étape 7 : Arrêter MongoDB**

Pour arrêter MongoDB :
```bash
docker stop mongodb
```

Pour supprimer le conteneur :
```bash
docker rm mongodb
```
