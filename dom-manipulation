const quoteDisplay = document.querySelector("#quoteDisplay");
const newQuoteBtn = document.getElementById("newQuote");

const newQuote = document.querySelector("#newQuoteText");
const newCategory = document.querySelector("#newQuoteCategory");

const quotes =JSON.parse(localStorage.getItem("quotes")) || [
  {inspirational: [
    "Believe you can and you're halfway there.",
    "The only way to do great work is to love what you do."
  ]},

  {love: [
    "The best thing to hold onto in life is each other.",
    "Love is not about how many days, months, or years you have been together. It’s all about how much you love each other every single day."
  ]},

  {success: [
    "Opportunities don't happen, you create them.",
    "Success is not final, failure is not fatal: it is the courage to continue that counts."
  ]},

  {life: [
    "Do what you can, with what you have, where you are.",
    "In the middle of every difficulty lies opportunity."
  ]}
];

function showRandomQuote(array){
  //Generate a random category index
  const categoryIndex = Math.floor(Math.random() * array.length);
  //Get the category name 
  const category = Object.keys(array[categoryIndex])[0];

  const quoteIndex = Math.floor(Math.random() * array[categoryIndex][category].length);

  const randomQuote = array[categoryIndex][category][quoteIndex];
  
  const para = document.createElement("p");
  para.textContent = randomQuote;
  quoteDisplay.innerHTML = "";
  quoteDisplay.appendChild(para);
  
}

function createAddQuoteForm(){

  if(newQuote.value && newCategory.value){

    let categoryExists = false;
    let categoryIndex;
    let quoteCategory = newCategory.value.toLowerCase();
  
    //Loop through the quotes array to check if category already exists
    quotes.forEach((obj, index)=> {
      if(obj.hasOwnProperty(quoteCategory)){
        categoryExists = true;
        categoryIndex = index;
        return;
      }
    })
  
    if(categoryExists){
      if(quotes[categoryIndex][quoteCategory].includes(newQuote.value)){
        console.log("This quote already exists");
      }else{
        quotes[categoryIndex][quoteCategory].push(newQuote.value);
        localStorage.setItem("quotes", JSON.stringify(quotes));
      }
      
    }else{
      const newQuoteObj = {[quoteCategory]: [newQuote.value]};
      quotes.push(newQuoteObj);
      localStorage.setItem("quotes", JSON.stringify(quotes));
      populateCategories();

    }
  }
  
  newQuote.value = "";
  newCategory.value = "";
  newQuote.focus();
}

newQuoteBtn.addEventListener("click", () =>{

  showRandomQuote(quotes);

});

const downloadBtn = document.querySelector("#downloadBtn");
const fileInput = document.querySelector("#importFile");


function downloadQuotes(){
  const jsonData = JSON.stringify(quotes, null, 2);
  
  const blob = new Blob([jsonData], {type: "application/json"});

  const link = document.createElement("a");

  link.href = URL.createObjectURL(blob);

  link.download = "quotes.json";

  document.body.appendChild(link);

  link.click();

  document.body.removeChild(link);
  
}

downloadBtn.addEventListener("click", downloadQuotes);

function importFromJsonFile(event){
  const file = event.target.files[0]; //get the chosen file

  const reader = new FileReader();// new instance of file reader

  reader.onload = function(e){
    const importedQuotes  = JSON.parse(e.target.result);
    quotes.push(...importedQuotes);
    localStorage.setItem("quotes", importedQuotes);
  }

  reader.readAsText(file);

}

fileInput.addEventListener("change", (event) => {
  importFromJsonFile(event);
});

const categoryFilter = document.querySelector("#categoryFilter");

function populateCategories(){
  categoryFilter.innerHTML = "";
  const firstOption = document.createElement("option");
  firstOption.value = "All";
  firstOption.textContent = "All Categories";
  categoryFilter.appendChild(firstOption);

  quotes.map(obj => {
    const category  = Object.keys(obj)[0];
    const option = document.createElement("option");
    option.value = category;
    option.textContent = category;

    categoryFilter.appendChild(option);
  })
}

populateCategories();

function filterQuotes(event){
  const selectedCategory = event.target.value;
  let categoryIndex;
  quotes.forEach((obj, index) => {
    if(Object.keys(obj)[0] == selectedCategory){
      categoryIndex = index;
      return
    }
  })

  quoteDisplay.innerHTML = "";

  const quoteArr = quotes[categoryIndex][selectedCategory];

  quoteArr.forEach(quote => {
    const para = document.createElement("p");
    para.textContent = quote;
    quoteDisplay.appendChild(para);
  }) 
  
}

categoryFilter.addEventListener("change", (event) => {
  filterQuotes(event);
})

//Syncing data with the server and implementing conflict resolution
let serverQuotes  = [];

async function fetchQuotesFromServer(){
  try{
    const response = await fetch("https://jsonplaceholder.typicode.com/posts");
    const data = await response.json();
    
    serverQuotes = [...data];  
  
  }catch(error){
    console.log("Error fetching data");
  }

}

setInterval(fetchQuotesFromServer, 30000);

let localEdits = JSON.parse(localStorage.getItem("localEdits")) || [];

function saveLocalQuote(text, author){
  const quote = {
    title: author,
    body: text,
    id: Date.now(),
    timestamp: Date.now()
  }

  if(localEdits)
  localEdits.push(quote);
  localStorage.setItem("localEdits", JSON.stringify(localEdits));

}

saveLocalQuote("First come first serve", "Jeremy");



async function postQuote(text, author){
  const quote = {
    title: author,
    body: text,
    id: 1
  }

  try{
    const response = await fetch("https://jsonplaceholder.typicode.com/posts", {
      method: "POST",
      headers: {"Content-Type": "application/json"},
      body: JSON.stringify(quote)
    });

    const postedQuote = await response.json();
    console.log(postedQuote);

  }catch(error){
    console.log("Error posting quote")
  }
  

}

// postQuote("Hello beautiful people", "Mimoh");
function syncQuotes(){
  if(JSON.stringify(serverQuotes) !== JSON.stringify(localEdits)){
    const manualSync = prompt("Press 1 if you wish to leave the initial data as is or 2 to stay up to data" );
    //Option for manual sync to leave the initial data as is.
    if(manualSync == 1){
      alert("Initial data maintained");
    }else{
      //conflict mgt where server data takes precedence
      localEdits = [...serverQuotes];
      localStorage.setItem("localEdits", JSON.stringify(localEdits));
      alert("Quotes synced with server!")
    }
    
  }else{
    alert("Your data is up to date");
  } 
   
}

setInterval(syncQuotes, 60000);