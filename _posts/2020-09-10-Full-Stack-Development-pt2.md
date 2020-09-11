---
layout: post
title: Full Stack Development Part 2 Connecting React To Web Server
date: 2020-09-10 20:46
category: Full Stack 
thumbnail: /assets/img/posts/React.png
author: Benjamin Makowsky
tags: [express, cors, mysql, react, node, javascript, typescript]
summary: In this post we will be discussing how to connect a React web application to javascript webserver
---

# Welcome Back!
If you read the [previous post][FS_Part1] or watched the [YouTube Video][Youtube_Link] then you should have a simple JavaScript web server up and running. If not go watch [this video][Youtube_Link] so that you can be all caught up. As always you can find the [complete source code here][sourceCode].


## Setting Up React
The setup for react is actually pretty straight forward. In fact, its only a single line the only thing different from a standard React app is the --typescript flag at the end of the command:
```
npm create react-app NAME_OF_APP_HERE --typescript
```
Once the command has been completed, inside the folder it just created navigate to _src/App.tsx/_ and open that file, it should look like the following:
```typescript
import React from 'react';
import logo from './logo.svg';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.tsx</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}
export default App;
```
Now add the following import statements and delete everything inside the return statement so that your App.tsx matches below:
```typescript
//REQUIRED IMPORT STATEMENTS
import React, {useState, useEffect} from 'react';
import './App.css';

function App() {
  return (
      <header className="App-header">
      </header>
  );
}
```
## useState()
Now we have a blank canvas to work with. The first thing we are going to want to do is use what is called a hook. The hook we want is called useState(). Modify your function so that it looks like below now:
```typescript
function App() {
    
    //NEW STUFF:
    const [battery, setBattery] = useState('');
    
    return (
        <header className="App-header">
        </header>
    );
}
```
__useState()__ is a hook that returns an array where the first element is your state variable, and the second is the function to modify that variable. What a state variable does, is it tells react to re-render the element identified as a state so that upon change, it is shown on the web app. You can use the state hook for buttons or text or anything you expect to change. In this case we are using it to hold our database values.
## useEffect()
Now that we have our state variable we want a way to set it when our app renders. To do this, we are going to use another hook call __useEffect()__. If you are familiar React classes, useEffect() is like componentDidMount, componentDidUpdate, and componentWillUnmount all rolled into one. Now, update the function so that it looks like below:
```typescript
function App() {
    const [battery, setBattery] = useState('');

    //NEW STUFF:
    useEffect(()=> {
        //NOTE: USE THE IP ADDRESS OF THE DEVICE RUNNING THE SERVER, REPLACE WITH LOCAL HOST IF THE SERVER IS RUNNING ON THE SAME MACHINE AS YOUR APP
        fetch('http://localhost:4000/stats')
            .then(response => response.json())
            .then(response => {
                setBattery(response.data[0].battery);
            });
    });

    return (
        <header className="App-header">
        </header>
    );
}
```
Use effect takes in a function that will run once React renders our app. Here we call __fetch()__ to retrieve the data from our webserver at the stats path that we defined in the previous tutorial. _Please note that the IP Address will likely be localhost unless your server is running elsewhere like a raspberry pi._ Once fetch has retrieved the data we __then__ convert it to json. __Then__ we use the setBattery state function we created earlier and pass in the value we want from our fetch response.

## Making the Data Appear
Now for the last part, making the data appear in our webapp. This can be accomplished by adding just one line to our App function:
```typescript
function App() {
    const [battery, setBattery] = useState('');

    useEffect(()=> {
        fetch('http://localhost:4000/stats')
            .then(response => response.json())
            .then(response => {
                //Change battery to your own cell name
                setBattery(response.data[0].battery);
            });
    });

    return (
        <header className="App-header">
            <div>{battery}</div> <-New Stuff here->
        </header>
    );
}
```
And thats it! Now make sure your server is running then from then in the terminal, make sure you are in your react apps root folder calling the following and enjoy!
```
npm start
```

[FS_Part1]: https://makestudios.dev/full%20stack/2020/09/08/Full-Stack-Part-1/#/
[YouTube_Link]: https://www.youtube.com/watch?v=xM8NxY9sU1g
[sourceCode]: https://github.com/benjaminmakowsky/Youtube/tree/master/demo
