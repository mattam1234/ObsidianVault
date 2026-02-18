# MySQL

**MySQL** is an open-source relational database management system (RDBMS) based on Structured Query Language (SQL). It is one of the most popular databases for web applications.

Project Homepage: [MySQL](https://www.mysql.com/)
Documentation: [MySQL Documentation](https://dev.mysql.com/doc/)

---

## Installation

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
```

**macOS (Homebrew):**
```bash
brew install mysql
brew services start mysql
```

**Docker:**
```bash
docker run --name mysql -e MYSQL_ROOT_PASSWORD=password -d mysql:latest
```

---

## Basic Commands

**Connect to MySQL:**
```bash
mysql -u root -p
mysql -u username -p -h hostname database_name
```

**Database Operations:**
```sql
-- List databases
SHOW DATABASES;

-- Create database
CREATE DATABASE database_name;

-- Use database
USE database_name;

-- Drop database
DROP DATABASE database_name;
```

**Table Operations:**
```sql
-- Show tables
SHOW TABLES;

-- Describe table structure
DESCRIBE table_name;
SHOW CREATE TABLE table_name;

-- Create table
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Drop table
DROP TABLE table_name;
```

**CRUD Operations:**
```sql
-- Insert data
INSERT INTO users (username, email) VALUES ('john', 'john@example.com');

-- Select data
SELECT * FROM users;
SELECT username, email FROM users WHERE id = 1;

-- Update data
UPDATE users SET email = 'newemail@example.com' WHERE id = 1;

-- Delete data
DELETE FROM users WHERE id = 1;
```

---

## User Management

**Create User:**
```sql
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'username'@'%' IDENTIFIED BY 'password';  -- Allow from any host
```

**Grant Privileges:**
```sql
-- Grant all privileges on a database
GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'localhost';

-- Grant specific privileges
GRANT SELECT, INSERT, UPDATE ON database_name.* TO 'username'@'localhost';

-- Apply changes
FLUSH PRIVILEGES;
```

**Show and Revoke Privileges:**
```sql
-- Show user privileges
SHOW GRANTS FOR 'username'@'localhost';

-- Revoke privileges
REVOKE ALL PRIVILEGES ON database_name.* FROM 'username'@'localhost';
```

**Delete User:**
```sql
DROP USER 'username'@'localhost';
```

---

## Backup and Restore

**Backup (mysqldump):**
```bash
# Backup single database
mysqldump -u root -p database_name > backup.sql

# Backup all databases
mysqldump -u root -p --all-databases > all_databases.sql

# Backup specific tables
mysqldump -u root -p database_name table1 table2 > tables.sql

# Backup with compression
mysqldump -u root -p database_name | gzip > backup.sql.gz
```

**Restore:**
```bash
# Restore database
mysql -u root -p database_name < backup.sql

# Restore compressed backup
gunzip < backup.sql.gz | mysql -u root -p database_name

# Restore all databases
mysql -u root -p < all_databases.sql
```

---

## Performance and Optimization

**Show Slow Queries:**
```sql
-- Enable slow query log
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 2;

-- Show current queries
SHOW PROCESSLIST;
```

**Indexes:**
```sql
-- Create index
CREATE INDEX idx_username ON users(username);

-- Create unique index
CREATE UNIQUE INDEX idx_email ON users(email);

-- Show indexes
SHOW INDEX FROM users;

-- Drop index
DROP INDEX idx_username ON users;
```

**Analyze and Optimize:**
```sql
-- Analyze table
ANALYZE TABLE users;

-- Optimize table
OPTIMIZE TABLE users;

-- Check table
CHECK TABLE users;

-- Repair table
REPAIR TABLE users;
```

---

## Configuration

**Common Configuration File Locations:**
- Linux: `/etc/mysql/my.cnf` or `/etc/my.cnf`
- macOS (Homebrew): `/usr/local/etc/my.cnf`
- Windows: `C:\ProgramData\MySQL\MySQL Server 8.0\my.ini`

**Important Settings:**
```ini
[mysqld]
max_connections = 200
innodb_buffer_pool_size = 1G
query_cache_size = 64M
max_allowed_packet = 64M
```

---

## Related Topics

- [[databases/postgres|PostgreSQL]] - Alternative RDBMS
- [[databases/mariadb|MariaDB]] - MySQL fork
- [[databases/sqlite|SQLite]] - Lightweight database
- [[docker/docker|Docker]] - Run MySQL in containers

---

## Best Practices

- Always use prepared statements to prevent SQL injection
- Regularly backup your databases
- Use transactions for data integrity
- Index frequently queried columns
- Normalize database design
- Monitor query performance
- Use connection pooling
- Keep MySQL updated
- Secure user accounts with strong passwords
- Limit user privileges to minimum required