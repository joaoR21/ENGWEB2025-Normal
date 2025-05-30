## PR.md

### Persistência de Dados

-   Transformei o dataset num formato de array de objetos, cada um representando uma edição. O `_id` de cada edição (proveninente do campo original "id") é o seu identificador.
-   **Base de Dados**: em mongoDB, com o nome `eurovisao`
-   **Coleção**: `edicoes`[cite: 8].
-   **Schema (`ex1/models/editions-model.js`)**:
    -   `_id`: String, required (ID da edição)
    -   `anoEdição`: String, required
    -   `musicas`: Array de objetos (`id`, `link`, `título`, `país`, `compositor`, `intérprete`, `letra`)
    -   `organizacao`: String, required
    -   `vencedor`: String

---
### Setup da base de dados

-   **Importação**: utilizar `mongoimport` com o dataset JSON em formato array.
    ```bash
    mongoimport --db eurovisao --collection edicoes --file <DATASET>.json --jsonArray
    ```

---
### Queries (exercício 1.2 - ficheiro `ex1/queries.txt`)

1.  **Total de registos (edições)**:
    ```javascript
    db.edicoes.countDocuments()
    ```
2.  **Edições com "Ireland" como vencedor**:
    ```javascript
    db.edicoes.countDocuments({ vencedor: "Ireland" })
    ```
3.  **Lista de intérpretes (ordenada, sem repetições)**:
    ```javascript
    db.edicoes.aggregate([
      { $unwind: "$musicas" },
      { $group: { _id: "$musicas.intérprete" } },
      { $sort: { _id: 1 } }
    ])
    ```
4.  **Distribuição de músicas por edição**:
    ```javascript
    db.edicoes.aggregate([
      { $project: { _id: 1, anoEdição: 1, numMusicas: { $size: "$musicas" } } },
      { $sort: { anoEdição: 1 } }
    ])
    ```
5.  **Distribuição de vitórias por país**:
    ```javascript
    db.edicoes.aggregate([
      { $match: { vencedor: { $ne: null, $ne: "" } } },
      { $group: { _id: "$vencedor", numVitorias: { $sum: 1 } } },
      { $sort: { numVitorias: -1, _id: 1 } }
    ])
    ```

---
### Instruções de Execução

**Pré-requisitos**: Node.js, npm, mongoDB (com a base de dados inicializada).

**Para cada aplicação (`ex1`, `ex2`):**
1.  `cd ex1` (ou `cd ex2`)
2.  `npm i` (instalar dependências)
3.  `npm start` (iniciar aplicação)

**Portas**:
-   **ex1 (API de dados/backend)**: `http://localhost:25000`
-   **ex2 (interface/frotend)**: `http://localhost:25001`
