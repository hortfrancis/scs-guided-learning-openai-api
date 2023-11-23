# scs-guided-learning-openai-api
Code Snippets from the guided learing code-along workshop I ran on how to use the OpenAI API, using Node and npm. 

## Make a request to OpenAI

- Text completion: single prompt and reply
- Using a Fetch request to the basic text completion endpoint — *the simplest implemention*
    
    ```php
    require('dotenv').config();
    
    fetch('https://api.openai.com/v1/completions', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${process.env.OPENAI_API_KEY}`
        },
        body: JSON.stringify({
            'model': 'text-davinci-003',
            'prompt': "What is the worst possible way to describe an egg & cress sandwich?",
            max_tokens: 60
        })
    })
        .then(response => response.json())
        .then(data => console.log(data))
        .catch(error => console.log(error));
    ```
    
- Refactor to use the openai Node module
    
    - https://www.npmjs.com/package/openai
    
    - ES import/export syntax: `"type": "module"` in `package.json`
    
    ```jsx
    import 'dotenv/config';
    import OpenAI from 'openai';
    
    const openAI = new OpenAI({
      apiKey: process.env.OPENAI_API_KEY, 
    });
    
    async function main() {
      const completion = await openAI.completions.create({
        model: 'text-davinci-002',
        prompt: 'Give me bad news in a lighthearted way.',
        max_tokens: 60,
        // temperature: 0,
      });
    
      console.log(completion.choices);
    }
    main().catch(console.error);
    ```

## Create a chatbot with conversation history

- Refactor as a single prompt & response, but using the chat completions endpoint:
    
    ```jsx
    import 'dotenv/config';
    import OpenAI from 'openai';
    
    const openai = new OpenAI({
      apiKey: process.env.OPENAI_API_KEY, 
    });
    
    async function main() {
      const chatCompletion = await openai.chat.completions.create({
        messages: [{ role: 'user', content: 'Review lemons as if you are hungry.' }],
        model: 'gpt-3.5-turbo',
        max_tokens: 60,
      });
    
        console.log(chatCompletion.choices);
    }
    
    main();
    ```
    
- Set up the ‘conversation array’
    
    ```jsx
    import 'dotenv/config';
    import OpenAI from 'openai';
    import readline from 'readline';
    
    const openai = new OpenAI({
        apiKey: process.env.OPENAI_API_KEY, 
      });
    
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout
    });
    
    // Declare the 'conversation array'
    const conversationArray = [];
    
    // Add initial instructions to the AI model
    conversationArray.push({
        role: 'system',
        content: "You are a helpful, friendly assistant. Please answer any questions put to you."
    });
    
    async function callApi() {
      const chatCompletion = await openai.chat.completions.create({
        messages: conversationArray,
        model: 'gpt-3.5-turbo',
        max_tokens: 100,
      });
    
    //   console.log(chatCompletion.choices[0].message);
        return chatCompletion.choices[0].message;
    }
    
    async function chat() {
    
        rl.question('You: ', async (userInput) => {
    
            // Add user input to the conversation array
            conversationArray.push({
                role: 'user',
                content: userInput
            });
    
            // console.log(conversationArray);
    
            // Call the API
            const response = await callApi();
    
            // Add answer from the AI model to the conversation array
            conversationArray.push(response);
    
            // Display the answer from the AI model to the user
            console.log('Bot: ', response.content);
    
            chat(); // repeat the question-answer cycle indefinitely
        });
    }
    
    chat();
    ```
    

## Learning outc
