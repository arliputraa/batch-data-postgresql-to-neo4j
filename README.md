# Batching data Postgresql to Neo4j

Login Postgresql
    
    sudo -u postgres -i

Akses User Postgres

    psql

Create table in Postgres (Optional)

    CREATE TABLE employees (
        emp_no SERIAL PRIMARY KEY,
        first_name VARCHAR(50),
        last_name VARCHAR(50),
        birth_date DATE,
        hire_date DATE,
        gender CHAR(1)
    );
    INSERT INTO employees (first_name, last_name, birth_date, hire_date, gender)
    VALUES
        ('John', 'Doe', '1990-01-15', '2021-05-10', 'M'),
        ('Jane', 'Smith', '1985-09-22', '2018-03-18', 'F'),
        ('Michael', 'Johnson', '1978-03-05', '2005-11-30', 'M'),
        ('Emily', 'Williams', '1993-07-12', '2020-08-22', 'F'),
        ('David', 'Brown', '1982-11-02', '2014-06-14', 'M'),
        ('Sarah', 'Miller', '1989-04-25', '2011-02-08', 'F'),
        ('Daniel', 'Jones', '1975-12-19', '1998-09-17', 'M'),
        ('Jessica', 'Davis', '1987-08-10', '2016-07-03', 'F'),
        ('Matthew', 'Martinez', '1992-06-30', '2019-04-21', 'M'),
        ('Linda', 'Garcia', '1980-02-18', '2007-10-09', 'F');

List All Tables

    \dt

Download driver In $Neo4jHome/plugins

    wget https://github.com/neo4j-contrib/neo4j-jdbc/releases/tag/4.0.2/neo4j-jdbc-driver-4.0.2.jar
    wget https://jdbc.postgresql.org/download/postgresql-42.6.0.jar
    wget https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/download/4.4.0.12/apoc-4.4.0.12-all.ar

Edited pg_hba.conf
 
    sudo nano /etc/postgresql/12/main/pg_hba.conf

![image](https://github.com/arliputraa/batch-data-postgresql-to-neo4j/assets/110078907/a7401432-fec0-45b7-9566-fa08f89c902a)

Edited postgresql.conf and changed the listen parameter

    sudo nano /etc/postgresql/12/main/postgresql.conf

![image](https://github.com/arliputraa/batch-data-postgresql-to-neo4j/assets/110078907/d2b49a62-13fc-4b83-9c9f-6511f185f109)

Config JDBC Postgresql in Neo4j

    CALL apoc.load.jdbc("jdbc:postgresql://192.168.18.198/employees?user=postgres&password=ddi123", "SELECT * FROM employees LIMIT 10;")
    YIELD row
    RETURN row

Example Load in Neo4j Browser 

    CALL apoc.load.jdbc("jdbc:postgresql://192.168.18.198/employees?user=postgres&password=ddi123", "SELECT * FROM employees LIMIT 10;")
    YIELD row
    WITH row.gender AS gender,
         row.emp_no AS emp_no,
         row.birth_date AS birth_date,
         row.last_name AS last_name,
         row.hire_date AS hire_date,
         row.first_name AS first_name;
    MERGE (e:Employee {emp_no: emp_no})
    ON CREATE SET e.gender = gender,
                  e.birth_date = date(birth_date),
                  e.last_name = last_name,
                  e.hire_date = date(hire_date),
                  e.first_name = first_name;

![image](https://github.com/arliputraa/batch-data-postgresql-to-neo4j/assets/110078907/58d63e7f-3697-4133-b653-b01fa1663345)

![image](https://github.com/arliputraa/batch-data-postgresql-to-neo4j/assets/110078907/e759c5ee-99c6-44bc-b6ba-d1a269564044)










