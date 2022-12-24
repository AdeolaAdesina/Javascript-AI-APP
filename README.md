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



