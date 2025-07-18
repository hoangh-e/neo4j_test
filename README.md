##  IMPORT lên NEO4J ONLINE

### Bước 1: Upload `nodes.csv` & `relationships.csv` lên GitHub

  * `https://raw.githubusercontent.com/hoangh-e/neo4j_test/main/NODES.CSV`
  * `https://raw.githubusercontent.com/hoangh-e/neo4j_test/main/RELATIONSHIPS.CSV`

---

### Bước 2: Tạo node trong Neo4j từ CSV online

```cypher
LOAD CSV WITH HEADERS FROM 'ttps://raw.githubusercontent.com/hoangh-e/neo4j_test/main/NODES.CSV' AS row
CREATE (n:Node {
  nodeId: row.nodeId,
  name: row.name,
  type: row.type,
  size: toInteger(row.size),
  registrationNumber: row.registrationNumber,
  price: row.price,
  expiryDate: row.expiryDate,
  shelfLife: row.shelfLife,
  icdCode: row.icdCode,
  severity: row.severity,
  formula: row.formula,
  molarMass: row.molarMass,
  function: row.function,
  origin: row.origin,
  established: row.established,
  employees: row.employees,
  headquarters: row.headquarters,
  capacity: row.capacity,
  certification: row.certification,
  code: row.code,
  gdp: row.gdp,
  description: row.description,
  authority: row.authority,
  validPeriod: row.validPeriod
});
```

---

### Bước 3: Thêm labels theo `type` (nếu có APOC)

Neo4j Aura và Sandbox thường. Nếu có, chạy:

```cypher
MATCH (n:Node)
WITH n, toUpper(n.type) AS label
CALL apoc.create.addLabels(n, [label]) YIELD node
RETURN count(node);
```

> Nếu không có APOC, cần dùng `FOREACH` để thêm label thủ công.

---

### Bước 4: Import relationships từ URL

```cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/hoangh-e/neo4j_test/main/RELATIONSHIPS.CSV' AS row
MATCH (source:Node {nodeId: row.sourceId})
MATCH (target:Node {nodeId: row.targetId})
CALL apoc.create.relationship(source, row.relationshipType, {weight: toFloat(row.weight)}, target) YIELD rel
RETURN count(rel);
```

---

## Bước 5: Kiểm tra dữ liệu


```cypher
MATCH (d:DRUG)-[:ĐIỀU_TRỊ]->(x:DISEASE)
RETURN d.name, x.name;
```

