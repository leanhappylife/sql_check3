# Relationship List with English Definitions and Sample SQL

This document lists each `RelationshipType` with:

- a short English definition
- a small SQL example

---

## CREATE_TABLE
**Definition:** Defines a table object, or defines a session table under the current rule set.

**Sample SQL**
```sql
CREATE TABLE INTERFACE.API_MTM_REVAL (
    CUST_NUM VARCHAR(20)
)
@
```

```sql
DECLARE GLOBAL TEMPORARY TABLE SESSION.T_MTM_STAGE
(
    CUST_NUM VARCHAR(20)
)
WITH REPLACE
ON COMMIT PRESERVE ROWS;
```

---

## CREATE_VIEW
**Definition:** Defines a view object.

**Sample SQL**
```sql
CREATE VIEW INTERFACE.V_API_MTM_REVAL AS
SELECT F.CUSTOMER_NUMBER AS CUST_NUM
FROM FOS.API_MTM_REVAL F
@
```

---

## CREATE_VIEW_MAP
**Definition:** A view target column is directly mapped from a source table or source view column.

**Sample SQL**
```sql
CREATE VIEW INTERFACE.V_API_MTM_REVAL AS
SELECT
    F.CUSTOMER_NUMBER AS CUST_NUM
FROM FOS.API_MTM_REVAL F
@
```

Example mapping:
```text
FOS.API_MTM_REVAL.CUSTOMER_NUMBER -> INTERFACE.V_API_MTM_REVAL.CUST_NUM
```

---

## SELECT_TABLE
**Definition:** The current object directly reads from a table.

**Sample SQL**
```sql
SELECT CODE_VALUE
FROM CCL.CODE_MAP
WHERE CODE_CATEGORY = P_CODE_CATEGORY;
```

---

## SELECT_VIEW
**Definition:** The current object directly reads from a view.

**Sample SQL**
```sql
SELECT V.CUST_NUM
FROM INTERFACE.V_API_MTM_REVAL V
WHERE V.CUST_NUM = P_CUST_NUM;
```

---

## SELECT_FIELD
**Definition:** The current object directly uses a column from a source object.

**Sample SQL**
```sql
SELECT V.CUST_NUM
FROM INTERFACE.V_API_MTM_REVAL V;
```

---

## SELECT_EXPR
**Definition:** A result expression directly depends on a source column, but not as a simple direct column pass-through.

**Sample SQL**
```sql
SELECT TRIM(V.CUST_NUM) || '-'
FROM INTERFACE.V_API_MTM_REVAL V;
```

---

## INSERT_TABLE
**Definition:** The current object inserts rows into a table.

**Sample SQL**
```sql
INSERT INTO INTERFACE.API_MTM_REVAL
(
    CUST_NUM
)
SELECT S.CUST_NUM
FROM SESSION.T_MTM_STAGE S;
```

---

## INSERT_VIEW
**Definition:** The current object inserts rows into a view.

**Sample SQL**
```sql
INSERT INTO INTERFACE.V_API_MTM_REVAL
(
    CUST_NUM
)
SELECT CUSTOMER_NUMBER
FROM FOS.API_MTM_REVAL;
```

---

## INSERT_TARGET_COL
**Definition:** A target column of an INSERT statement is written. The target column may be explicit or inferred from metadata.

**Sample SQL**
```sql
INSERT INTO INTERFACE.API_MTM_REVAL
(
    CUST_NUM,
    DEAL_NUM
)
SELECT
    S.CUST_NUM,
    S.DEAL_NUM
FROM SESSION.T_MTM_STAGE S;
```

Metadata-inferred example:
```sql
INSERT INTO INTERFACE.API_MTM_REVAL_STAR
SELECT *
FROM SESSION.T_MTM_STAGE_ALL;
```

---

## INSERT_SELECT_MAP
**Definition:** In an INSERT ... SELECT flow, a source column directly maps to a target column.

**Sample SQL**
```sql
INSERT INTO TABLE_A
(
    COL_A
)
SELECT
    S.COL_A
FROM SESSION.T_GRAPH_STAGE S;
```

Example mapping:
```text
SESSION.T_GRAPH_STAGE.COL_A -> TABLE_A.COL_A
```

---

## UPDATE_TABLE
**Definition:** The current object updates a table.

**Sample SQL**
```sql
UPDATE INTERFACE.API_MTM_REVAL T
SET STATUS_CD = 'ACTIVE'
WHERE T.CUST_NUM = P_CUST_NUM;
```

---

## UPDATE_VIEW
**Definition:** The current object updates a view.

**Sample SQL**
```sql
UPDATE INTERFACE.V_API_MTM_REVAL V
SET DEAL_TYPE = 'UPD'
WHERE V.CUST_NUM = P_CUST_NUM;
```

---

## UPDATE_TARGET_COL
**Definition:** A target column in an UPDATE statement is assigned a value.

**Sample SQL**
```sql
UPDATE INTERFACE.API_MTM_REVAL T
SET STATUS_CD = 'ACTIVE',
    CUR_MTM_AMT = (
        SELECT S.CUR_MTM_AMT
        FROM SESSION.T_MTM_STAGE S
        WHERE S.DEAL_NUM = T.DEAL_NUM
    )
WHERE T.CUST_NUM = P_CUST_NUM;
```

---

## UPDATE_SET
**Definition:** An UPDATE SET expression directly uses a source column or source-side expression.

**Sample SQL**
```sql
UPDATE INTERFACE.API_MTM_REVAL T
SET CUR_MTM_AMT = (
    SELECT S.CUR_MTM_AMT
    FROM SESSION.T_MTM_STAGE S
    WHERE S.DEAL_NUM = T.DEAL_NUM
)
WHERE T.CUST_NUM = P_CUST_NUM;
```

---

## UPDATE_SET_MAP
**Definition:** A source column directly maps to an updated target column.

**Sample SQL**
```sql
UPDATE INTERFACE.API_MTM_REVAL T
SET CUR_MTM_AMT = (
    SELECT S.CUR_MTM_AMT
    FROM SESSION.T_MTM_STAGE S
    WHERE S.DEAL_NUM = T.DEAL_NUM
)
WHERE T.CUST_NUM = P_CUST_NUM;
```

Example mapping:
```text
SESSION.T_MTM_STAGE.CUR_MTM_AMT -> INTERFACE.API_MTM_REVAL.CUR_MTM_AMT
```

---

## DELETE_TABLE
**Definition:** The current object deletes rows from a table.

**Sample SQL**
```sql
DELETE FROM INTERFACE.API_MTM_REVAL
WHERE STATUS_CD = 'OLD';
```

---

## DELETE_VIEW
**Definition:** The current object deletes rows from a view.

**Sample SQL**
```sql
DELETE FROM INTERFACE.V_API_MTM_REVAL
WHERE CUST_NUM = 'TO_DELETE';
```

---

## MERGE_TABLE
**Definition:** The current object performs a MERGE against a table.

**Sample SQL**
```sql
MERGE INTO INTERFACE.API_MTM_REVAL T
USING SESSION.T_MTM_STAGE S
ON T.DEAL_NUM = S.DEAL_NUM
WHEN MATCHED THEN
    UPDATE SET T.CUR_MTM_AMT = S.CUR_MTM_AMT
WHEN NOT MATCHED THEN
    INSERT (CUST_NUM, DEAL_NUM, CUR_MTM_AMT)
    VALUES (S.CUST_NUM, S.DEAL_NUM, S.CUR_MTM_AMT);
```

---

## MERGE_VIEW
**Definition:** The current object performs a MERGE against a view.

**Sample SQL**
```sql
MERGE INTO INTERFACE.V_API_MTM_REVAL V
USING SESSION.T_MTM_STAGE S
ON V.DEAL_NUM = S.DEAL_NUM
WHEN MATCHED THEN
    UPDATE SET V.CUR_MTM_AMT = S.CUR_MTM_AMT;
```

---

## MERGE_TARGET_COL
**Definition:** A target column is updated or inserted inside a MERGE statement.

**Sample SQL**
```sql
MERGE INTO INTERFACE.API_MTM_REVAL T
USING SESSION.T_MTM_STAGE S
ON T.DEAL_NUM = S.DEAL_NUM
WHEN MATCHED THEN
    UPDATE SET T.CUR_MTM_AMT = S.CUR_MTM_AMT;
```

---

## MERGE_MATCH
**Definition:** A MERGE match condition directly depends on a field.

**Sample SQL**
```sql
MERGE INTO INTERFACE.API_MTM_REVAL T
USING SESSION.T_MTM_STAGE S
ON T.DEAL_NUM = S.DEAL_NUM
WHEN MATCHED THEN
    UPDATE SET T.CUR_MTM_AMT = S.CUR_MTM_AMT;
```

---

## MERGE_SET_MAP
**Definition:** In a MERGE matched-update branch, a source field directly maps to a target field.

**Sample SQL**
```sql
MERGE INTO INTERFACE.API_MTM_REVAL T
USING SESSION.T_MTM_STAGE S
ON T.DEAL_NUM = S.DEAL_NUM
WHEN MATCHED THEN
    UPDATE SET T.CUR_MTM_AMT = S.CUR_MTM_AMT;
```

Example mapping:
```text
SESSION.T_MTM_STAGE.CUR_MTM_AMT -> INTERFACE.API_MTM_REVAL.CUR_MTM_AMT
```

---

## MERGE_INSERT_MAP
**Definition:** In a MERGE not-matched-insert branch, a source field directly maps to a target field.

**Sample SQL**
```sql
MERGE INTO INTERFACE.API_MTM_REVAL T
USING SESSION.T_MTM_STAGE S
ON T.DEAL_NUM = S.DEAL_NUM
WHEN NOT MATCHED THEN
    INSERT (CUST_NUM, DEAL_NUM, CUR_MTM_AMT)
    VALUES (S.CUST_NUM, S.DEAL_NUM, S.CUR_MTM_AMT);
```

Example mapping:
```text
SESSION.T_MTM_STAGE.CUST_NUM -> INTERFACE.API_MTM_REVAL.CUST_NUM
```

---

## JOIN_ON
**Definition:** A JOIN condition directly depends on a field.

**Sample SQL**
```sql
SELECT I.DEAL_TYPE
FROM INTERFACE.API_MTM_REVAL I
JOIN FOS.API_MTM_REVAL F
  ON I.DEAL_NUM = F.DEAL_NUMBER;
```

---

## WHERE
**Definition:** A WHERE condition directly depends on a field.

**Sample SQL**
```sql
SELECT CODE_VALUE
FROM CCL.CODE_MAP
WHERE CODE_CATEGORY = P_CODE_CATEGORY;
```

---

## GROUP_BY
**Definition:** A GROUP BY clause directly uses a field.

**Sample SQL**
```sql
SELECT I.DEAL_TYPE, COUNT(*) AS CNT
FROM INTERFACE.API_MTM_REVAL I
GROUP BY I.DEAL_TYPE;
```

---

## HAVING
**Definition:** A HAVING clause directly depends on a field or aggregate filter object.

**Sample SQL**
```sql
SELECT I.DEAL_TYPE, COUNT(*) AS CNT
FROM INTERFACE.API_MTM_REVAL I
GROUP BY I.DEAL_TYPE
HAVING COUNT(*) > 1;
```

---

## ORDER_BY
**Definition:** An ORDER BY clause directly uses a field.

**Sample SQL**
```sql
SELECT I.DEAL_TYPE
FROM INTERFACE.API_MTM_REVAL I
ORDER BY I.DEAL_TYPE;
```

---

## CALL_PROCEDURE
**Definition:** The current object directly calls a procedure.

**Sample SQL**
```sql
CALL RPT.PR_AUDIT_MTM_REVAL(P_CUST_NUM, 'D001', 'DONE');
```

---

## CALL_FUNCTION
**Definition:** The current object directly calls a function.

**Sample SQL**
```sql
SET V_STATUS = INTERFACE.FN_REL_DEMO(P_CUST_NUM);
```

Or:
```sql
SELECT INTERFACE.FN_GET_CODE_MAP_VALUE('MTM','STATUS','DEFAULT','A','B')
FROM SYSIBM.SYSDUMMY1;
```

---

## RETURN_VALUE
**Definition:** The return value of a function directly depends on a source field or a called function result.

**Sample SQL**
```sql
RETURN V_OUT;
```

and earlier:
```sql
SET V_OUT = (
    SELECT TRIM(V.CUST_NUM) || '-' ||
           INTERFACE.FN_GET_CODE_MAP_VALUE('MTM','STATUS','DEFAULT','A','B')
    FROM INTERFACE.V_API_MTM_REVAL V
    WHERE V.CUST_NUM = P_CUST_NUM
    FETCH FIRST 1 ROW ONLY
);
```

---

## TRUNCATE_TABLE
**Definition:** The current object truncates a table.

**Sample SQL**
```sql
TRUNCATE TABLE INTERFACE.MTM_REVAL_HIST IMMEDIATE;
```

---

## UNION_INPUT
**Definition:** A table or view is one input source of a UNION / UNION ALL.

**Sample SQL**
```sql
WITH BASE_DATA AS
(
    SELECT V.CUST_NUM
    FROM INTERFACE.V_API_MTM_REVAL V

    UNION ALL

    SELECT I.CUST_NUM
    FROM INTERFACE.API_MTM_REVAL I
)
SELECT B.CUST_NUM
FROM BASE_DATA B;
```

---

## CTE_DEFINE
**Definition:** Defines a CTE object.

**Sample SQL**
```sql
WITH BASE_DATA AS
(
    SELECT V.CUST_NUM
    FROM INTERFACE.V_API_MTM_REVAL V
)
SELECT B.CUST_NUM
FROM BASE_DATA B;
```

---

## CTE_READ
**Definition:** The current statement reads from a CTE.

**Sample SQL**
```sql
WITH BASE_DATA AS
(
    SELECT V.CUST_NUM
    FROM INTERFACE.V_API_MTM_REVAL V
)
SELECT B.CUST_NUM
FROM BASE_DATA B;
```

---

## UNKNOWN
**Definition:** A relationship exists, but the analyzer cannot safely classify it precisely. This is typically used for dynamic SQL or low-confidence cases.

**Sample SQL**
```sql
EXECUTE IMMEDIATE 'DELETE FROM INTERFACE.API_MTM_REVAL WHERE STATUS_CD = ''X''';
```

---

## 12. Recommended implementation order

1. Build table metadata
2. Parse view files and emit `CREATE_VIEW` / `CREATE_VIEW_MAP`
3. Parse function and procedure files and emit direct relationships
4. Expand `SELECT *`
5. Validate against `expected/relationship_detail.tsv`
6. Implement `lineage_path.tsv` later based on direct relationships
