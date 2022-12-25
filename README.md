# Javascript-AI-APP

We will use:

- Javascript
- HTML
- CSS
- Veet
- Node.JS
- Open-AI


You'll also learn how to: 
- Setup Vanilla Javascript with Veet
- Use Node.js for server side
- Responsive layouts with CSS
- Render markdown from strings
- Make API requests
- Catch errors



# SETUP

- Open VSCode and create a new folder
- Install node.js https://nodejs.org/en/download/
- Now ```npm create vite@latest client --template vanilla```
- Choose Vanilla and Javascript
- Now cd into client folder
- Then run ```npm install`` to install all necessary dependencies
- Download assets from this link: https://minhaskamal.github.io/DownGit/#/home?url=https:%2F%2Fgithub.com%2Fadrianhajdin%2Fproject_openai_codex%2Ftree%2Fmain%2Fclient%2Fassets

- Unzip the asset folder and paste it into client.
- Download style.css from this link: https://gist.github.com/adrianhajdin/2059ca74452a18d1560aac9499f58900
- Edit style.css
- From the assets folder, move the favicon to the public folder and delete vite.svg

- Open index.html 
change href to favicon.ico
change the title of the application
add a link tag: ```<link rel="stylesheet" href="style.css">```
in the body, we will add a self closing div to the app div.
then we will create a form that will allow us type into it:
```
<body>
    <div id="app">
      <div id="chat_container"></div>
      <form action="">
        <textarea name="prompt" id="" cols="1" rows="1" placeholder="Ask DeFi Assistant..."></textarea>
      </form>
    </div>
    <script type="module" src="/main.js"></script>
  </body>
```

Now we will submit our form with a button:
```
<body>
    <div id="app">
      <div id="chat_container"></div>
      <form action="">
        <textarea name="prompt" id="" cols="1" rows="1" placeholder="Ask DeFi Assistant..."></textarea>
        <button type="submit"><img src="assets/send.svg" /></button>
      </form>
    </div>
    <script type="module" src="/main.js"></script>
  </body>
```

Finally we need to hook up our script module. Change main.js to script.js, in index.js and file.\

Now delete everything in script.js and do ```npm run dev```



The goal is to take this empty script and connect it to the Open-AI API to get prompt from it.




# ChatGPT frontend


First let's import our icons from the assets folder.
```
import bot from './assets/bot.svg';
import user from './assets/user.svg';
```

Because we're not using react, we have to target index.html manually by using document.queryselector.
We'll do this for the form and chat container.

```
const form = document.querySelector('form');
const chatContainer = document.querySelector('#chat_container');
```

Now let's create a variable we're going to fill in later on:

```
let loadInterval;
```

Now we're going to write a function that's going to load our messages:

```
function loader(element) {
    element.textContent = ''

    loadInterval = setInterval(() => {
        // Update the text content of the loading indicator
        element.textContent += '.';

        // If the loading indicator has reached three dots, reset it
        if (element.textContent === '....') {
            element.textContent = '';
        }
    }, 300);
}
```

Now there's a similar function we're going to create. Here's where we will implement that typing functionality that the AI uses to generate its results.

```
function typeText(element, text) {
  let index = 0

  let interval = setInterval(() => {
    // this means that we're still typing
      if (index < text.length) {
          element.innerHTML += text.charAt(index)
          index++
      } else {
        //we have reached the end of the text
          clearInterval(interval)
      }
  }, 20)
}
```

We will need to generate a unique single ID for every single message.

```
// generate unique ID for each message div of bot
// necessary for typing text effect for that specific reply
// without unique ID, typing text will work on every element
function generateUniqueId() {
    const timestamp = Date.now();
    const randomNumber = Math.random();
    const hexadecimalString = randomNumber.toString(16);

    return `id-${timestamp}-${hexadecimalString}`;
}
```

Now let's implement a chat stripe that seperates a user message from the AI message:

```
function chatStripe(isAi, value, uniqueId) {
  //this function is going to return a template string - notice the div in the string
  return (
      `
      <div class="wrapper ${isAi && 'ai'}">
          <div class="chat">
              <div class="profile">
                  <img 
                    src=${isAi ? bot : user} 
                    alt="${isAi ? 'bot' : 'user'}" 
                  />
              </div>
              <div class="message" id=${uniqueId}>${value}</div>
          </div>
      </div>
  `
  )
}
```


Now we will create a function to handle submission from user


```
const handleSubmit = async (e) => {
  //we don't want it to reload the browser - the default event
  e.preventDefault();

  //to get the data that has been passed into the form
  const data = new FormData(form);

  //to generate a new chatstripe for the user
  // user's chatstripe
  chatContainer.innerHTML += chatStripe(false, data.get('prompt'));

  // to clear the textarea input 
  form.reset();

  // bot's chatstripe
  const uniqueId = generateUniqueId();
  chatContainer.innerHTML += chatStripe(true, " ", uniqueId);
  //the empty space will be filled with ....

  //as the user is typing we want to keep scrolling down
  // to focus scroll to the bottom 
  chatContainer.scrollTop = chatContainer.scrollHeight;

  // specific message div 
  const messageDiv = document.getElementById(uniqueId)

  //then we turn on the loader
  // messageDiv.innerHTML = "..."
  loader(messageDiv)

  const response = await fetch('https://codex-im0y.onrender.com/', {
      method: 'POST',
      headers: {
          'Content-Type': 'application/json',
      },
      body: JSON.stringify({
          prompt: data.get('prompt')
      })
  })

  clearInterval(loadInterval)
  messageDiv.innerHTML = " "

  if (response.ok) {
      const data = await response.json();
      const parsedData = data.bot.trim() // trims any trailing spaces/'\n' 

      typeText(messageDiv, parsedData)
  } else {
      const err = await response.text()

      messageDiv.innerHTML = "Something went wrong"
      alert(err)
  }
}

//for handleSubmit to work
form.addEventListener('submit', handleSubmit)
form.addEventListener('keyup', (e) => {
  if (e.keyCode === 13) {
      handleSubmit(e)
  }
})
```


Now if you save and reload your brower, you will find that the frontend works.



# Open AI Server

- Now collapse the client folder and create a new folder name server.
- ```cd ..`` to move the directory up
- cd into server folder
- Now run ```npm init -y``` to generate a new package.json file
- then run ```npm install cors dotenv express nodemon openai```

cors - for making cross origin request
dotenv - for environmental variables
express - 
nodemon - to keep our application running
openai

Now create a new file called server.js.

First we need the API key from https://openai.com/api/

Click the profile on the top right and click 'view API keys'. And create a new secret key

Now in the root of the application, create a .env file, paste the secret key:

```
OPEN_API_KEY=""
```

Now open the server.js file:

```
import express from 'express'
import * as dotenv from 'dotenv'
import cors from 'cors'
import { Configuration, OpenAIApi } from 'openai'

dotenv.config()

const configuration = new Configuration({
  apiKey: process.env.OPENAI_API_KEY,
});

//we will create an openai instance
const openai = new OpenAIApi(configuration);

//we need to initialize our express application
const app = express();
//we will set up middlewares
app.use(cors()); //allows our server to be called from the frontend
app.use(express.json()); //allows us to pass json from frontend to backend

//a dummy route rout
app.get('/', async (req, res) => {
  res.status(200).send({
    message: 'Hello from CodeX!'
  })
});

app.post('/', async (req, res) => {
  try {
    const prompt = req.body.prompt;

    const response = await openai.createCompletion({
      model: "text-davinci-003",
      prompt: `${prompt}`,
      temperature: 0, // Higher values means the model will take more risks.
      max_tokens: 3000, // The maximum number of tokens to generate in the completion. Most models have a context length of 2048 tokens (except for the newest models, which support 4096).
      top_p: 1, // alternative to sampling with temperature, called nucleus sampling
      frequency_penalty: 0.5, // Number between -2.0 and 2.0. Positive values penalize new tokens based on their existing frequency in the text so far, decreasing the model's likelihood to repeat the same line verbatim.
      presence_penalty: 0, // Number between -2.0 and 2.0. Positive values penalize new tokens based on whether they appear in the text so far, increasing the model's likelihood to talk about new topics.
    });

    //once we get a response, we will send it to the frontend
    res.status(200).send({
      bot: response.data.choices[0].text
    });

  } catch (error) {
    console.error(error)
    res.status(500).send(error || 'Something went wrong');
  }
})

//to make sure the server always listens to requests
app.listen(5000, () => console.log('AI server started on http://localhost:5000'));
```

Now we can run our server but first go to package.json and edit scripts with:

```
"server": "nodemon server"
```
Also add:

```
"type": "module",
```
This will allow us use the import statements in server.js

Also remove the main.

Now we can run ```npm run server

You can also open a new terminal, cd into client and run ```npm run dev``` to run client side simultaneously.



# Connecting Client to Server


