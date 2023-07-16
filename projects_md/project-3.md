## SIMPLE TO-DO APPLICATION ON MERN WEB STACK

In this project, you are tasked to implement a web solution based on MERN stack in AWS Cloud.

### Step 0 – Preparing prerequisites

You can follow same steps as Project 1 Step 0

### Step 1 – Backend configuration

- Update ubuntu:

  `sudo apt update`
  
- Upgrade ubuntu:

  `sudo apt upgrade`
  
- Lets get the location of Node.js software from Ubuntu repositories:

  `curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`
  
- Install Node.js with the command below:

  `sudo apt-get install -y nodejs`
  
- The command above installs both Node and NPM packages.

- To Verify the version of Nodejs and NPM installed, run the command below:

  `node -v; npm -v`
  
- You should see a screenshot like the one below on your terminal:

![Screenshot from 2022-09-30 21-37-33](https://user-images.githubusercontent.com/23356682/193352707-becf5c4b-e19a-4535-baf8-048ef481f333.png)

- Create a new directory for your To-Do project:

  `mkdir Todo`
  
- Changed into Todo directory with the command below:

  `cd Todo`
  
- Initialise a project by running the command below:

  `npm init`
  
- You should see something like the screenshot below:

![Screenshot from 2022-09-30 19-42-32](https://user-images.githubusercontent.com/23356682/193352922-0d62bf46-fc63-4640-bda2-3c117d3233ae.png)


#### Install ExpressJS
- Install Express using npm:

  `npm install express`
  
- create a file index.js with the command below:

  `touch index.js`
  
- Install the dotenv module:

  `npm install dotenv`
  
- Open the index.js file with the command below:

  `vi index.js`
  
- In the blank open file, paste and save the text below:


```
  const express = require('express');
  require('dotenv').config();
  const app = express();
  const port = process.env.PORT || 5000;
  app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "\*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
  });
  app.use((req, res, next) => {
  res.send('Welcome to Express');
  });
  app.listen(port, () => {
  console.log(`Server running on port ${port}`)
  });
```

- Now run the command below: 

  `node index.js`

#### Routes

- Lets create Routes folder with the command below:

  `mkdir routes`
  
- Change directory to routes folder:

  `cd routes`
  
- create a file api.js with the command below:

  `touch api.js`
  
- Open the file with the command below:

  `vim api.js`
  
- Paste text below in the blank file  and save the file:

```
  const express = require ('express');
  const router = express.Router();
  router.get('/todos', (req, res, next) => {
  });
  router.post('/todos', (req, res, next) => {
  });
  router.delete('/todos/:id', (req, res, next) => {
  })
  module.exports = router;
  
```

#### Models

- Change directory back Todo folder with cd .. and install Mongoose with the command below:

  `npm install mongoose`
  
- Create a new folder models :

  `mkdir models`
  
- Change directory into the newly created ‘models’ folder with:

  `cd models`
  
- Inside the models folder, create a file and name it todo.js:

  `touch todo.js`
  
- Open the file created with vi todo.js then paste the code below in the file:


```
   const mongoose = require('mongoose');
   const Schema = mongoose.Schema;
  //create schema for todo
  const TodoSchema = new Schema({
  action: {
  type: String,
  required: [true, 'The todo text field is required']
  }
  })
  //create model for todo
  const Todo = mongoose.model('todo', TodoSchema);
  module.exports = Todo;
  
```

- Now we need to update our routes from the file api.js in ‘routes’ directory to make use of the new model.

  In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit
  
  
```
   const express = require ('express');
   const router = express.Router();
   const Todo = require('../models/todo');
   router.get('/todos', (req, res, next) => {
   //this will return all the data, exposing only the id and action field to the client
   Todo.find({}, 'action')
   .then(data => res.json(data))
   .catch(next)
   });
   router.post('/todos', (req, res, next) => {
   if(req.body.action){
   Todo.create(req.body)
   .then(data => res.json(data))
   .catch(next)
   }else {
   res.json({
   error: "The input field is empty"
   })
   }
   });
   router.delete('/todos/:id', (req, res, next) => {
   Todo.findOneAndDelete({"_id": req.params.id})
   .then(data => res.json(data))
   .catch(next)
   })
   module.exports = router;
   
```

#### MongoDB Database

We need a database where we will store our data. For this we will make use of mongodb Atlas.

In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now.

- Create a file in your Todo directory and name it .env.

  ```
  touch .env
  vi .env
  ```
- Add the connection string to access the database in it, just as below:

  `DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'`
  
Now we need to update the index.js to reflect the use of .env so that Node.js can connect to the database.

- Delete the content of index.js, paste the text below and save the file:

  ```
     const express = require('express');
     const bodyParser = require('body-parser');
     const mongoose = require('mongoose');
     const routes = require('./routes/api');
     const path = require('path');
     require('dotenv').config();
     const app = express();
     const port = process.env.PORT || 5000;
     //connect to the database
     mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
     .then(() => console.log(`Database connected successfully`))
     .catch(err => console.log(err));
     //since mongoose promise is depreciated, we overide it with node's promise
     mongoose.Promise = global.Promise;
     app.use((req, res, next) => {
     res.header("Access-Control-Allow-Origin", "\*");
     res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
     next();
     });
     app.use(bodyParser.json());
     app.use('/api', routes);
     app.use((err, req, res, next) => {
     console.log(err);
     next();
     });
     app.listen(port, () => {
     console.log(`Server running on port ${port}`)
     });
  ```

- Start your server using the command:

  `node index.js`
  
- You should soething like the screenshot below if done successfully:

![Screenshot from 2022-09-30 20-01-10](https://user-images.githubusercontent.com/23356682/193354174-d796fe0c-1e35-452d-b87a-69577544c261.png)

Using Postman as a tool to test Api endpoints, You should see something like the screenshot below if our endpoints work perfectly.

![Screenshot from 2022-09-30 20-06-26](https://user-images.githubusercontent.com/23356682/193354406-b536421d-7620-42b5-8572-1c7a5a0189fd.png)



### Step 2 – Frontend creation
Since we are done with the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) with React to interact with the application via API.

- In the same root directory as your backend code, which is the Todo directory, run:

  `npx create-react-app client`
  
#### Running a React App

- Install concurrently with the command below:

  `npm install concurrently --save-dev`
  
- Install nodemon with the command below:

  `npm install nodemon --save-dev`

- In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.

  ```
     "scripts": {
     "start": "node index.js",
     "start-watch": "nodemon index.js",
     "dev": "concurrently \"npm run start-watch\" \"cd ../client && npm start\""
     },
     
  ```
  
#### Configure Proxy in package.json

- Change directory to ‘client’ with the command below:

  `cd client`

- Open the package.json file:

  `vi package.json`
  
- Add the key value pair in the package.json file "proxy": "http://localhost:5000"
  The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling     the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos
  
- Now, ensure you are inside the Todo directory, and simply do:

  `npm run dev`
  
  This should start the app on port 3000

#### Creating your React Components

- From your Todo directory run the command below:
  
  `cd client`
  
- move to the src directory

  `cd src`
  
- Inside your src folder create another folder called components:

  `mkdir components`
  
- Move into the components directory with the command below:

  `cd components`
  
- Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js:

  `touch Input.js ListTodo.js Todo.js`
  
- Open Input.js file:

  `vi Input.js`

- Copy and paste the code below:

  ```
      import React, { Component } from 'react';
      import axios from 'axios';
      class Input extends Component {
      state = {
      action: ""
      }
      addTodo = () => {
      const task = {action: this.state.action}
      if(task.action && task.action.length > 0){
       axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
           }
         })
         .catch(err => console.log(err))
      }else {
      console.log('input field required')
     }
   }
     handleChange = (e) => {
    this.setState({
     action: e.target.value
   })
   }
     render() {
    let { action } = this.state;
    return (
    <div>
    <input type="text" onChange={this.handleChange} value={action} />
    <button onClick={this.addTodo}>add todo</button>
    </div>
       )
       }
    }
   export default Input
  ```


- Install Axios: A http client using the commands below:

  `cd ..; cd ..; npm install axios;`

- Go to ‘components’ directory:

  `cd src/components`

- After that open your ListTodo.js:

  `vi ListTodo.js`
  
- in the ListTodo.js copy and paste the following code:

  ```
      import React from 'react';
      const ListTodo = ({ todos, deleteTodo }) => {
      return (
      <ul>
      {
      todos &&
      todos.length > 0 ?
      (
      todos.map(todo => {
      return (
      <li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
      )
      })
      )
      :
      (
      <li>No todo(s) left</li>
      )
      }
      </ul>
      )
      }
      export default ListTodo
      
  ```
- Then in your Todo.js file you write the following code:

  ```
     import React, {Component} from 'react';
     import axios from 'axios';
     import Input from './Input';
     import ListTodo from './ListTodo';
     class Todo extends Component {
     state = {
     todos: []
     }
     componentDidMount(){
     this.getTodos();
     }
     getTodos = () => {
     axios.get('/api/todos')
     .then(res => {
     if(res.data){
     this.setState({
     todos: res.data
     })
     }
     })
     .catch(err => console.log(err))
     }
     deleteTodo = (id) => {
     axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))
     }
    render() {
    let { todos } = this.state;
    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )
    }
  }
  export default Todo;
  
  ```
- Delete the logo and adjust our App.js to look like this.

- Open App.js file by running the command below:

  `cd .. && vi App.js`
  
- Paste the text below into the file:

  ```
     import React from 'react';
     import Todo from './components/Todo';
     import './App.css';
     const App = () => {
     return (
     <div className="App">
     <Todo />
     </div>
     );
     }
     import React from 'react';
     export default App;
  ```

- In the src directory open the App.css with the command below:

  `vi App.css`
  
- Then paste the following code into App.css:

  ```
      .App {
      text-align: center;
      font-size: calc(10px + 2vmin);
      width: 60%;
      margin-left: auto;
      margin-right: auto;
      }
      input {
      height: 40px;
      width: 50%;
      border: none;
      border-bottom: 2px #101113 solid;
      background: none;
      font-size: 1.5rem;
      color: #787a80;
      }
      input:focus {
      outline: none;
      }
      button {
      width: 25%;
      height: 45px;
      border: none;
      margin-left: 10px;
      font-size: 25px;
      background: #101113;
      border-radius: 5px;
      color: #787a80;
      cursor: pointer;
      }
      button:focus {
      outline: none;
      }
      ul {
      list-style: none;
      text-align: left;
      padding: 15px;
      background: #171a1f;
      border-radius: 5px;
      }
      li {
      padding: 15px;
      font-size: 1.5rem;
      margin-bottom: 15px;
      background: #282c34;
      border-radius: 5px;
      overflow-wrap: break-word;
      cursor: pointer;
      }
      @media only screen and (min-width: 300px) {
      .App {
      width: 80%;
      }
      input {
      width: 100%
      }
      button {
      width: 100%;
      margin-top: 15px;
      margin-left: 0;
      }
      }
      @media only screen and (min-width: 640px) {
     .App {
     width: 60%;
     }
     input {
     width: 50%;
     }
     button {
     width: 30%;
     margin-left: 10px;
     margin-top: 0;
     }
     }
  ```

- In the src directory open the index.css with the command below:

  `vi index.css`
  
- Copy and paste the code below:

  ``` 
      body {
      margin: 0;
      padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
      "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
      sans-serif;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
      box-sizing: border-box;
      background-color: #282c34;
      color: #787a80;
      }
      code {
      font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
      monospace;
      }
  ```
  
- Go to the Todo directory with the command below:

  `cd ../..`
  
- When you are in the Todo directory run:

  `npm run dev`
  
If ther are no errors, refresh your browser and you will find something like the screenshot below:

![Screenshot from 2022-09-30 20-41-59](https://user-images.githubusercontent.com/23356682/193355306-a7d80369-c8bb-471c-8088-c089f2c9e96d.png)
