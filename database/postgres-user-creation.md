# PostgreSQL User creation

The following sequence of commands create a new user in Postgres and give full access for one specific database:

```
create user {user} with encrypted password '{password}';
grant all privileges on database {database} to {user};
```
