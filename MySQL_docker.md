
# MySQL Docker Container with Persistent Volume and CSV File Loading

## Step 1: Pull MySQL Docker Image

First, pull the MySQL Docker image from Docker Hub.

```sh
docker pull mysql:latest
```

## Step 2: Create a Docker Volume

Create a Docker volume to store MySQL data persistently.

```sh
docker volume create mysql-data
```

## Step 3: Run MySQL Container with the Volume

Run the MySQL container and mount the volume to the appropriate directory inside the container.

```sh
docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=mydatabase -v mysql-data:/var/lib/mysql -d mysql:latest
```

In this command:
- `-e MYSQL_ROOT_PASSWORD=my-secret-pw` sets the root password.
- `-e MYSQL_DATABASE=mydatabase` creates a database named `mydatabase`.
- `-v mysql-data:/var/lib/mysql` mounts the `mysql-data` volume to `/var/lib/mysql` inside the container.

## Step 4: Copy CSV File to Docker Container

Copy the CSV file from your local machine to the Docker container.

```sh
docker cp data.csv mysql-container:/tmp/data.csv
```

## Step 5: Access MySQL Container

Access the running MySQL container to load the CSV file.

```sh
docker exec -it mysql-container bash
```

## Step 6: Load CSV File into MySQL Database

Inside the MySQL container, log in to the MySQL database using the root credentials.

```sh
mysql -u root -p
# Enter the root password when prompted
```

Create a table that matches the structure of your CSV file. For example, if your CSV file has columns `id`, `name`, and `value`, create a table accordingly.

```sql
USE mydatabase;

CREATE TABLE mytable (
    id INT,
    name VARCHAR(100),
    value DECIMAL(10, 2)
);
```

Load the CSV file into the table using the `LOAD DATA INFILE` command.

```sql
LOAD DATA INFILE '/tmp/data.csv'
INTO TABLE mytable
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

## Step 7: Verify the Data

Verify that the data has been loaded correctly by querying the table.

```sql
SELECT * FROM mytable;
```

## Additional Tips

### Backing Up Data

You can back up the data by creating a backup of the Docker volume.

```sh
docker run --rm -v mysql-data:/volume -v $(pwd):/backup busybox tar cvf /backup/backup.tar /volume
```

### Restoring Data

To restore data from a backup tar file:

```sh
docker run --rm -v mysql-data:/volume -v $(pwd):/backup busybox tar xvf /backup/backup.tar -C /
```

### Remove the Volume

If you need to remove the volume, you can do so with:

```sh
docker volume rm mysql-data
```

## Summary

1. Pull the MySQL Docker image.
2. Create a Docker volume.
3. Run the MySQL container with the volume.
4. Copy the CSV file to the Docker container.
5. Access the MySQL container.
6. Load the CSV file into the MySQL database.
7. Verify the data.

By following these steps, you can ensure that your MySQL data persists even if the container is stopped or removed, and you can load a CSV file from your local machine into the MySQL database.
