# Connecting-Python-with-MySQL
Here you will find all steps you need to do for connecting Python using Visual Studio Code with MySQL <br>

## First Step: Install MySQL
Download and Install MySQL using this link https://dev.mysql.com/downloads/mysql/ <br>
after Installing it will open MySQL configurator <br>
![image](https://github.com/user-attachments/assets/7467978d-eb7e-48d4-bfb1-ee7cba790bcf)
press next then in account you have to input a password (**Don't forget this Password**) <br>
then next again untill you are in apply configuration tab <br>
In apply configuration tab press execute and when it finishes press next and now congratulation first step is completed <br>

## Second Step: Install Visual Studio Code
You can simply download VS code from Microsoft Store (if you are on Windows) or using this link https://code.visualstudio.com/download <br>
then download python also from Microsoft Store (if you are on Windows) or using this link https://www.python.org/downloads/  <br>
after the download and install is complete open VS code and go to Extensions tab and download Python, Jupyter and MySQL extensions <br>
here is how they look like: <br>
![image](https://github.com/user-attachments/assets/126152a3-1200-4739-a8d5-647db31c7c74)
![image](https://github.com/user-attachments/assets/8188ab4c-1fa8-4404-abb5-8ad923ed8cc8)
![image](https://github.com/user-attachments/assets/b320f78b-6a8e-47e9-a4e0-38966544755f)

## Third Step: Make Database in MySQL
Open **MySQL Command Line Client** 
![image](https://github.com/user-attachments/assets/89ec64ef-4e7e-4a7a-acb5-16e74bf74b20)
Enter password you made in the configurator <br>
then type these queries: <br>
` CREATE DATABASE Manage_Products ` <br>
` USE Manage_Products ` <br>

## Fourth Step: Step up MySQL inside VS Code
1-Open Database Explorer panel, then click the + button. <br>
![connection](https://github.com/user-attachments/assets/6798237d-2696-4487-96c3-2f25620d14f6)
2-Select your database type, input connection config then click the connect button. <br>
while in the connect tab enter a *Database name* **Manage_Products** and *password* you made for MySQL then click connect <br>
you should see the connection like this: <br>
![image](https://github.com/user-attachments/assets/b8b104ff-25b6-4e57-972b-164b64012147)
Congrats now you can use MySQL inside VS code <br>

# Example:
Now this an example on how you can work <br>
first make a Query file in your Database using this + sign: <br>
![Screenshot 2024-09-02 172315](https://github.com/user-attachments/assets/485bd8a0-3d90-413a-b142-1f8170695bb1)
inside Query write this line `show VARIABLES Like 'secure_file_priv';` <br>
this code will show you the directory in which MySQL is reading from so all data that you want to upload must be in this directory <br>
I have left sample data in these three files found in this repo **aisles.csv** , **products.csv** and **departments.csv** <br>
upload it in the directory that was show using the above code <br>
### Now let's make some tables inside our database write the following code:
```
CREATE TABLE Aisles (
    aisle_id INT PRIMARY KEY,
    aisle VARCHAR(255)
);

LOAD DATA INFILE "C:/ProgramData/MySQL/MySQL Server 9.0/Uploads/aisles.csv"
INTO TABLE aisles
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
IGNORE 1 ROWS
(aisle_id, aisle);

CREATE TABLE departments (
    dep_id INT PRIMARY KEY,
    dep VARCHAR(255)
);


LOAD DATA INFILE "C:/ProgramData/MySQL/MySQL Server 9.0/Uploads/departments.csv"
INTO TABLE departments
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
IGNORE 1 ROWS
(dep_id, dep);

CREATE TABLE products(
    product_id INT PRIMARY KEY,
    product_name VARCHAR(255),
    aisle_id INT,
    dep_id INT,
    FOREIGN KEY (aisle_id) REFERENCES aisles(aisle_id),
    FOREIGN KEY (dep_id) REFERENCES departments(dep_id)
);

LOAD DATA INFILE "C:/ProgramData/MySQL/MySQL Server 9.0/Uploads/products.csv"
INTO TABLE products
FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS
(product_id, product_name, aisle_id, dep_id);
```
run this code it will make three tables using data samples <br>
### Trying to add data on MySQL using Python
In this step we will try to add data on the MySQL using Python <br>
we will these data **add_aisles.csv** , **add_products.csv** , **add_departments.csv** you will find them attached in this repo download and save them. <br>
Now make a new jupyter notebook file in VS code: <br>
![image](https://github.com/user-attachments/assets/145ce3f1-f910-4ddc-8254-6436bf3a4c99)
![image](https://github.com/user-attachments/assets/161de891-0670-45f8-8e8b-d7edb80db325)
open command prompt in your computer and write these lines to install some python libraries we will use. <br>
`pip install mysql` <br>
`pip install pandas` <br>
after they finish installing write these codes 
```
import pymysql
import pandas as pd
```
this code will import the libraries we just install <br>
```
dep = pd.read_csv("file_path/add_departments.csv") # remove file path word and enter the file path where you downloaded the sample data
aisles = pd.read_csv("file_path/add_aisles.csv")
products = pd.read_csv("file_path/add_products.csv")  
```
this code will read our downloaded data **add_aisles.csv** , **add_products.csv** , **add_departments.csv** <br>
```
conn = pymysql.connect(
    host='localhost',
    user='root',
    password='your password',          # remove your password and enter the password you made for MySQL
    database='Manage_Products'
)
cursor = conn.cursor()
```
#### Write these three codes to add data to your MySQl tables 
```
for index, row in departments.iterrows():
    cursor.execute("INSERT INTO departments (dep_id, dep) VALUES (%s,%s)",
                   (row['department_id'], row['department']))
    
conn.commit()
```
```
for index, row in aisles.iterrows():
    cursor.execute("INSERT INTO aisles (aisle_id, aisle) VALUES (%s,%s)",
                   (row['aisle_id'], row['aisle']))
    
conn.commit()
```
```
for index, row in products.iterrows():
    cursor.execute("INSERT INTO products (product_id, product_name, aisle_id, dep_id) VALUES (%s,%s,%s,%s)",
                   (row['product_id'], row['product_name'], row['aisle_id'], row['department_id']))
    
conn.commit()
```
Now we can save the complete tables in a new csv files using this code <br>
```
query = f"SELECT * from {table}"
df = pd.read_sql(query, conn)
#save each table to a csv file
df.to_csv(f"choose_path/{table}.csv", index= False)
```






