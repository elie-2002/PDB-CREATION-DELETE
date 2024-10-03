# README: Pluggable Database Creation in Oracle

## Overview

This README provides a step-by-step guide on how to create, open, save state, and manage users within a Pluggable Database (PDB) in Oracle Database. It also includes instructions on how to create and delete a pluggable database.

## Prerequisites

- Oracle Database installed (with multitenant architecture support).
- Administrator access with SYSDBA privileges.
- Familiarity with Oracle's PDB (Pluggable Database) architecture.

### Step 1: Connect to the Oracle Database as SYSDBA

1. Open a terminal or command prompt.
2. Connect to the database using the following command:
   ```sql
   sqlplus sys/Elie@localhost:1521/xe AS SYSDBA
   ```

### Step 2: Check Existing Pluggable Databases

Before creating a new PDB, it's good practice to check the current pluggable databases.

Run the following command:
```sql
SHOW PDBS;
```

You should see output similar to the following:
```
    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 XEPDB1                         READ WRITE NO
```

### Step 3: Create a Pluggable Database

To create a new pluggable database from an existing PDB (e.g., `XEPDB1`), run the following command:
```sql
CREATE PLUGGABLE DATABASE plsql_class2024db
FILE_NAME_CONVERT=(
  'C:\app\Shelter\product\21c\oradata\XE\XEPDB1',
  'C:\app\Shelter\product\21c\oradata\XE\plsql_class2024db'
);
```

This command creates a new PDB (`plsql_class2024db`) by cloning the existing `XEPDB1` PDB and specifying the location for the new PDB's data files.

**Output**:
```
Pluggable database created.
```

### Step 4: Open the Pluggable Database

After creating the new PDB, you need to open it:
```sql
ALTER PLUGGABLE DATABASE plsql_class2024db OPEN;
```

**Output**:
```
Pluggable database altered.
```

### Step 5: Save the PDB State

To ensure that the PDB automatically opens when the container database (CDB) starts, save the PDB state:
```sql
ALTER PLUGGABLE DATABASE plsql_class2024db SAVE STATE;
```

**Output**:
```
Pluggable database altered.
```

### Step 6: Create a User in the Pluggable Database

To create a user inside the newly created PDB, follow these steps:

1. Switch to the `plsql_class2024db` PDB (if not already connected to it):
   ```sql
   ALTER SESSION SET CONTAINER = plsql_class2024db;
   ```

2. Create a new user:
   ```sql
   CREATE USER ns_plsqlauca IDENTIFIED BY admin;
   ```

**Output**:
```
User created.
```

### Step 7: Create and Delete a Pluggable Database

#### 1. Create a New Pluggable Database (`ns_to_delete_pdb`)

If you want to create a new PDB (`ns_to_delete_pdb`), run the following command:
```sql
CREATE PLUGGABLE DATABASE ns_to_delete_pdb
FILE_NAME_CONVERT=(
  'C:\app\Shelter\product\21c\oradata\XE\XEPDB1',
  'C:\app\Shelter\product\21c\oradata\XE\ns_to_delete_pdb'
);
```

**Output**:
```
Pluggable database created.
```

#### 2. Delete the Pluggable Database (`ns_to_delete_pdb`)

To delete a PDB (`ns_to_delete_pdb`), use the following command:
```sql
DROP PLUGGABLE DATABASE ns_to_delete_pdb INCLUDING DATAFILES;
```

**Output**:
```
Pluggable database dropped.
```

---

## Summary of Commands

### Check Pluggable Databases
```sql
SHOW PDBS;
```

### Create Pluggable Database
```sql
CREATE PLUGGABLE DATABASE plsql_class2024db
FILE_NAME_CONVERT=(
  'C:\app\Shelter\product\21c\oradata\XE\XEPDB1',
  'C:\app\Shelter\product\21c\oradata\XE\plsql_class2024db'
);
```

### Open Pluggable Database
```sql
ALTER PLUGGABLE DATABASE plsql_class2024db OPEN;
```

### Save Pluggable Database State
```sql
ALTER PLUGGABLE DATABASE plsql_class2024db SAVE STATE;
```

### Create a User in PDB
```sql
CREATE USER ns_plsqlauca IDENTIFIED BY admin;
```

### Create Another Pluggable Database
```sql
CREATE PLUGGABLE DATABASE ns_to_delete_pdb
FILE_NAME_CONVERT=(
  'C:\app\Shelter\product\21c\oradata\XE\XEPDB1',
  'C:\app\Shelter\product\21c\oradata\XE\ns_to_delete_pdb'
);
```

### Delete a Pluggable Database
```sql
DROP PLUGGABLE DATABASE ns_to_delete_pdb INCLUDING DATAFILES;
```

---

## Notes

- Always verify that the PDBs you are working with are either open or saved to automatically open on database startup.
- Be cautious while deleting PDBs, as using `INCLUDING DATAFILES` will permanently remove all associated data files.
