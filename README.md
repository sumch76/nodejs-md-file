```const express=require("express");
const app=express();
app.use((req,res)=>{
    res.send("hello from the server");
});
app.listen(3000,()=>{
    console.log("server is successfully listening on port 3000..");
    
});
```

- it wll always show a "hello from the server" whatevr be the request i.e if i write  `localhost:3000/hello` it will show the hello from the server


### to change this we have to do changes on the code //////////////

```javascript
const express=require("express");
const app=express();
app.use("/home "(req,res)=>{
    res.send("hello from the server");
});
app.listen(3000,()=>{
    console.log("server is successfully listening on port 3000..");
    
});
```
- while doing this i am getting response on `http://localhost:3000/home  `  not on `http://localhost:3000 ` it shows cannot get

`we need to create different different routes`
> ### `app.get()` is used for handling specific GET requests. This way, /home and /contact won't be affected by the root path /.

>### `app.use()` is for middleware and will trigger for every route if the base path matches. In your case, app.use("/", ...) matches every route since all routes are prefixed with /.


## Difference between  Caret (`^`) vs. Tilde (`~`)

>### In JavaScript (specifically in the context of package management with npm or yarn), the caret (^) and tilde (~) symbols are used in package.json to specify version ranges. They indicate how flexible the versioning of a package is when you're installing dependencies.

## `1.Caret (^):`
- #### The caret symbol allows updates to non-breaking changes based on semver (Semantic Versioning) rules.
- #### It allows updates to `minor and patch versions` but not major versions.
## Example:

```
"lodash": "^4.17.0"
```
- #### This means that the version can update to anything from 4.17.0 to <5.0.0.
- #### It will allow updates like 4.17.1, 4.18.0, etc., but not 5.0.0 because a major version change may introduce breaking changes.

## In summary:
---

- #### ^1.2.3 allows updates to any version >=1.2.3 and <2.0.0.
- #### ^0.2.3 (if the major version is 0) allows updates to >=0.2.3 and <0.3.0 (no major version bump when the major version is 0).

## `2. Tilde (~):`
- The tilde symbol allows updates to the patch version, but not the minor or major version.
It only allows updates within the same minor version.

### **Example**:

```
"lodash": "~4.17.0"
```
- #### This means the version can update to anything from 4.17.0 to <4.18.0.
- #### It will allow updates like 4.17.1, 4.17.2, etc., but not 4.18.0 because the minor version must stay the same.

### In summary:

- #### ~1.2.3 allows updates to >=1.2.3 and <1.3.0.
- #### ~0.2.3 allows updates to >=0.2.3 and <0.3.0.

### Key Differences:

- #### Caret (^) is more permissive and allows updates to both minor and patch versions.
- #### Tilde (~) is more restrictive and only allows updates to patch versions within the same minor version.

## When to use:
- ### `Use caret (^)` when you're okay with` both minor and patch updates`, trusting the package won't introduce breaking changes.
- ### `Use tilde (~)` when you want to restrict updates to `only patch versions`, ensuring more stability

## Detailed exploration of +, *, and ? in Express.js route paths, including use cases, practical examples, and potential pitfalls.

### 1. The + Symbol: Match One or More of the Preceding Character

- **Definition:** In Express.js, `+` is used to specify that the character or group of characters immediately preceding it must appear one or more times. It is used as part of regular expression patterns in routes.

**Practical Example:**
```js
app.get('/a+', (req, res) => {
    res.send('Matched!');
});
```

**Explanation:**

- This route will match any path that starts with at least one a.
- Examples of matching routes: /a, /aa, /aaa, /aaaaaaaaaa, etc.
- Routes that do not match: /, /b, /ab, /x.

**Use Case:**

- Suppose you are building a route that should match a repeated pattern, such as detecting multiple exclamations like /woww!.
- You can use + to allow users to enter various levels of excitement:
```js
app.get('/wow+', (req, res) => {
    res.send('Excitement detected!');
});
```
- This would match /wow, /woww, /wowwwwww, and so on.

**Potential Pitfall:**

- If you need to match an exact string and not a pattern of repetition, avoid using +. Otherwise, users could match URLs unintentionally by entering a repeated character.

### 2. The * Symbol: Match Any Sequence of Characters

- **Definition:** The * symbol is a wildcard character that matches zero or more characters. It essentially says "match everything" from this point onwards, allowing any characters to follow the path.

**Practical Example:**
```js
app.get('/anything/*', (req, res) => {
    res.send('Wildcard matched!');
});
```
**Explanation:**

- This route matches any path that starts with /anything/ and is followed by any number of characters (including no characters).

- Examples of matching routes: /anything/abc, /anything/123, /anything/foo/bar, /anything/.

- Routes that do not match: /any, /something/.

**Use Case:**

- You might use * to build a catch-all route. For example, if you want to create a route for serving static files where users can request any file under a specific directory, you can use:
```js
app.get('/public/*', (req, res) => {
    const filePath = req.params[0]; // Get the part after /public/
    res.sendFile(`/path/to/files/${filePath}`);
});
```
- Here, `/public/*` matches any file requested in the `/public/ `directory `(e.g., /public/index.html`, `/public/images/photo.jpg).`

**Potential Pitfall:**

- Using * can make your routing too broad. If you don’t handle it carefully, it might match routes you don’t intend, which could lead to ambiguous routing behavior.
- For instance, if you have /anything/*, it will match everything after /anything/, which might conflict with more specific routes.

### 3. The ? Symbol: Match Zero or One of the Preceding Character

- **Definition:** The ? symbol makes the preceding character optional. The character immediately before ? can appear either once or not at all.

**Practical Example:**
```js
app.get('/colou?r', (req, res) => {
    res.send('Matches both "color" and "colour"');
});
```
**Explanation:**

- The ? here is placed after u, meaning that u is optional. Therefore, both /color and /colour would match this route.
- Examples of matching routes: /color, /colour.
- Routes that do not match: /colouur, /collor.

**Use Case:**
- This is useful for handling regional spelling differences in URLs (e.g., American vs. British spelling) or optional URL parameters.

- Another common use case is matching URLs where a part of the path may or may not be present. For instance, if you want to make /admin or /admin/ match, you could write:

```js
app.get('/admin/?', (req, res) => {
    res.send('Admin page');
});
```
- Here, the /admin/? route matches both /admin and /admin/.

**Potential Pitfall:**

- Be careful with overusing ? because it makes the route less deterministic, potentially leading to unintended matches. For example, if you use it too broadly, users might misspell paths and still hit valid routes.

**Combination of +, *, and ? in Routes:**

- You can also combine these symbols for more complex route patterns:

**Example 1:** Matching a Repeated Optional Character
```js
app.get('/colou?r+', (req, res) => {
    res.send('Matches repeated optional "u"');
});
```
- This will match any path that contains /color or /colour followed by one or more "r" characters, such as /colorrr or /colourrr.

**Example 2:** Match Everything after a Route:
```js
Copy code
app.get('/blog/*?', (req, res) => {
    res.send('Blog route with optional anything after');
});
```
- This route will match /blog, /blog/, and any other path starting with /blog/, followed by any characters (like /blog/post/123).
----


## - episode 6 

```javascript
const express = require("express");
const app = express();

app.use("/admin", (req, res, next) => {
  console.log("admin auth is getting checked");
  const token = "xyz";
  const isAdminAuthorized = token === "xyz";
  if (!isAdminAuthorized) {
    res.status(401).send("unauthorized request");
  } else {
    next();
  }
});

// Define a route for /admin
app.get("/admin", (req, res) => {
  res.send("Admin dashboard or other admin-related info");
});

app.get("/user", (req, res) => {
  res.send("user data sent");
});

app.get("/admin/getAllData", (req, res) => {
  res.send("You have access to all data");
});

app.get("/admin/deleteUser", (req, res) => {
  res.send("delete a user");
});

app.listen(3000, () => {
  console.log("Server is successfully listening on port 3000..");
});
```

#### 1. app.use()
- **Purpose:** app.use() is used to apply middleware. Middleware functions are executed in sequence for every incoming request that matches the given path, regardless of the HTTP method (e.g., GET, POST, PUT).
- **Execution:** It doesn't care about the specific HTTP method (GET, POST, etc.) and applies to all of them unless you specify otherwise.
- **Path:** It can take a path (like /admin) and apply the middleware to any route that starts with that path (e.g., /admin/getAllData, /admin/deleteUser).
 ```
 app.use("/admin", (req, res, next) =>
  {
  console.log("Checking authorization for admin routes");
  next(); 
  // Move to the next middleware or route handler
});
```
- In this example, any request to a path that starts with /admin (like /admin/getAllData) will trigger this middleware. It will run for all HTTP methods.

#### **2. app.get()**
- **Purpose:** app.get() is used to define a route that responds to GET requests. It listens for GET requests on a specific path and executes the associated callback function.
- **Execution:** Only responds to GET requests.
- **Path:** It is tied to a specific route and will only handle GET requests to that exact path (or a path pattern).

 **Example:**

```
app.get("/user", (req, res) => {
  res.send("User data sent");
});
```
- In this example, only GET requests to /user will be handled by this route. It won’t affect POST, PUT, or other methods.

```javascript
const express = require("express");
const app = express();
const { adminAuth } = require("./middlewares/auth.js");

app.use("/admin",adminAuth);

app.get("/admin",(req,res)=>{
  res.send("finally on admin using  get");
  
})

app.get("/user", (req, res) => {
 
  res.send("user data sent");

});

app.get("/admin/getAllData", (req, res) => {
 
    res.send("You have access to all data");
 
});
app.get("/admin/deleteUser",(req,res)=>
{
  res.send("delte a user");
});


app.listen(3000, () => {
  console.log("Server is successfully listening on port 3000..");
});
```

## Connecting database to the server..
- first we have to create a config folder then inside this we have to create a database.js file

- make suree you have install mongoose `(npm i mongoose)`

#### how the database structure looks like

```javascript
const mongoose=require('mongoose');

const connectDB=async()=>
{
    await mongoose.connect(
        "mongodb+srv://sumitchaubey76:password@mongodbtest.9axqh.mongodb.net/DevTinder"
                          );
};
connectDB().then(()=>{
    console.log("database connection established");
    
}).catch((err)=>
{
    console.log("database cannot be connected");
    
});
```
#### how app.js structue looks like

```javascript
const express = require("express");
require("./config/database");
const app = express();

app.listen(3000, () => {
  console.log("Server is successfully listening on port 3000..");
});
```
- **but this is not right way to do it becausee first it goes to server then database is coonecting.**  

```
Server is successfully listening on port 3000..
database connection established
```

### now we need to first database coonction established then server is running on port 3000 i.e  `this is the right process`

## good way
#### `app.js file`
```javascript
const express = require("express");
const connectDB=require("./config/database");
const app = express();

connectDB().then(()=>{
  console.log("database connection established");
  app.listen(3000, () => {
    console.log("Server is successfully listening on port 3000..");
  });
  
  
}).catch((err)=>
{
  console.log("database cannot be connected");
  
});
```
---

### Database.js file inside comfig
```javascript
const mongoose=require('mongoose');

const connectDB=async()=>
{
    await mongoose.connect(
        "mongodb+srv://sumitchaubey76:Tq9KWbiJafPhz9qu@mongodbtest.9axqh.mongodb.net/DevTinder"
                          );
};
module.exports=connectDB
```
```
database connection established
Server is successfully listening on port 3000..
```
------
## How to create schema

- **we need to create a folder models and inside this user.js folder**
```javascript
const mongoose=require('mongoose');

const userSchema= new mongoose.Schema({
    firstName:{
        type: String,
    },
    lastName:{
        type:String
    },
    emailId:{
        type: String,
        unique: true
    },
    password:{
        type: String
    },
    age:{ 
        type: Number
    },
    gender:{
        type:String
    }

    });
    // const User= mongoose.model("User",userSchema);
    // module.exports=User; 

    //or
    module.exports=mongoose.model("User",userSchema);
```
> **this is schema we need to do write the same details as given in the schema `if we do not write a same name we will get error`**

#### **in app.js we need to add user instance**
```javascript
const express = require("express");
const connectDB=require("./config/database");
const app = express();


constUser=require("./models/user");//

app.post("/signup", async(req,res)=>{
  const user=new User({
    firstName:"sumit",
    lastName:"chaubey",
    emailId:"sumit76@gmail.com",
    password:"123456"
  });
  await user.save();
  res.send("user added successfully");

}) //

connectDB().then(()=>{
  console.log("database connection established");
  app.listen(3000, () => {
    console.log("Server is successfully listening on port 3000..");
  });
  

  
}).catch((err)=>
{
  console.log("database cannot be connected");
  
});
```
`// means added code`
---
**after this we need to call a API through postman ie. `http://localhost:3000/post` it will give response**
---------
**and after this we get a data in my cluster database which i have created DeVTinder inside this i.e `DevTinder.users` i get all details**

>- `devTinder` is a database.
>- `users` is a collection.
>- the info inside it , is a document

### __it is better to add in try and catch block__ (error handling)
```javascript
try{
    await user.save();
    res.send("user added successfully");
  }
  catch(err){
    res.status(400).send("error while adding user");
  }
  
  ```
  ---
  # EPISODE 7⬇️
  ### __Javascript Object vs JSON__
  **1. JavaScript Object**
  - `Type:` A built-in data type in JavaScript.

- `Usage:` Used to store key-value pairs, functions, and other complex data in JavaScript code.

 **`Syntax:`**

- Properties can be unquoted.
- Can include methods (functions).
- Flexible in terms of data types (numbers, strings, arrays, functions, etc.).
```javascript
let person = {
  name: 'John',
  age: 30,
  greet: function() {
    console.log('Hello');
  }
};
```
 **2. JSON(javascript object notation)**

 
- `Type:` A lightweight data interchange format.

- `Usage:` Used primarily for data transmission (e.g., between client and server) and data storage.

**`Syntax:`**

- Keys and strings must be double-quoted.
- Does not support functions or undefined values.
- Supports simple data types like strings, numbers, booleans, arrays, and objects.
```json
{
  "name": "John",
  "age": 30
}
```
---

| Feature | JS Object | JSON|
| -------- | ------- |--------|
| **Format** | Flexible|Strict (double quotes for keys)|
| **Data Types** | Can include functions, undefined |Only supports simple data types|
| **Use** |For working with data in JavaScript code |For data storage/transfer|
|**Methods**|can have methods| cannot include methods|

- ### this data is hard-coded  we need to pass dynamic data 
- we will go to postman then will go to request then body=>raw=>JSON 
- now in json we have to pass dynamic data in json format.
```json
{
    "firstName":"kahuri",
    "lastName":"singh",
    "emailId":"sarfbjd@gmail.com",
    "password":"12345"
}

```
- now  to access the body of json we need to  do a `req.body`
- but it will give **undefined** because our server is  unable to read the json data
- to read the json data we need a help of middleware 

- a middleware which is given by express ie. `express.json`
- and we know how to use middleware  using app.use
```
app.use(express.json());
```
- we need to simply add  `req.body` inside the new User

```javascript
const express = require("express");

const connectDB=require("./config/database");

const app = express();

const User=require("./models/user");
 app.use(express.json());✅

app.post("/signup", async(req,res)=>{
  //console.log(req.body);
  
 const user=new User(req.body);✅
  try{   
    await user.save();
    res.send("user added successfully");
  } 
  catch(err){
    res.status(400).send("error while adding user");
  }
 
})  
connectDB().then(()=>{
  console.log("database connection established");
  app.listen(3000, () => {
    console.log("Server is successfully listening on port 3000..");
  });
  

  
}).catch((err)=>
{
  console.log("database cannot be connected");
  
});
```
- tick ✅ one change only.
- now user will add in the mangodb database by sending req to the server (postman)

## now i will a feed api
- Feed API-get/feed -get all the users from the database

### **get user by email**
```javascript
app.get("/user", async (req,res)=>{
  const userEmail=req.body.emailId;
try{
  const users= await User.find({emailId: userEmail});
  res.send(users);
}
catch(err){
  res.status(404).send("user not found");
}
})
```
- **now suppose there is no user found then what.**
- i will add condition here

```javascript
app.get("/user", async(req,res)=>{
  try{
    const users= await User.find({emailId:req.body.emailId});
    if(users.length===0)
    {
      res.status(404).send("user not found");
    }
    else{

res.send(users);
  }
    }
    
  catch(err){
    res.status(404).send("something went wrong");
  }
})

```
---
## feed API -GET/feed -`get all the users from the database`

```javascript
app.get("/feed", async(req,res)=>{
  try {
    const users= await User.find({});
    res.send(users);
    
  } catch (error) {

     res.status(404).send("something went wrong");
  }

});
```
### **now let suppose we have a same emailId then what i will do**
-  i will  use findOne();
```javascript
app.get("/user",async(req,res)=>
{
  const userEmail=req.body.emailId;
  try{
    const user=User.findOne({emailId:userEmail});
    if(!user)
    {
      res.status(404).send("user not found") 
         }
         else
         {
           res.send(user);
         }
  }
  catch(err){
    res.status(404).send("user not found");
  }

})
```
- but we should not add same email ,email should be unique

## deleting a user by its id
```javascript
app.delete("/user",async(req,res)=>
{
  const userId=req.body.userId;
  try{
    const user=await User.findByIdAndDelete({userId});
    res.send("user deleted successfully");
  }
  catch(e){
    res.status(404).send("somethig went wrong");
  }
})
```
- now postman me body ke json format me likna hai
```json
{
  "userId":"jo bhi id hogii"
}
```
# **PUT vs PATCH in HTTP Methods**

The main difference between **PATCH** and **PUT** lies in **how they update resources** on the server:

## 1. **PUT (Replace Entire Resource)**

**PUT** is used when you want to **replace the entire resource** on the server with the new data provided in the request.

- If the resource exists, it will be fully replaced with the new data.
- If the resource doesn’t exist, **PUT** can also create a new resource (depending on how the server is designed).
- **Idempotent**: Multiple **PUT** requests with the same data will result in the same resource state on the server, meaning the request can be repeated without changing the resource further.

### Example of PUT:

If you have a resource (e.g., user) at `/users/1`, and you want to update the entire resource:

```json
PUT /users/1
{
  "firstName": "John",
  "lastName": "Doe",
  "emailId": "john.doe@example.com",
  "age": 30,
  "gender": "Male"
}
```
- This request will replace all fields of the resource, including fields that aren’t provided in the request (those will be removed or set to default if missing in the body)


## 1. **PATCH (Partial Update)**
- **PATCH** PATCH is used when you want to **partially update** a resource, modifying only the specific fields sent in the request.
- **PATCH** does not replace the entire resource—only the provided fields are updated, and other fields remain unchanged.
- `Non-idempotent by default`, but can be designed to be idempotent in certain implementations.

### Example of Patch:
 if you want to update the `age` field of the user at `/users/1`:
  ```json
  PATCH /users/1
{
  "age": 31
}
  ```
## Key Differences Between PUT and PATCH

| **Feature**            | **PUT**                              | **PATCH**                            |
|------------------------|--------------------------------------|--------------------------------------|
| **Purpose**            | Replace the entire resource          | Partially update a resource          |
| **Request Body**       | Contains the full resource           | Contains only the fields to update   |
| **Effect**             | Replaces the whole resource          | Updates specific fields only         |
| **Missing Fields**     | Removes or sets missing fields to default | Unchanged if not included in the request |
| **Idempotent**         | Yes (repeating the same request has the same effect) | Not guaranteed to be idempotent unless designed that way |
| **Common Use Case**    | Full updates (e.g., replacing a document) | Partial updates (e.g., changing one or two fields) |


## When to Use

- **Use PUT** when you need to replace or create a resource with a complete new representation.
- **Use PATCH** when you only want to update specific fields of a resource without affecting the rest.

---
### update data of the user using id.
```javascript
app.patch("/user",async(req,res)=>
{
  const userId=req.body.userId;
  const data=req.body;
  try{
    await User.findByIdAndUpdate({_id:userId},data);
    res.send("user added successfully");
  }
  catch(e){
    res.status(404).send("something went wrong");
  }
})
```
## update the user with emailId 

```javascript
app.patch("/user",async(req,res)=>
  {
     const emailId=req.body.emailId;
        const data=req.body;
        try{
          await User.findOneAndUpdate({emailId},data);
          res.send("user added successfully");
        }
        catch(e){
          res.status(404).send("something went wrong");
        }
})
```
 ### in postman json file will looks like
 
 ```json
 {
    "emailId":"sardar@gmail.com",
        "firstName": "haaha",
        "lastName": "singh"
}
 ```
# EPISODE 8⬇️


### custom validator
```javascript
gender:{
  type:   String,
  validate(value)
  {
    if(!["male", "female","others"].includes(value))
    {
      throw new Error("gender data is not valid");
    }
  },

}

```
- buut it will only work   when we add the user or object ,not on the updating the user on updating the user  

- for this we need to add  runValidators:true, inside the await User.findByIdAndUpdate() method

```javascript
app.patch("/user",async(req,res)=>
  {
     
    const userId=req.body.userId;
    const data=req.body; 
    try{
      await User.findByIdAndUpdate({ _id:userId },data,{
        runValidators:true,
      });
      
      res.send("user added successfully");
    }
    catch(e){
      res.status(404).send("something went wrong" +e.message);
    }
  });
  ```
- now it will work on updating the user

### timestamps in database
- we pass it as a second parameter in a schema
 ```javascript
 const userSchema=new mongoose.Schema({
    firstName:{
        type: String,
    },
    lastName:{
        type:String
    }
    },{timestamps:true},);
     
 ```
## Api level validation

- **`API LEVEL Validation`  is a process where  data is validated when it is send through an api before being stored to database**
  -  it ensures only correct,,complete and secured data is send to db
  - it allows ony the required fields and filter out the unnecesaary fields thst are injected by any attacks.

```javascript
app.patch("/user",async(req,res)=>
  {
     
    const userId=req.body.userId;
    const data=req.body; 
    try{
      ▶️const Allowded_fields=["about","skill"]
      ▶️const isUpdated=Object.keys(data).every((k)=>
      ▶️Allowded_fields.includes(k));
      ▶️if(!isUpdated){
        throw new Error("Invalid fields to update");
      }
      await User.findByIdAndUpdate({ _id:userId },data,{
        runValidators:true,
      });
      
      res.send("user added successfully");
    }
    catch(e){
      res.status(404).send("something went wrong" +e.message);
    } 
  },
  );
  ```
  > this ▶️ indicates the changes in  code

- if there is any mismatch in the incoming data like addition of extra field it will throw an error which is catched by catch block


### validation on the skills ,it should not be  more than 10

```javascript
//it is inside the app.patch api call
 if(data?.skills.length>10){
        throw new Error("Skills should not be more than 10");
      }
```
- **NEVER TRUST THE req.body**
- we have validator.js library for validation.
- we can use it in user schema and api call 

- [for more details of validator click on this](https://www.npmjs.com/package/validator)

## Episode 9⬇️⬇️

- for validation we can create a new file where we can put our all validation and pass as a function inside the api call.

- for that we can create a new folder `utils` and  and inside it ` validation.js` file

here is validation.js code
```javascript
const validator=require("validator");
const validateSignUpData=(req)=>{
    const {firstName,lastName,emailId,password} = req.body;
    if(!firstName || !lastName)
    {
        throw new Error("name is not valid!!");
    }

    else if(firstName.length<4||firstName.length>50)
    {
        throw new Error("first name should be greater than 4 and less than 50");
        
    }

else if(!validator.isEmail(emailId))
{
    throw new Error("email is not valid");
}
else if(!validator.isStrongPassword(password)){
    throw new Error("Password is not strong enough");
}
};
module.exports={validateSignUpData  

}

```

- passing in app.post api call in try block
```javascript
try{
 validationSignUpData(req);
}
catch(e){
}
```
#### **for password hashing we will use bcrypt**

- bcrypt is a password-hashing function designed to securely hash passwords by providing resistance against brute-force attacks.

# bcrypt: Secure Password Hashing

- 
bcrypt is a tool that helps you securely store passwords. Instead of saving the actual password, it converts (or "hashes") the password into a scrambled version that is much harder for hackers to guess. Even if two people use the same password, bcrypt scrambles them in a unique way.

## How bcrypt Works:

### 1. Salting:
It adds a random string (called a "salt") to your password before converting it. This ensures that even if two people use "password123", their saved versions will look different.

### 2. Hashing:
- bcrypt turns your password into a fixed-length, scrambled code (called a "hash"). This code is what's stored, not the actual password.

### 3. Work Factor:
- bcrypt can be adjusted to take more time to scramble the password, making it harder for hackers to guess (especially with fast computers). This setting is called the "cost factor."

## Example of How to Use bcrypt in JavaScript (Node.js)

### Step 1: Install bcrypt
- To use bcrypt, you need to install it in your project:
```bash
npm install bcrypt
```
### Step 2: Hashing (scrambling) a password
 Here’s a simple code to hash a password:
```javascript
const bcrypt = require('bcrypt');

// Your password
const myPassword = "mySecurePassword";

// Salt rounds (how many times the password gets scrambled)
const saltRounds = 10; // 10 is a good starting number

// Hash the password
bcrypt.hash(myPassword, saltRounds, (err, hash) => {
  if (err) {
    console.error(err);  // In case something goes wrong
  } else {
    console.log("Hashed password:", hash);  // This scrambled version gets saved
  }
});
```
### Step 3: Verifying a Password
When someone logs in, you don’t compare the password directly. You compare the scrambled (hashed) version they enter with the one saved in the database.

```javascript
bcrypt.compare(myPassword, hash, (err, result) => {
  if (err) throw err;
  console.log("Password matches:", result);  // true if correct, false otherwise
});
```

 ```
 const user=new User(req.body)
 ```
 - ~~not good way~~

**good way**
```javascript
app.post("/signup", async (req, res) => {
  try {

    //validation of data
    validateSignUpData(req);
    ↪️const {firstName, lastName, emailId, password } = req.body;

    //encrypt the password
    ↪️const hashedPassword = await bcrypt.hash(password, 10);
    console.log(hashedPassword);
    ↪️const user = new User({ firstName, lastName, emailId, password: hashedPassword });

    console.log(user);
    await user.save();
    res.send("user added successfully");
  }
  catch (err) {
    res.status(400).send("Error : " + err.message

    );
  }
});
```
- <mark>this all changes taking place in  signup<mark>

### now use of bcrypt password in login api call

```javascript
app.post("/login",async(req, res)=>{
  try{
const {emailId, password} = req.body;
const user= await User.findOne({ emailId: emailId});
if(!user)
{
  throw new Error("email is  not present in DB");
}
const isPasswordValid=await bcrypt.compare(password, user.password);
 if(isPasswordValid)
 {
  res.send("login sucessfully");
 }
 else{
  throw new Error("password is not correct");
 }
}
  
  catch(err){
    res.status(400).send("Error : "+err.message);
  }
});
```
- but it should have bcrypt password using singup api

##  <font color="aqua">Episode 10
</font>

**HOW authentication works**

- let suppose user make a login (email,password) request  lekin wo authenticate honi chaiye then server genrates the jwt tokens
and wrap the jwt token inside the cookies  and it send back the cookies to the client 
- whenver the cookies send by the server, browser has inbuilt feature which store the cookies 
- and if again you send a api call suppose a profile api then this cookie will  go along withit 
- on every  api call run the cookie will send to the server it validates.

### cookie demo

suppose we want to add cookie on the login api inside thee  password validation

```javascript
if(isPasswordValid)
 {
   //create a jwt token

   //add the token to the cookie and send back to the user
   res.cookie("token","vbfhbvhfbghfdbghdbfguyewifhfkjadvanewjf ");
  res.send("login sucessfully");
 }
 else{
  throw new Error("password is not correct");
 }
```

- now we need to create a profile api
```javascript
app.get("/profile",(req,res)=>{
  const cookies=req.cookies;
  console.log(cookies);
  res.send("send cookies"); 
})
```

- it will send a cookies and saved but it will give undefined 
- so for this we have cookie-parser 
- we need to first install it using `npm i cookie-parser`
 -then need to import 
  `const cookieParser=require("cookie-parser");`

 then pass it as `app.use(cookieParser());`

 **Summary of Authentication Flow:**
1. **Login:** User sends credentials (e.g., username/password).
2. **Validation:** Server validates credentials.
3. **Token/Session Creation:** If valid, the server creates a token (JWT) or a session.
4. **Store Token/Session:** Token is stored on the client (e.g., localStorage or cookies).
5. **Authorized Requests:** Client sends token/session with each request to access protected resources.
6. **Verification:** Server verifies the token/session for every request.
7. **Grant/Deny Access:** If valid, user is allowed access.
This is how the basic authentication process works in most modern web applications.



**The `app.use(cookieParser())`; middleware in an Express.js application is used to parse cookies attached to the client’s request.**
- It makes it easier to work with cookies within your Express app by adding the parsed cookie data to req.cookies, which can then be accessed as part of the request object**

>📝**Note:** now it will  show token in the console

> ⚠️ **__Warning__:**  it is just dummy token for checks.


## A JWT (JSON Web Token) 
- It is a compact, self-contained token used for securely transmitting information between a client and a server. It is commonly used for authentication and authorization purposes in web applications. Here's how it works:

### **Structure of a JWT**

**`A JWT consists of three parts, separated by dots (.):`**

**1.Header**
**2.Payload**
**3.Signature**

`A typical JWT looks like this:`
```javascript
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```
1. **Header**
- The header contains metadata about the type of token and the hashing algorithm used (e.g., HMAC SHA256). It looks like this:

```json
{
  "alg": "HS256", 
  "typ": "JWT"
}
```
2. **Payload**
- The payload contains the claims, which are statements about the user and additional metadata. There are three types of claims:

- Registered claims (e.g., iss, exp, sub)
- Public claims (custom claims like name, email)
- Private claims (specific to your application)

**Example payload:**

```json

{
  "sub": "1234567890", 
  "name": "John Doe", 
  "admin": true, 
  "iat": 1516239022
}
```
3. **Signature**
- The signature is used to verify that the token has not been altered. It is created by taking the encoded header, encoded payload, a secret key, and signing them using the specified algorithm.

#### Example signature creation (with HMAC SHA256):

```scss
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload), 
  secret_key
)
```
# how to create a jwt token
```javascript
app.post("/login",async(req, res)=>{
  try{
const {emailId, password} = req.body;
const user= await User.findOne({ emailId: emailId});
if(!user)
{
  throw new Error("email is  not present in DB");
}
const isPasswordValid=await bcrypt.compare(password, user.password);
 if(isPasswordValid)
 {
   //create a jwt token
   const token=await jwt.sign({_id:user._id},secretkey)

   //add the token to the cookie and send back to the user
   res.cookie("token",token);
  res.send("login sucessfully");
 }
 else{
  throw new Error("password is not correct");
 }
}
  
  catch(err){
    res.status(400).send("Error : "+err.message);
  }
});

```
## how to verify jwt token on subsequent requests

- now i want to see the data after login  in profile

- read the cookie inside the profile Api and find the logged in user

```javascript
app.get("/profile",async(req,res)=>{
  try {
    const cookies=req.cookies;

    const {token}=cookies
   
    if (!token)
    {
      throw new Error("Invalid token"); 
    }
    //validate the token
    const decodeMessage=await jwt.verify(token,"AbcdDE@123");
  
  const {_id}=decodeMessage;
  const user=await User.findById(_id); 
  if(!user)
  {
    throw new Error("user not found");
    
  }
    res.send(user);
    
  } catch (error) { 
  } 
})
```
**⚠️: post login api will same**

## adding middleware of UserAuth

- changes in the ./middlewares/auth.js

```javascript
const jwt=require("jsonwebtoken");
const User=require("../models/user");
 const UserAuth=async(req,res,next)=>{
  try{
    const {token}=req.cookies;
    if (!token)
      {
        throw new Error("Invalid token"); 
      }
    const decodeMessage=await jwt.verify(token,"AbcdDE@123");
    const {_id}=decodeMessage;
    const user=await User.findById(_id);
    if(!user)
    { 
      throw new Error("User not found");
    }
     
    req.user=user;  
    next(); 
  }
  catch(err){
    res.status(401).send({message:"err.message"});
  }
 }
module.exports={
  UserAuth,
}

```
- now if we want to use it we can directly put it in the api call.
```javascript

app.get("/profile",UserAuth,async(req,res)=>{
  try {
  const user=req.user;
  res.send(user);
  
  } catch (error) {
    res.status(400).send("error:"+error.message);
    
  } 
});
```
- `UserAuth is main character here`

## how to set an expiry time for the jwt token
- we can set an expiry time for the jwt token when creating it by passing a  `"expiresIn"`
property as a 3rd argument to the "sign" function

**EXAMPLE**
```javascript
const token=await jwt.sign(data,secretKey,{expiresIn:"7d"})
```
- in code
```javascript
const token=await jwt.sign({_id:user._id},"AbcdDE@123",{expiresIn:"7d"})
```
## how to set a expiry time for the cookies

- similiary we can pass the expiry time for the cookie when  creating it passing expires prop as  3rd argument in the `res.cookies()`

```javascript
 res.cookie("token",token,{expires: new Date(Date.now()+8*3600000)});
  ```

   ### **what are Schema methods?** 

   - These are methods that are directly attached to schema and are available for all the document that are created based on that schema.
   -  → Arrow fn  is not allowed for schema methods as "this" Keyword is `"undefined"`

 **this is userSchema.methods**
 ```javascript
    userSchema.methods.getJWT=async function(){
        const user =this;
        const token=await jwt.sign({_id:user._id},"AbcdDE@123",{expiresIn:"7d",});

        return token; 
    };
  ```

- here to use it
 **before**

 ```javascript
  const token=await jwt.sign({_id:user._id},"AbcdDE@123",{expiresIn:"7d"})
  ```

  **after**
  ```javascript
  const token=await user.getJWT();
  ```

  - same for the validatePassword
   
   ```javascript
     userSchema.methods.validatePassword = async function(password){
        const user =this;
        const isPasswordValid=await bcrypt.compare(password, this.password);
        return isPasswordValid;
     }
   ```
**before**
```javascript
const isPasswordValid=await bcrypt.compare(password, user.password);
```
**after**
```javascript
const isPasswordValid=await user.validatePassword(password);
```
```
app.patch("/user", async (req, res) => {

    const userId = req.body.userId;
    const data = req.body;
    try {
      const Allowded_fields = ["about", "skills", "age", "gender", "userId"]
      const isUpdated = Object.keys(data).every((k) =>
        Allowded_fields.includes(k));
      if (!isUpdated) {
        throw new Error("Invalid fields to update");
      }
  
      if (data?.skills.length > 10) {
        throw new Error("Skills should not be more than 10");
      }
      await User.findByIdAndUpdate({ _id: userId }, data, {
        runValidators: true,
      });
  
      res.send("user added successfully");
    }
    catch (e) {
      res.status(404).send("something went wrong" + e.message);
    }
  
  },
  );
  ```
```
app.get("/user", async (req, res) => {

  // const userEmail=req.body.emailId;
  // try{
  //   const user= await User.find({emailId:userEmail});
  //   res.send(user);

  // }
  // catch(err){
  //   res.status(404).send("something went wrong");
  // }
  const userEmail = req.body.emailId;
  try {
    const users = await User.find({ emailId: userEmail });
    if (users.length === 0) {

      res.status(404).send("user not found haha");  //to stop further execution of the code block.  It will not reach the catch block.  It's a way to avoid unhandled promise rejection error.
    }
    else {
      res.send(users);
    }

  }
  catch (err) {
    res.status(404).send("user not found");
  }


});

app.delete("/user", async (req, res) => {
  const userId = req.body.userId;
  try {
    const user = await User.findByIdAndDelete(userId);
    res.send("user deleted successfully");
  }
  catch (e) {
    res.status(404).send("somethig went wrong");
  }
});
//feed API -GET/feed -get all the users from the database
app.get("/feed", async (req, res) => {
  try {
    const users = await User.find({});
    res.send(users);

  } catch (err) {

    res.status(404).send("something went wrong");
  }
});
```

## what is express Router
- express router is a way of organising  and managing routes of our application

## why to use express.Router??

- when the application grows and we have lot api to call   then to manage all the routes in a single file becomes hard  so router which have same routes are grouped together in  a seperate file.
- it makes our code clean and maintaiable


## how to create a  express router??

we can create a router using  express.Router() function and attach  the router to it.

- **we have created a routes folder> then in which we have three folder 1.auth.js 2.profile.js 3.request.js**

- in auth.js we  have given the authentication part like login and singup

```javascript
 const express=require('express');
 //this is how we import express.Router()
 const authRouter=express.Router();
 const { validateSignUpData } = require("../utils/validation");
 const bcrypt = require("bcrypt");
 const User = require("../models/user");

//here we need to write name of router jaise yha authRouter hai (peheley app.post) likhte the
authRouter.post("/signup", async (req, res) => {
  try {

    //validation of data
    validateSignUpData(req);
    const {firstName, lastName, emailId, password } = req.body;

    //encrypt the password
    const hashedPassword = await bcrypt.hash(password, 10);
    console.log(hashedPassword);
    const user = new User({ firstName, lastName, emailId, password: hashedPassword });

    console.log(user);
    await user.save();
    res.send("user added successfully");
  }
  catch (err) {
    res.status(400).send("Error : " + err.message

    );
  }

});
authRouter.post("/login",async(req, res)=>{
  try{
const {emailId, password} = req.body;
const user= await User.findOne({ emailId: emailId});
if(!user)
{  
  throw new Error("email is  not present in DB");
}
const isPasswordValid=await user.validatePassword(password );
 if(isPasswordValid) 
 {
   //create a jwt token
   const token=await user.getJWT();

   //add the token to the cookie and send back to the user
   res.cookie("token",token,{expires:new Date(Date.now()+8*3600000)});
  res.send("login sucessfully");
 }
 else{
  throw new Error("password is not correct");
 }
}
  catch(err){
    res.status(400).send("Error : "+err.message);
  }
});

module.exports=authRouter;

```

- **in app.js the changes we like**
we need to just import
```javascript
const authRouter=require("./routes/auth");
app.use("/",authRouter);
```
we just connect the router just like we connect with middleware using `app.use`
- first it will see the slash then it go to the router which is called
- suppose hmme profile api call kri then sbse pehle slash me jayega fir wo sab me check krega ki ki profile kisme hai then milne ke baad uska result de degaa

- for more details click on this [link](https://expressjs.com/en/5x/api.html)

## Logic for Logic Api
```javascript
authRouter.post("/logout", async(req,res)=>{
    try {
        res.cookie("token",null,{expires:new Date(Date.now())});
        res.send("logout sucessfully");
        
    } catch (error) {
        res.status(401).send("Error: " + error.message);
        
    }
})
```
- added validationEditProfileData in validation.js
```javascript
const validateEditProfileData=(req)=>{

    const allowdedEditFields=
    ["firstName", 
        "LastName",
        "emailId",
        "photoURL",
        "gender",
        "about",
        "skills"];
const isEditAllowed=Object.keys(req.body).every((field)=>
    allowdedEditFields.includes(field)
);

return isEditAllowed;
};
```
- **now changes in profile.js which is edit**
```javascript
const express = require("express");
const {UserAuth}=require("../middlewares/auth");
const { validateEditProfileData } = require("../utils/validation");
const profileRouter=express.Router();

profileRouter.get("/profile/view",UserAuth,async(req,res)=>{
    try {
    const user=req.user;
    res.send(user);
    
    } catch (error) {
      res.status(400).send("error:" +error.message);
      
    } 
  });
  profileRouter.patch("/profile/edit", UserAuth ,async(req, res) => {

    try {
      if(!validateEditProfileData(req))
        {
        throw new Error("invalid edit request");
      }
      const loggedInUser=req.user;
      Object.keys(req.body).forEach((key)=>(loggedInUser[key]=req.body[key]));
       await loggedInUser.save();
  
       res.json({
        message: `${loggedInUser.firstName}, your profile updated successfuly`,
        data: loggedInUser,
    });
  } 
    catch (e) {
      res.status(400).send("something went wrong :" + e.message);
    }
  },
  );

  module.exports=profileRouter;
  ```

  - now we need to create a schema for a connectionRequest in which we have fromUserId and toUserId

### how do you reference the ObjectId in schema??
- the type of objectId is `"mongoose.Schema.Types.ObjectId.`

- ye _id se check krega user ko kisko req bheji kisko swap kra

```javascript
const mongoose = require('mongoose');
const connectionRequestSchema=new mongoose.Schema(
    {
        fromUserId:{
            type: mongoose.Schema.Types.ObjectId,
           reqired: true,
        },
        toUserId:{
            type: mongoose.Schema.Types.ObjectId,
            reqired: true,
        },
        status:{
            type:String,
            enum:{
                values:["ignore","interested","accepted","rejected"],
                message: `{VALUE} is incorrect status type`,
            },
        },
    },
    {
        timestamps:true, 
    }
);

const connectionRequest=new mongoose.model("ConnectionRequest", connectionRequestSchema);

module.exports=connectionRequest;

```

- routes>request.js will look like this.


**1.Importing the necessary modules**
```javascript
const express = require('express');
const { UserAuth } = require('../middlewares/auth');
const requestRouter = express.Router();
const ConnectionRequest = require("../models/connectionRequest");
const User = require("../models/user"); // Assuming this is the user model
```
- `express.Router():` Creates a new router instance for handling requests specific to connection requests.
- ` UserAuth:` Middleware to protect the route and ensure only authenticated users can make a connection request.
- `ConnectionRequest:` Mongoose model representing the connection request entity.
- `User:` A reference to the User model (assuming it is defined elsewhere), which will be used to check if the recipient user exists.

2. #### Defining the POST Route
```js
requestRouter.post("/request/send/:status/:toUserId", UserAuth, async (req, res) => {
  ```
- `/request/send/:status/:toUserId`: This is the route's endpoint. It's dynamic due to the :status and :toUserId parameters.
`:status:` Status of the connection request, which can be either "ignored" or "interested".
`:toUserId:` The ID of the user to whom the connection request is being sent.
- `UserAuth middleware:` Ensures the user is authenticated before processing the request.

3. #### Extracting Key Parameters
```js
const fromUserId = req.user._id;
const toUserId = req.params.toUserId;
const status = req.params.status;
```

- **fromUserId:** Extracted from req.user, which is set by the UserAuth middleware. This represents the ID of the user sending the connection request.

- **toUserId:**  Extracted from the route parameter (req.params.toUserId). This is the ID of the user who will receive the connection request.

- **status:** The status of the connection request, also extracted from the route parameter.

4. ### Validating the Status
```js

const allowedStatus = ["ignored", "interested"];
if (!allowedStatus.includes(status)) {
  const toUser = await User.findById(toUserId);
  if (!toUser) {
    return res.status(400).json({ message: "User not found" });
  }
}
```
- **allowedStatus:** This is an array containing the only valid statuses: "ignored" and "interested".
- **Validation:** The code checks whether the provided status is one of the allowed values.
- If the status is invalid, the code proceeds to verify if the recipient user (toUser) exists in the database using User.findById(toUserId).
- If the recipient user does not exist, it returns a 400 Bad Request response with the message "User not found".
**Key Reason for This Check:**
The status validation ensures that the user is sending a valid type of connection request (either ignoring or showing interest). Additionally, it ensures that the recipient user exists in the system before proceeding, which prevents sending connection requests to non-existent users.

5. #### Checking for Existing Connection Requests
```js
const existingConnectionRequest = await ConnectionRequest.findOne({
  $or: [
    { fromUserId, toUserId },
    { fromUserId: toUserId, toUserId: fromUserId }
  ]
});

if (existingConnectionRequest) {
  return res.status(400).json({ message: "Request already sent" });
}
```
- **findOne()**: This query searches the database for any existing connection requests between the fromUserId and toUserId:

- **Case 1:** If the current user (fromUserId) has already sent a request to toUserId.
- **Case-2:** If the current user (fromUserId) has already received a request from toUserId (i.e., a reversed relationship).

- If an existing connection request is found in either direction, a 400 Bad Request response is returned with the message "Request already sent".
**Key Reason for This Check:**
This prevents duplicate connection requests between the same two users. It ensures that users cannot keep spamming or sending multiple connection requests to the same person.

**Condition 1:** { fromUserId, toUserId }
This shorthand { fromUserId, toUserId } is equivalent to writing:
```js
{ fromUserId: fromUserId, toUserId: toUserId }
```
- This checks if there is a connection request where the fromUserId is equal to the fromUserId of the current user and the toUserId is equal to the toUserId of the other user.
- In other words, this condition checks if you (the current user) have sent a connection request to the other user.
**Condition 2:** `{ fromUserId: toUserId, toUserId: fromUserId }`

- This condition reverses the fields. 
- It checks if there is a connection request where the fromUserId is the toUserId of the current user and the toUserId is the fromUserId of the current user.
- In other words, this condition checks if the other user has already sent a connection request to you (the current user).

6. #### Creating and Saving the Connection Request
```js

const connectionRequest = new ConnectionRequest({
  fromUserId,
  toUserId,
  status
});
```
- **new ConnectionRequest({...}):** Creates a new instance of the ConnectionRequest model, which represents a new connection request. The request contains the following fields:
**fromUserId:** The ID of the user sending the request.
**toUserId:** The ID of the recipient.
**status:** The status of the request ("ignored" or "interested").

7. #### Saving the Request to the Database
```js
const data = await connectionRequest.save();
```
- **save():** The save() method is called on the newly created ConnectionRequest instance, which writes the request to the database.

8. #### Sending the Success Response
```js
res.json({
  message: "Connection request sent successfully",
  data
});
```
- If the request is successfully saved, a JSON response is sent back to the client with a success message and the saved data (i.e., the connection request details).
9. #### Error Handling
```js
} catch (error) {
  res.status(400).send("Error: " + error.message);
}
```
- **try-catch block:** The entire operation is wrapped in a try-catch block to handle any potential errors.

- If an error occurs at any point in the process (e.g., database errors, invalid parameters), the server responds with a 400 Bad Request status and the error message.


### Summary of Key Steps
**Authentication:** The user must be authenticated to send a connection request (`UserAuth`middleware).
**Validation:** The status of the request must be either "ignored" or "interested".
Check User Existence: If the status is invalid, the system verifies whether the recipient user (toUser) exists.
**Prevent Duplicate Requests:** The code checks if a request already exists between the two users, either in the sent or received direction.
**Create and Save:** If all checks pass, a new connection request is created and saved in the database.
**Error Handling:** The code handles errors, ensuring that meaningful error messages are sent to the client.



### One more validation of if a user sending connection request to himself

- i can do it request.js but now i am doing it on schema of connectionRequest.js(that is model)
```js
connectionRequestSchema.pre("save", function(next){
    const connectionRequest=this;
    //check if fromUserId is same as toUserId
    if(connectionRequest.fromUserId.equals(connectionRequest.toUserId))
    {
        throw new Error("cannot send connection request to yourself");
    }
    next();
});
```
- the code  provided is a Mongoose middleware function that is executed before a ConnectionRequest document is saved to the database. This function ensures that users cannot send connection requests to themselves

- connectionRequestSchema.pre('save', ...): This is a Mongoose `pre-save hook.`

- The **pre-save** hook is middleware that runs before the save() method is executed on a Mongoose model (in this case, ConnectionRequest).
- This means that the function will be triggered every time a ConnectionRequest is about to be saved to the database, ensuring that additional logic (like validation) is executed.

**next()** is called if the validation passes (i.e., fromUserId is not equal to toUserId).

- This function moves on to the next middleware in the Mongoose lifecycle (or proceeds to save the document if no further middleware is defined).

- If next() is not called, the save operation will not complete, so it's crucial to invoke it if everything is correct.

**schema.index()** to define indexes on your schema. This method allows you to create indexes programmatically when defining a schema, instead of manually using the createIndex() method on the collection.

#### Example of schema.index()

Here’s how you can use schema.index() to define an index on a Mongoose schema:

```js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  username: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  age: { type: Number }
});

// Create an index on the 'username' field in ascending order
userSchema.index({ username: 1 });

// Create a compound index on 'username' (ascending) and 'age' (descending)
userSchema.index({ username: 1, age: -1 });

// Define a unique index on 'email'
userSchema.index({ email: 1 }, { unique: true });

const User = mongoose.model('User', userSchema);
```
#### Key Points:
**schema.index():** This is used to create indexes directly on the schema level.

**Compound Index:** You can create an index on multiple fields by passing multiple field names to schema.index().

**Options:** You can pass additional options, such as
` { unique: true }`, to enforce uniqueness or other constraints.

#### Why use schema.index()?

**Automated:** Mongoose will automatically create these indexes when the application connects to the database.

**Schema-Level Indexing:** It keeps index definitions close to the schema definition, improving readability and maintainability.
Example of Compound Index with Unique Constraint:
```js
userSchema.index({ username: 1, email: 1 }, { unique: true });
```
- This compound index ensures that both username and email are unique together, meaning the same combination cannot appear twice in the collection.

#### When to Use schema.index():

**Performance**: Use indexing to optimize query performance, especially for frequently queried fields.

**Constraints:** Use unique indexes to enforce constraints on fields like email or username.

### The $and Operator
- The $and operator is used to match documents that satisfy all of the conditions specified. It requires that all the conditions be true for a document to be included in the result set.

**Syntax:**
```js
{ $and: [ { condition1 }, { condition2 }, ... ] }
```
**Example:**
- Let’s say you want to find users whose age is greater than 25 and whose status is "active":

```js
db.users.find({
  $and: [
    { age: { $gt: 25 } },
    { status: "active" }
  ]
});
```
**In this query:**

- It will return users who are both older than 25 and have the status "active".

**How $and works:**

- All conditions must be true for the document to be returned.
- Useful when you need multiple conditions to be satisfied simultaneously.

### The $or Operator
- The $or operator is used to match documents that satisfy at least one of the conditions specified. It takes an array of conditions, and if any of them are true, MongoDB returns the document.

**Syntax:**

```js
{ $or: [ { condition1 }, { condition2 }, ... ] }
```
**Example:**

- Suppose you have a users collection and you want to find users who either have the name "Alice" or the age is greater than 25:

```js
db.users.find({
  $or: [
    { name: "Alice" },
    { age: { $gt: 25 } }
  ]
});
```
**In this query:**

- It will return users whose name is "Alice" or whose age is greater than 25.

**How `$or` works:**

- If either condition is true, the document is included in the result set.

- It's useful when you want to search for documents that match one condition or another.



## EPISODE 13

**Write code with proper validation for` POST /request/review/:status/:requestId`.**
```javascript
requestRouter.post("/request/review/:status/:requestId",UserAuth,async(req,res)=>
{
    try{
        const  loggedInUser=req.user;
        const {status, requestId}=req.params;
        const allowdedStatus=["accepted","rejected"];
        if(!allowdedStatus.includes(status))
        {
            return res.status(400).json({message:"status is not allowded"});
        }
        const connectionRequest=await ConnectionRequest.findOne(
            {
                _id:requestId,
                toUserId:loggedInUser._id,
                status:"interested",
            }
        ); 
        if(!connectionRequest) 
        {
            return res.status(404).json({message:"Request not found"});
        }
connectionRequest.status=status;
const data = await connectionRequest.save();
  res.json({message:"Connection Request " +status,data});

    }
    catch(err){
        res.status(400).send("Error: "+err.message);
    }
})
```

**Route Setup:** The requestRouter.post method sets up a POST route at /request/review/:status/:requestId. This route expects two URL parameters: status (indicating the action) and requestId (the ID of the connection request). It also applies a UserAuth middleware to ensure only authenticated users can access this route.

**Parameters and Authentication:** Inside the route, it retrieves loggedInUser from req.user (set by the UserAuth middleware) and extracts the status and requestId from req.params.

**Status Validation:** It defines an array allowedStatus with acceptable statuses ("accepted" and "rejected"). If status is not in allowedStatus, it responds with a 400 error and a message indicating the status is invalid.

**Database Query:** The route attempts to find a connection request in the database using ConnectionRequest.findOne that matches:

- _id matching requestId
- toUserId matching the logged-in user's ID
- status being "interested"
- If no such request is found, it returns a 404 error with the message "Request not found."

**Status Update:** If a matching connection request is found, the code updates its status to the provided value (either "accepted" or "rejected"), saves the update, and returns a success message with the updated request data.

**Error Handling:** If any errors occur during the process, the catch block sends a 400 error with the error message.

**Purpose:** This route allows users to accept or reject incoming connection requests based on the specified status.

- **now i moving to the user level authentication**
```javascript
const express=require('express');
const userRouter=express.Router();
const {UserAuth}=require('../middlewares/auth');
const ConnectionRequest=require("../models/connectionRequest");

userRouter.get("/user/requests/received",UserAuth,async(req,res)=>
{
    try {
        const loggedInUser=req.user;
        const connectionRequest = await ConnectionRequest.find({
            toUserId: loggedInUser._id,
            status:"interested",
        }).populate("fromUserId",["firstName","lastName"]);

        res.json({
            message: "data fetched successfully",
            data: connectionRequest,
        });
    } catch (error) {
        res.status(400).send("error:" +error.message);
        
    }
});

userRouter.get("/user/connections",UserAuth,async(req,res)=>
{
    try{
        const loggedInUser=req.user;
        const connectionRequest = await ConnectionRequest.find({
            $or: [
                {fromUserId: loggedInUser._id,status: 'accepted'},
                {toUserId: loggedInUser._id,status: 'accepted'},
            ],
        }).populate("fromUserId",["firstName","lastName"]).populate("toUserId",["firstName","lastName"]);

      const data=connectionRequest.map((row)=>
      {
        if(row.fromUserId._id.toString() === loggedInUser._id.toString())
        {
            return row.toUserId;
        }
        return row.fromUserId
    });
        res.json({
            data
        });
    }
    catch(err){
        res.status(400).send("Error : "+err.message);
    }
})


module.exports =userRouter;
```
### 1.First Route: /user/requests/received
- This route handles GET requests to fetch the connection requests that the logged-in user has received from other users, but only those that have a status of "interested."

**Code Breakdown:**
```js
userRouter.get("/user/requests/received", UserAuth, async (req, res) => {
    try {
        const loggedInUser = req.user;
        const connectionRequest = await ConnectionRequest.find({
            toUserId: loggedInUser._id,
            status: "interested",
        }).populate("fromUserId", ["firstName", "lastName"]);

        res.json({
            message: "data fetched successfully",
            data: connectionRequest,
        });
    } catch (error) {
        res.status(400).send("error:" + error.message);
    }
});
```
**Key Parts:**

`UserAuth middleware:` This middleware ensures that the user making the request is authenticated (i.e., logged in). If the user is not authenticated, the request will be rejected.

`loggedInUser = req.user:` This retrieves the currently logged-in user's information from the request object, which is typically set by the UserAuth middleware.

`ConnectionRequest.find()`: This query searches the ConnectionRequest collection for connection requests that meet two conditions:

- The toUserId matches the logged-in user's _id, meaning this user is the recipient of the request.

- The status is **"interested"**, meaning the request is currently in a state where the other user is interested in connecting.

- `populate("fromUserId", ["firstName", "lastName"])`: This is an important feature of Mongoose called "population."
- The fromUserId is a reference to another collection (likely the User collection).
- The populate function replaces the fromUserId (which is just an ID) with the actual user data. In this case, it populates only the firstName and lastName fields of the user who sent the request.

#### What Happens Without the populate:

>Without the populate function, the response will contain just the fromUserId field as an ObjectId. The client would not get the sender’s first name and last name unless an additional query is made.

### 2. Second Route: `/user/connections`
This route handles GET requests to fetch all the accepted connection requests involving the logged-in user, whether the user sent or received the request.

**Code Breakdown:**
```js
userRouter.get("/user/connections", UserAuth, async (req, res) => {
    try {
        const loggedInUser = req.user;
        const connectionRequest = await ConnectionRequest.find({
            $or: [
                { fromUserId: loggedInUser._id, status: 'accepted' },
                { toUserId: loggedInUser._id, status: 'accepted' },
            ],
        })
        .populate("fromUserId", ["firstName", "lastName"])
        .populate("toUserId", ["firstName", "lastName"]);

        const data = connectionRequest.map((row) => {
            if (row.fromUserId._id.toString() === loggedInUser._id.toString()) {
                return row.toUserId;
            }
            return row.fromUserId;
        });

        res.json({ data });
    } catch (err) {
        res.status(400).send("Error: " + err.message);
    }
});
```
**Key Parts:**

`UserAuth middleware:` Again, this ensures that only authenticated users can access this route.

`ConnectionRequest.find():` 
- This query searches the ConnectionRequest collection for any connection requests that involve the logged-in user. 
- It uses the $or operator to check two possible scenarios:

- The logged-in user is the fromUserId (i.e., they sent the connection request), and the status is "accepted".
- The logged-in user is the toUserId (i.e., they received the connection request), and the status is "accepted".
- This ensures that the user can see all accepted connections, regardless of who initiated the connection.

**`populate("fromUserId", ["firstName", "lastName"])`** and populate
**`("toUserId", ["firstName", "lastName"])`**: 
- This populates both the fromUserId and toUserId with the respective users' first and last names, instead of just returning their ObjectId.

### Mapping the connections:

```javascript
const data = connectionRequest.map((row) => {
    if (row.fromUserId._id.toString() === loggedInUser._id.toString()) {
        return row.toUserId;
    }
    return row.fromUserId;
});
```
- This code checks whether the logged-in user was the sender (fromUserId) or the recipient (toUserId) of the connection request.

- If the logged-in user sent the request (fromUserId is equal to loggedInUser._id), then the code returns the toUserId (the other user in the connection).

- If the logged-in user received the request (toUserId is equal to loggedInUser._id), then it returns the fromUserId (the other user in the connection).

- This ensures that the response contains only the other user (the one the logged-in user is connected to), not the logged-in user's own information.

### Why the if condition is needed:
- Without the if condition inside the .map() function, the response would return both the sender and the recipient in each connection request. `However, the purpose of this endpoint is to show the other user in the connection, not the logged-in user.`

- The condition ensures that only the other user involved in the connection request is returned.

### What is ref in Mongoose?

- In Mongoose, the ref option is used to specify which model (collection) the referenced field is related to. 
- This is helpful in scenarios where `documents in one collection need to reference documents in another collection,` creating a relationship between them.

- When you define a field with a ref in a Mongoose schema, that field stores an ObjectId that references a document in another collection. Later, you can use Mongoose's populate() method to retrieve the actual documents associated with those ObjectIds.

### Example of ref in Mongoose

- Let’s say you have two collections: User and Post. Each post is created by a user, so the Post collection should reference the User collection. This is how you would set up the relationship using ref:


**User Schema:**
```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const userSchema = new Schema({
    firstName: String,
    lastName: String,
    email: String,
});

const User = mongoose.model('User', userSchema);
module.exports = User;
```

### Post Schema (with ref):
```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const postSchema = new Schema({
    title: String,
    body: String,
    author: {
        type: Schema.Types.ObjectId,  // Reference to another document's ObjectId
        ref: 'User',  // Model that this ObjectId refers to
    },
});

const Post = mongoose.model('Post', postSchema);
module.exports = Post;
```
**In this example**:

- The author field in the Post schema is of type Schema.Types.ObjectId. This is the ID of a document in the User collection.

- **The ref:** 'User' tells Mongoose that the author field refers to the User model, meaning the value of author is an ObjectId that points to a User document.

### How to Use populate() with ref

- When you query the Post model, the author field will initially contain only the ObjectId of the user who created the post. However, you can use the populate() method to automatically fetch and include the entire user document related to that ObjectId.

**Query Example with populate()**
```js
const Post = require('./models/Post');

async function getPostWithAuthor(postId) {
    const post = await Post.findById(postId).populate('author', 'firstName lastName');
    console.log(post);
}

getPostWithAuthor('634a2fbf509c710128d9fb28');
```
**In this query:**

**populate('author'):**
 - This tells Mongoose to replace the author field (which contains an ObjectId) with the actual User document it refers to.

- 'firstName lastName': This specifies which fields from the User model you want to include in the result.

 **Result without populate()**

- Without populate(), the result of the query would look like this:

```json
{
    "_id": "634a2fbf509c710128d9fb28",
    "title": "My First Post",
    "body": "This is the body of my post",
    "author": "634a2f9e509c710128d9fb27"  // Only ObjectId, not user details
}
```

**Result with populate()**

- With populate(), the author field will contain the full user data:
```json
{
    "_id": "634a2fbf509c710128d9fb28",
    "title": "My First Post",
    "body": "This is the body of my post",
    "author": {
        "_id": "634a2f9e509c710128d9fb27",
        "firstName": "John",
        "lastName": "Doe"
    }
}
```


## EPISODE 14

- user should see all the user card except-
1. his own card
2. his connections
3. ignored people
4. already sent the  connection request.

## Select and Populate

- In Mongoose, both select and populate are used in queries, but they serve different purposes. Let's explore their differences in detail:

### 1. select() in Mongoose
- The select() method is used to specify which fields of a document you want to include or exclude when querying the database. This allows you to return only the data you need, improving query performance and reducing data transfer.

**Syntax:**
```js
Model.find().select('field1 field2');  // Include specific fields
Model.find().select('-field1 -field2');  // Exclude specific fields
```
**Including fields:** You provide a space-separated string of field names you want to return.

**Excluding fields:** You use the minus (-) sign before the field names to exclude them.
**Example:**
```js
const User = require('./models/User');

// Fetch only firstName and lastName from the User model
const users = await User.find().select('firstName lastName');
```

- In this query, only the firstName and lastName fields are returned for each user document. All other fields like email, _id, etc., will be excluded.

**Use case for select:**

- Use select when you only need specific fields from a document and want to optimize your query by excluding unwanted data.

### 2. populate() in Mongoose

- The populate() method is used to fetch referenced documents from other collections. 
- It replaces the ObjectId stored in a field (which references another document) with the actual document it points to.

#### Syntax:
```js
Model.find().populate('field');  // Populate all fields in the reference
Model.find().populate('field', 'specificField1 specificField2');  // Populate specific fields in the reference
```
**Basic populate:** Replaces the ObjectId with the entire referenced document.

**Partial populate**: You can populate only specific fields of the referenced document.

**Example:**

- Let’s say we have two models: User and Post. In the Post model, there is an author field that stores an ObjectId referencing a user.

```js
const Post = require('./models/Post');
// Populate the author field with the full User document
const posts = await Post.find().populate('author');
```
- In this query, the author field, which initially contains an ObjectId, will be replaced with the full User document.

**Use case for populate:**

- Use populate when you want to fetch related documents from other collections based on the ObjectId reference.

| **Aspect**               | **select()**                                                | **populate()**                                                |
|--------------------------|-------------------------------------------------------------|---------------------------------------------------------------|
| **Purpose**               | Choose specific fields to include/exclude from a document.  | Fetch related documents based on ObjectId references.          |
| **Usage**                 | Used to optimize performance by returning only the fields you need. | Used to replace ObjectIds with full documents from other collections. |
| **Scope**                 | Affects only the fields of the document being queried.      | Works on fields that reference other collections.              |
| **Effect**                | Includes or excludes fields from the original document.     | Replaces referenced ObjectIds with actual documents.           |
| **Example**               | `.select('firstName lastName')` to get specific fields from the document. | `.populate('author')` to retrieve full documents for referenced author field. |
| **Performance Impact**    | Reduces the amount of data returned, improving query performance. | Requires an additional query to fetch referenced documents, which can impact performance. |

| **Aspect**               | **select()**                                                | **populate()**                                                |
|--------------------------|-------------------------------------------------------------|---------------------------------------------------------------|
| **Purpose**               | Choose specific fields to include/exclude from a document.  | Fetch related documents based on ObjectId references.          |
| **Usage**                 | Used to optimize performance by returning only the fields you need. | Used to replace ObjectIds with full documents from other collections. |
| **Scope**                 | Affects only the fields of the document being queried.      | Works on fields that reference other collections.              |
| **Effect**                | Includes or excludes fields from the original document.     | Replaces referenced ObjectIds with actual documents.           |
| **Example**               | `.select('firstName lastName')` to get specific fields from the document. | `.populate('author')` to retrieve full documents for referenced author field. |
| **Performance Impact**    | Reduces the amount of data returned, improving query performance. | Requires an additional query to fetch referenced documents, which can impact performance. |

### Example Using Both select and populate
```js
const Post = require('./models/Post');

// Find posts, populate the author field, but only return specific fields
const posts = await Post.find()
  .populate('author', 'firstName lastName')  // Populate only specific fields of the author
  .select('title body');  // Select specific fields of the Post model
  ```
**In this query:**

- `populate('author', 'firstName lastName'):` Populates only the firstName and lastName fields of the author document.
- `select('title body'):` Returns only the title and body fields of the Post document.

| **Operator** | **Description**                                                                    | **Use case**                                       | **Example**                                                    |
|--------------|------------------------------------------------------------------------------------|---------------------------------------------------|----------------------------------------------------------------|
| **$nin**     | Matches documents where the field’s value is not in a specified array of values.    | Use when you want to exclude multiple values.      | `{ category: { $nin: ["Electronics", "Furniture"] } }`          |
| **$ne**      | Matches documents where the field’s value is not equal to a single specified value. | Use when you want to exclude one specific value.   | `{ role: { $ne: "admin" } }`                                   |



 **Code Breakdown of**⬇️

### userRouter.get("/feed",UserAuth,async(req,res)):

- This sets up a route /feed with UserAuth middleware to ensure the user is authenticated. Once authenticated, the async function runs.

**Extracting Logged-in User:**

```js
const loggedInUser = req.user;
```

- The req.user contains the authenticated user object. This user’s ID is used to filter connections and determine which users should not appear in the feed.

### Pagination Logic:

```js
const page = parseInt(req.query.page) || 1;
let limit = parseInt(req.query.limit) || 10;
limit = limit > 50 ? 50 : limit;
const skip = (page - 1) * limit;
```
- Pagination is implemented here. page and limit are query parameters from the request.
- If page or limit are not provided, default values are used (page = 1 and limit = 10).
- The limit is capped at 50 to prevent too much data from being loaded at once.
- skip determines how many documents should be skipped based on the page number. It helps in returning the correct page.

**Fetching Connection Requests:**

```js
const connectionRequest = await ConnectionRequest.find({
    $or: [
        { fromUserId: loggedInUser._id },
        { toUserId: loggedInUser._id },
    ], 
}).select("fromUserId toUserId");
```
- It fetches connection requests related to the logged-in user (either sent by or received by the user).

- The select method limits the returned fields to fromUserId and toUserId to reduce unnecessary data fetching.

**Building the hideUsersFromFeed Set:**

```js
const hideUsersFromFeed = new Set();
connectionRequest.forEach((req) => {
    hideUsersFromFeed.add(req.fromUserId.toString());
    hideUsersFromFeed.add(req.toUserId.toString());
});
```
- A Set is used to collect IDs of users involved in the connection requests. 
- This ensures that these users will be hidden from the feed since they are already connected or pending connection.

**Filtering Users for the Feed:**

```js
const users = await User.find({
    $and: [
        { _id: { $nin: Array.from(hideUsersFromFeed) } },
        { _id: { $ne: loggedInUser._id } },
    ],
}).select("firstName" "lastName")
.skip(skip)
.limit(limit);
```
>
The User.find query fetches users who:

- Are not in the hideUsersFromFeed set ($nin operator).
- Are not the logged-in user ($ne: loggedInUser._id).
- select is used to fetch only firstName and lastName.
- Pagination is applied using skip and limit.

**Response:**

```js
Copy code
res.json({ data: users });
```
`The filtered user list is sent back as a JSON response.`
Error Handling:

```js
catch (error) {
    res.status(400).send("error:" + error.message);
}
```
- If any error occurs, a 400 status code is returned with the error message.

**Why This Logic is Used**

**Pagination:** Without it, the entire list of users would be returned, potentially overwhelming the server and client with too much data.

**Hiding Connected Users:**
 - The logic using ConnectionRequest prevents showing users that the logged-in user is already connected to or has sent/received a connection request from.
 - Without this, the user might see people they are already connected with, which defeats the purpose of a "new connections" feed.










  







 














  




















