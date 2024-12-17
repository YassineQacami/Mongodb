## Rapport sur CouchDB

### 1. Introduction
CouchDB est une base de données NoSQL open-source développée par Apache Software Foundation. Elle est conçue pour stocker, répliquer et synchroniser des données de manière fiable. CouchDB repose sur une architecture orientée documents (Document-Oriented Database) et utilise JSON pour stocker les données.

---

### 2. Base de Données Documentaire
Contrairement aux bases de données relationnelles traditionnelles (SQL), CouchDB adopte une structure flexible, basée sur des documents JSON. Chaque document est une structure autonome avec des paires **clef-valeur** et un identifiant unique (*id*).

- **Structure JSON** : Les données sont stockées dans un format JSON lisible par les humains.
- **Schema flexible** : Contrairement à une base relationnelle, chaque document peut avoir une structure différente.
- **Documents autonomes** : Les documents ne dépendent pas les uns des autres, ce qui facilite les opérations sur des données distribuées.

#### Exemple d'un document JSON dans CouchDB :
```json
{
  "_id": "001",
  "name": "Alice",
  "age": 25,
  "city": "Paris"
}
```
Chaque document a :
- `_id` : Identifiant unique obligatoire.
- Des paires clef-valeur pour stocker les informations.

---

### 3. Fonctionnement
CouchDB repose sur les principes suivants :
- **RESTful API** : CouchDB expose ses opérations via des requêtes HTTP (GET, POST, PUT, DELETE).
- **Réplication** : Supporte la réplication maître-maître permettant de synchroniser plusieurs instances de bases de données.
- **MapReduce** : CouchDB utilise MapReduce pour effectuer des requêtes complexes et créer des vues indexées.
- **ACID** : CouchDB garantit des propriétés ACID à l’échelle du document.
- **Conflits et résolutions** : CouchDB gère automatiquement les conflits de synchronisation grâce à sa réplication.

#### Vue d'ensemble de l'architecture :
CouchDB fonctionne comme un serveur HTTP où chaque base de données est un ensemble de documents stockés sous forme JSON. Les opérations se font à travers des requêtes HTTP REST, ce qui simplifie l'interaction avec CouchDB sans client spécifique.

---

### 4. MapReduce dans CouchDB
CouchDB utilise MapReduce pour créer des vues. **Map** transforme les données en clé-valeur, tandis que **Reduce** agrège les résultats.

#### 4.1. Fonction Map
La fonction Map est exécutée sur chaque document pour générer une clé et une valeur :
```javascript
function (doc) {
  emit(doc.city, 1);
}
```
- **emit** : Émet une paire clé-valeur.
- Exemple : Si le document a `city: "Paris"`, la clé sera "Paris" et la valeur sera `1`.

#### 4.2. Fonction Reduce
La fonction Reduce agrège les valeurs en fonction des clés :
```javascript
function (keys, values, rereduce) {
  return sum(values);
}
```
- **keys** : Les clés retournées par la fonction Map.
- **values** : Les valeurs associées aux clés.
- **sum** : Fonction qui additionne toutes les valeurs pour une clé donnée.

#### 4.3. Exemple
Si la fonction Map produit les clés `Paris`, `Lyon`, `Paris`, la fonction Reduce les agrège comme suit :
```json
{
  "Paris": 2,
  "Lyon": 1
}
```

---

### 5. API REST de CouchDB
L'accès aux données CouchDB se fait via des requêtes HTTP simples. Voici quelques exemples courants :

#### 5.1. Vérifier l'état du serveur
```bash
curl -X GET http://login:password@localhost:5984/
```
- Réponse :
```json
{
  "couchdb": "Welcome",
  "version": "3.2.0"
}
```

#### 5.2. Créer une base de données
```bash
curl -X PUT http://login:password@localhost:5984/BD_NAME
```
- **PUT** : Crée une nouvelle base de données nommée `BD_NAME`.

#### 5.3. Ajouter un document
```bash
curl -X PUT http://login:password@localhost:5984/BD_NAME/DOC -d '{"cle":"valeur"}'
```
- **DOC** : Nom du document.
- **-d** : Contenu JSON à insérer.

#### 5.4. Insérer un fichier JSON dans un document
```bash
curl -X PUT http://login:password@localhost:5984/BD_NAME/JSN -d @jsn1:10098.json -H "Content-Type: application/json"
```
- Permet d'envoyer un fichier JSON complet dans CouchDB.

#### 5.5. Insertion de documents en masse (bulk insert)
```bash
curl -X POST http://login:password@localhost:5984/BD_NAME/_bulk_docs -d @docs_collection.json -H "Content-Type: application/json"
```
- Envoie plusieurs documents en une seule requête.
- **_bulk_docs** : Point d'entrée pour l'insertion groupée.

#### 5.6. Récupérer un document par son identifiant
```bash
curl -X GET http://login:password@localhost:5984/BD_NAME/doc:id
```
- Renvoie les données du document avec l'identifiant `id`.

---

### 6. Utilisation de CouchDB avec Docker
CouchDB peut être facilement déployé avec Docker. Voici les étapes :

#### 6.1. Télécharger et déployer CouchDB avec Docker
```bash
docker run -d --name couchdb -p 5984:5984 -e COUCHDB_USER=admin -e COUCHDB_PASSWORD=password couchdb
```
Cette commande :
- **déploie** un conteneur CouchDB,
- **expose** le port 5984,
- configure un utilisateur administrateur et un mot de passe.

#### 6.2. Accéder à CouchDB
Une fois CouchDB déployé, vous pouvez accéder à l'interface web via l'URL suivante :
```http
http://localhost:5984/_utils/
```
Cette interface permet de visualiser et gérer les bases de données, documents et réplications.

---

### 7. Cas d'utilisation de CouchDB
CouchDB est utilisé dans plusieurs domaines :
- **Applications web hors-ligne** : Synchronisation des données entre clients et serveurs.
- **Applications mobiles** : Stockage local et réplication des données.
- **IoT** : Gestion des données distribuées provenant de plusieurs appareils.
- **Big Data** : Traitement et agrégation de grandes quantités de données via MapReduce.
- **Applications décentralisées** : Prise en charge d'environnements multi-utilisateurs avec gestion des conflits.

---

### 8. Conclusion
CouchDB est une solution flexible et robuste pour les bases de données documentaires, grâce à son API REST, son support de la réplication, et son modèle de stockage JSON. Son intégration avec Docker en facilite le déploiement et son utilisation dans des applications modernes. Il est particulièrement adapté aux besoins des applications distribuées et hors-ligne.

