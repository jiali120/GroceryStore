# GroceryStore

# Introduction



# Setting Up Your Environment

## Prerequisites

* JDK: At least JDK17.
* IDE: Intellij is preferred. VSCode is good.
* Version control: Git is a must. Github CLI, and Github Desktop are optional but good to have.
* DB: MySQL, MySQL Workbench. Require both at v8.0+. 
* Virtualization : We use Docker.
* For local testing, update /backend/src/main/resources/application.properties to use spring.datasource.url=jdbc:mysql://localhost:3306/dronedelivery instead of spring.datasource.url=jdbc:mysql://mysqldb:3306/dronedelivery

## How to start

#### Build with Docker
Start docker desktop then run the following commands
```
cd <your repo directory>
docker build -t gatech/backend -f ./images/Dockerfile.backend ./backend
docker build -t gatech/frontend -f ./images/Dockerfile.frontend ./frontend
docker-compose up site
docker-compose run --rm api
```
The first two commands are building the backend and frontend images defined by our dockerfiles, and the third is using docker-compose to deploy the service locally. To break down the first command we can read it as "build an imageusing the ./images/Dockerfile.backend file from the ./backend directory and tag it as gatech/backend" To break down the third command, we can read it as "deploy a service called gatech as defined in file docker-compose.yml
REMEMBER to clean up all docker images and containers before redeploying.

### Step 1: Clone the repo

```bash
## HTTPS
git clone github.gatech.edu/zyang701/omscs-cs6310-23F-group18.git

## Github CLI 
gh repo clone github.gatech.edu/zyang701/omscs-cs6310-23F-group18
```
### Step 2: MySQL
If you don't have MySQL installed yet, for Mac users, one can follow [this guide](https://flaviocopes.com/mysql-how-to-install/) with the help of `homebrew`. 
For Windows, download and install mysql https://dev.mysql.com/downloads/installer/. Walk through the guide to install MySQL server and workbench. Use the user name and password specified in backend\src\main\resources\sql\application.properties for log in.

Suppose you have installed MySQL and MySQL workbench, check the following 2 before continuing:

* You have started MySQL server. Check if the server runs in daemon mode. For windows, open task manager and ensure MySQL80 is started under Services tab.
* You have set up a root user.

Now, we are going to create the database and the database user account. This can be done within MySQL workbench. 
#### With MySQL workbench:
Create a MySQL connection with root user, if you don't already have one. 
Import or copy the sql script from `/backend/src/main/resources/create-user-and-database.sql`.   
Execute and you should have the database and user available.

### Step 3: Backend

Open `./backend/` as a project in Intellij. Alternatively, you can simply drag the folder to Intellij.
For windows, if dependencies did not start downloading automatically, try opening ./backend/pom.xml as project and it should start downloading dependencies.

#### Start backend with Intellij
Build the project and run the `DroneDeliveryApplication.java`. We should have our backend running successfully. 
You can interact with the app with command line.

```
## RESTful apis 
http://localhost:8080/api/
```

### Step 4: Frontend

Open your termianl, make sure you are at repo's root folder rather than `./frontend`. 

#### Start frontend with Docker
Currently (11172023) we have only plain html and es6. 

Run the following: 
```bash
docker build -t gatech/frontend -f ./images/Dockerfile.frontend ./frontend
docker-compose run --rm api 
```

Note: "gatech/frontend" is the image name, and "gatech" is the container name. We can change both later.

Your webapp is available at
```
http://localhost:3001/
```

## Quick Start - cheatsheet
Placeholder.

# Backend:
## Backend in general
### Springboot backend, from scratch
If you are interested in how the backend is built from scratch, the anwser is simple:

There is an official [Spring Initializr](https://start.spring.io/) that generates the starter code for us. Open the website and do the following:
* Select Java and maven as language and project, respectively.
* Select a springboot version.
* Put project metadata.
* Add dependencies:
    * Springboot DevTools
    * Spring Web
    * MySQL Driver

You are all set. Cheeers.

### maven
Just in case no experience with maven: maven to Java is like the webpack to javascript. Check dependency details in `pom.xml`.

### Springboot `application.properties`:

You find this file under `resources`.
Check your database connection credentials here.

Adjust the following accordingly:

```
## start the backend and reset db data
spring.jpa.hibernate.ddl-auto=create

## start the backend and do NOT reset db data
spring.jpa.hibernate.ddl-auto=update
```


### Main
`DroneDeliveryApplication.java` is the entry point of the backend. It does the following:

* Enables backend services
* Hosts restful api
* Executes command line input

### 3-tier architecture

We make use of the good old 3-tier architechture which separate the code into 3 layers:

* Entity: 
    *  In our project, they are basically POJOs (Plain Old Java Objects) annotated by Jakarta Persistence (formerly known as Java Persistence API or JPA).
    * Entities define the structure and behavior of the data.
    * files are in entity package.
* Data Access Object(DAO):
    * The DAO layer is responsible for encapsulating the logic required to access and manipulate data stored in MySQL. 
    * files are in dao package.
* Service:
    * Service layer contains core business logic. It calls DAO layer when necessary.
    * files are in service package.

## Springboot in details

### Dependency Injection

Note: Do NOT use Field Injection. 
A field injection is where Spring directly injects the dependencies into annotated fields, like this
```java
@Autowired
private SomeDependency dependency;
```
Field injection was once widely used. It was even used in the sample code officially provided by CS6310. It can reduce constructor complexity, which is true. However, it is no longer recommended as the best practice because: 
* it makes unit tests hard to perform.
* it may causes circular dependencies issues.

Note: Use Constructor Injection or Setter Injection.

```java
private UserDAO userDAO;
// Constructor Injection
@Autowired
public UserServiceImpl(UserDAO userDAO) {
    this.userDAO = userDAO;
}

// Setter Injection
@Autowired
public void setUserDAO(UserDAO userDAO) {
    this.userDAO = userDAO;
}
```


For setter injection, you don't have to call the setters explicitly: Springboot handles it for you.

# Commands used for Security modification
```
make_store, kroger, 132, 2134, 1234
login, admin, password
make_store, kroger, 132, 2134, 1234
make_customer,customerId1, Ninong, Liu, 999-888-5555, 5, 920, password1
make_customer,customerId2, DongChen, Guan, 439-888-5555, 3, 234,password2
make_customer,customerId3, ZiChao, Yang, 439-828-5555, 3, 532, password3
make_customer,customerId4, Jia, Li, 439-828-5555, 5, 45, password4
make_customer,customerId5, ShaoQiu, Weng, 439-828-5555, 5, 656, password5
make_store_owner, storeOwnerId1, storeOwnerFirstName, storeOwnerLastName, 999-868-5555, password2, kroger
logout
login,customerId1,password1
make_item, kroger, coke, 30, 53
logout
login,storeOwnerId1,password2
make_item, kroger, coke, 30, 53
display_items, kroger
display_items, storeName
```

# Commands used for time-sensitive coupon

```
login, admin, password
make_store, kroger, 10000, 0, 0
make_customer, customerId, first,    last,    990-123-2134, 8,      2000,   good_password
make_pilot, ppilot1, f, l, 990-123-2134, 22, 22, 22, 223, 213
make_pilot, ppilot2, f, l, 996-996-1234, 22, 22, 22, 223, 213
make_drone, kroger, dronedrone1, 300, 150, 5, 1
make_drone, kroger, dronedrone2, 300, 150, 5, 1
add_pilot_to_drone, kroger, dronedrone1, ppilot1
add_pilot_to_drone, kroger, dronedrone2, ppilot2
// create a beer: 50lb and for each
make_item, kroger, beer, 50, 10
make_order, kroger, customerId, orderName, 30, 40
add_line_to_order, kroger, customerId, orderName, beer, 5
add_drone_to_order, kroger, orderName, dronedrone2
make_coupon, couponV90, 5, 9, 0.9
make_coupon, couponX80, 15, 9, 0.8
add_coupon_to_order, kroger, orderName, couponX80
add_coupon_to_order, kroger, orderName, couponV90
transfer_order, kroger, orderName, dronedrone2, dronedrone1
purchase_order, kroger, orderName, customerId
```





# Other random tips
It's a common practice to place @Transactional annotations on service layer methods rather than DAO (Data Access Object) methods. 
If you remove the @JoinColumn annotation from your @ManyToOne association, the JPA provider will use default naming conventions to determine the name of the foreign key column in the database. 
Cannot use ORDER as a table name because it is a keyword in SQL.
Try build -> rebuild project to resolve some building issue.

