# pistahx.io : Design-First Type-Safe Haxe Web API Framework 

* We'll be happy to meet you during WWX2016, the WorldWide Haxe conference in Paris (27-30 May 2016) ! 
* We are thrilled to give you the opportunity to attend to the first and live full presentation of pistahx (40 min talk)

![] (http://www.silexlabs.org/wp-content/uploads/2016/02/wwx2016-bandeau-blog-18fev2016-687x159.png)


# bootstrap a sample pistahx based web api

pistahx-app 
(sample Haxe api using pistahx, pistahx-db, pistahx-spec)


### 1. BUILD LOCALLY ( demo app : mebyz/pistahx-app )


```
git clone github.com:mebyz/pistahx-app

cd pistax-app/

./prepare.sh

gulp build

```

### 2. RUN LOCALLY

```
gulp run

```

#OR

### 1. BUILD FROM DOCKER

```
git clone github.com:mebyz/pistahx-app

cd pistax-app/

docker build -f Dockerfile.build -t pistahx/build .

docker run -d --name pistahx_build pistahx/build tail -f /dev/null

docker cp pistahx_build:/app/distrib/out ./distrib/out

```
=> your built app now resides in distrib/out

### 2. RUN WITH DOCKER-COMPOSE

```
docker-compose build

docker-compose up

```

>
- You'll need **Haxe** installed on your system
>
- Design and implement  your api spec in the **./app/api.yaml** file
>
- Code your business logic in the **./app/Business/** folder
>
- Use the **./app/conf/[env].yaml** file to set your configuration (db user/pass, ...)
>
- you'll need a running **redis server**

-

# pistahx modules :
>
####- https://github.com/mebyz/pistahx.git (pistahx's core)
>
####- https://github.com/mebyz/pistahx-db.git (DB to Haxe typedefs generation lib)
>
####- https://github.com/mebyz/pistahx-spec.git (OpenAPI yaml to Haxe typedefs generation lib)
>
####- https://github.com/mebyz/pistahx-ui.git (Typescript Angular2 Bootstrap4 UI for pistahx apps)
>
####- https://github.com/mebyz/pistahx-app.git (pistahx demo api, use it to bootstrap your own api !)

-

# pistahx Building blocks :
>![] (https://raw.githubusercontent.com/mebyz/pistahx/master/pistahx-stack-mini.png)

-

# pistahx promises:

>- **PERFORMANCE :** pistahx heavily relies on **many cache layers**, and let you define your own **cache invalidation strategies**
  - Custom output cache for each route based on your api specs
  - Secure Cookie based Session / Auth token Redis store
  - Pro-active cascading cache invalidation strategies
  - Db caching (via Sequelize) : use cache to store entities and queries
  - Define your own caching strategies for fine tuning

-

>- **SECURITY :** pistahx offers you **multiple authentication strategies** activate them in your configuration file ./app/conf/[env].yaml 
  - JSON Web Tokens (JWT) : if you want to build RestFULL APIs or if you have to deal with cookie-less clients
  - Standard but fast and secure sessions strategy if you're setting up a service targeting Web browsers supporting cookies
  - pistahx also implements Passport.js (which includes 300+ more auth strategies) !

-

>- **MONITORING :** 
  - pistahx implements monitoring natively, with the support of the gorgious **ELK stack + appmetrics**. use ELK_SERVER parameter in your ./app/conf/[env].yaml file and you're good to go !

-

>- **DESIGN FIRST :** 
  - pistahx follows the **openapi** specification. creating and modifying your api is done using **yaml language**

-

>- **MOCK YOUR TESTS IN THE SPEC DESCRIPTION :** 
  - simply mock some test cases (a request and its response) in the **yaml specification** file  and let **mocha** do the magic !

-

>- **AUTOMATIC DOCGEN :** 
  - an **interactive documentation** is automatically generated from the spec file 

-

>- **AUTOMATIC CODEGEN :** 
  - API server code (routing, server core...) is **automatically generated** from the spec file

-

>- **CORE / BUSINESS CODE LOGIC SEPARATION :** 
  - your business logic **is separated from** pistahx server core during the whole life of your project.

-

>- **STRONGLY TYPED CODE :** 
  - Write your business logic using **Haxe language** (type checking helps the code to stay clean and secure). pistahx tranpiles your api to nodejs.

-

>- **OPEN SOURCE, MULTIOS, FAST BOOTSTRAPPING STACK :** 
  - pistahx can be set up (within minutes), modified and deployed **anywhere nodejs can run** : Unix/Linux, Windows, OSx, ...

-

>- **MULTIDB :** 
  -  pistahx uses **Sequelize** : connect to mssql, mysql, pgsql, sqlite,... databases

-

>- **PROMISES-FULL :** 
  -  Use the great power of promises using a simple Haxe ( **Promhx, Thx.*,..** ) workflow

-

>- **DB PROMISES :** 
  -  pistahx handles **parallel db querying strategies**

-

>- **CONTAINER READY :** 
  -  pistahx comes with a **native docker container environment** for your app. You can now trully **deploy anywhere !**
 
# pistahx INSIGHTS : code

**SAMPLE CODE : OPEN API YAML ROUTE DEFINITION**

```yaml
paths:
  /employees:
    get:
      operationId: employees
      tags:
      - "Employees"
      summary: "{'ttl':3600,'xttl':3600,'cachekey':'','xcachekey':''}"
      description: "/employees returns a list of employee"
      responses:
        200:
          description: "An array of employees"
          schema:
            $ref: "#/definitions/Employees"
  /employee/{EmployeeId}:
    get:
      operationId: employee
      tags:
      - "Employees"
      summary: "{'ttl':3600,'xttl':3600,'cachekey':'','xcachekey':''}"
      description: "/employee/{EmployeeId} returns an employee"
      parameters:
      - name: "EmployeeId"
        in: "path"
        description: "EmployeeId"
        required: true
        type: "integer"
      responses:
        200:
          description: "An employee"
          schema:
            $ref: "#/definitions/Employee"
  /employee:
    put:
      operationId: employee
      tags:
      - "Employees"
      summary: "{'ttl':0,'xttl':0,'cachekey':'','xcachekey':''}"
      description: "/employee saves an employee"
      x-cache-flush: 
      - "/employees"
      parameters:
      - name: "employee"
        in: "body"
        description: "Employee to add"
        required: true
        schema:
          $ref: '#/definitions/Employee'
      responses:
        200:
          description: "200 is returned"
```

**YAML CONFIGURATION : example here : ./app/conf/local.yaml**

```yaml
APP_NAME: pistahx_app
ENV_NAME: local
#ELK_SERVER: to be defined
#JWT_SECRET: local_secret_key
#JWT_TTL: 3600
CACHE_OUT_TTL_DEFAULT: 60
#GOOGLE_CLIENT_ID: to be defined
#GOOGLE_CLIENT_SECRET: to be defined
#GOOGLE_CALLBACK_URL: http://localhost:3000/callback
REDIS_HOST: localhost
REDIS_PORT: 6379
DB_HOST:
DB_USER:
DB_PASSWORD:
DB_NAME:
DB_OPTIONS: 
  dialect: sqlite
  storage: Chinook_Sqlite.sqlite
  pool: 
    max: 5
    min: 0
    idle: 10000
  dialectOptions:
    encrypt: true
  logging: true
SESSION_TTL: 3600
BASE_URL: /api/v1
API_PORT: 3000
API_ROUTE_STATUS: /users/me/status
#API_CORS_ALLOWED:
#  - "http://localhost:9000"
#  - "http://localhost:8080"
#  - "http://localhost:3000"
#S3_ID: "to be defined"
#S3_KEY: "to be defined"
#S3_BUCKET: "to be defined"
#S3_REGION: "to be defined"
#S3_FOLDER: "to be defined"
```

**SAMPLE CODE : Sample GET Business method using pistahx-db generated typedefs, and pistahx-spec generated typedefs & mappers**
```haxe     

  typedef DBEmployees = {
    findAll : FindAllOptions -> Promise<Array<DB__Employee>>,
    find : FindOptions -> Promise<DB__Employee>
  }

  @:publicFields
  class DbRepos {

    var dbEmployees : DBEmployees;

    function new(db : Sequelize) {
      dbEmployees = db.import_("models/Employee.js");
      untyped dbAlbums.belongsTo(dbArtist, {foreignKey: 'ArtistId'});
    }

  }

...
...

  public static function get_employees(db : Sequelize,req : ClientRequest, res : ServerResponse, dbcacher : Dynamic,outputcacher : Dynamic, extra : Dynamic) : Promise<Array<Employee>> {    

    var dbr = new DbRepos(db);

    return
      dbr.dbEmployees.findAll({
        limit : 5
      }).then(function (dbEmployeesRes) {
        return dbEmployeesRes.map(EmployeeMapper.dbEmployeeToEmployee); 
      });

  }

  public static function get_employee(db : Sequelize,req : ClientRequest, res : ServerResponse, dbcacher : Dynamic,outputcacher : Dynamic, extra : Dynamic) : Promise<Employee> {
  
    var dbr = new DbRepos(db);

    return
      dbr.dbEmployees.find({
         where: [ { 'EmployeeId' : untyped req.params.EmployeeId } ]
      }).then(function (dbEmployeeRes) {
        return EmployeeMapper.dbEmployeeToEmployee(dbEmployeeRes);
      });
  }


```     

**SAMPLE CODE : HAXE PROMISES**

```haxe			
    public static function firstPromise(/* some args here, such as "myArg : Int" */) : Promise</* return type here, such as "String"*/>{
      var p = new Deferred<String>();
        some.async.Method(/* some params */, function(err,data){
          p.resolve(data);
        });
      return p.promise();
    }
  
    public static function secondPromise(/* some args here, such as "myArg : Int" */) : Promise</* return type here, such as "String"*/>{
      var p = new Deferred<String>();
        some.async.OtherMethod(/* some params */, function(err,data){
          p.resolve(data);
        });
      return p.promise();
    }

    public static function finalPromise(	myArg1 : /* return type of firstPromise */, 
					myArg2 : /* return type of secondPromise */
				   ) : Promise</* return type here, such as "String"*/> {
      var p = new Deferred<String>();
      	 // do something with the results
        some.async.FinalMethod(myArg1, myArg2 , function(err,data){
	  // here is our final result
          p.resolve(data);
        });
      return p.promise();
    }

    // NOW YOU CAN USE YOUR PROMISES AS FOLLOWS : 

    // TRIGGER YOUR FIRST PROMISE => try to fullfill 2 subpromises (parallel)
    Promise.when(			
      firstPromise(/* some args for this first promise */), 
      secondPromise(/* some args for this second promise */)
    )
    .then(
      // WHEN BOTH SUBPROMISES HAVE BEEN FULFILLED :
      // TRIGGER 2ND PROMISE => gather fulfilled sub-promises results and process them
      function(a,b) return finalPromise(a,b)
    )
    .then(function(b) {
	    trace("everything went ok. rainbow.");
    });
```

**SAMPLE CODE : PARALLEL QUERIES USING PROMISES**

```haxe		
     // PARALLEL CALL SOME QUERIES USING SEQUELIZE PROMISES
      var SPromise = Sequelize.Promise;
      SPromise.map([
          sql1,
          sql2
      ], function runQuery(query) {
          return db.query(query);
      }).then(function(result) {
      		// result now contains data from sql1 AND sql2 execution
          trace(result);
      });
```		

# End-to-End TESTS

**REMINDER : TESTS (AND MOCK DATAS) ARE DEFINED IN YOUR PROJECT'S API.YAML FILE**

=> For each route defined in the spec, you can/should add a `x-amples` key in wich you can write new tests.

```yaml
paths:
  /users/me/status:
    get:
      ...
      ...
      x-amples:
      - title: TEST1 - fail to auth user 1
        description: "fail to auth user"
        request: {}
        response:
          status: 401
      - title: TEST2 - fail to auth user 2
        description: "fail to auth user"
        request: 
          headers: 
            Authorization: "Basic bad_auth_hash_key"
        response:
          status: 401
      - title: TEST3 - auth user ok
        description: "auth user ok"
        request: 
          headers: 
            Authorization: "Basic ZDSGOJGDFJKLGFJKSFLDGJLJGSFKLFDJGSLJFDGSLJ="
        response:
          status: 200
          body: ''
```

**--- run tests locally:**

1 . Change the host value in the api.yaml file to `localhost:3000` 

2 . You will need a running api (local)

3 . Run the test suite using mocha **(run this from the project's root folder, NOT from the ./distrib/ folder !)** :

`mocha test.js`

# DOCS

- **api description** resides in your project's `./app/api.yaml` file

- **api interactive doc** can be seen here : `http://[host]/doc`

- **haxedoc** can be seen here : `http://[host]/haxedoc`
