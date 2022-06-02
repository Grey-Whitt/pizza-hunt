# pizza-hunt

## Description:

[Pizza Hunt](https://pizza-hunt-gw.herokuapp.com/) is a full-stack project built for pizza lovers around the globe to communicate and share their favorite recipes. HTML and CSS frontend and a custom RESTful API with the Mongoose ODM to interact with a MongoDB database.
IndexedDB was also made use of to give this app offline functionality.

# Table of Contents

- [Repository](#repository)
- [Examples](#examples)
- [Technologies Used](#technologies-used)
- [Questions](#questions)
- [Contributions](#contributing)
- [License](#license)

## Repository:

---

- [My Github Profile](https://github.com/Grey-Whitt)

- [This Repository](https://github.com/Grey-Whitt/portfolio-v3)

- [Deployed Appliccation](https://pizza-hunt-gw.herokuapp.com/)

## Examples

---

Here I want to talk a bit about my use of [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) because I think its super interesting.  
IndexedDB is a low-level client side storage api that stores data in key-value pairs. I implemented it in this app so users can still post their favorite pizzas, even when offline.

This is the request that is made when the user attempts to submit a new pizza using the fetch API. If the user cant connect to the Express server then the saveRecord function is called on the last line of the .catch() and we feed in (formdata) which is the pizza.
This code exists in /public/assets/js/add-pizza.js  
```
fetch('/api/pizzas', {
    method: 'POST',
    headers: {
      Accept: 'application/json',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(formData)
  })
    .then(response => response.json())
    .then(postResponse => {
      alert('Pizza created successfully!');
      console.log(postResponse);
    })
    .catch(err => {
      console.log(err);
      saveRecord(formData); <------------
    });
};
```  



Now moving over to /public/assets/js/idb.js we can see what happens when the saveRecord() function is invoked.  
First a new transaction is opened and two values are passed in, "new_pizza" is the object store (table) that I want to access, and "readwrite" which gives read and write permissions.  
  
Then I access the "new_pizza" object store, and on the next line I use the .add() method and pass in "record", which is the pizza.
```
function saveRecord(record) {

    const transaction = db.transaction(['new_pizza'], 'readwrite');

    const pizzaObjectStore = transaction.objectStore('new_pizza');

    pizzaObjectStore.add(record);
}
```

Here is what happens when the user gets internet connection again. this is called by using `window.addEventListener('online', uploadPizza);`  
I've broken this down in to three parts. In the first section I open up a connection to the store and get all of the data.
  
In the second section, if getAll is succesful and there is more than 0 pizzas stored I make a POST request to the API and send all of the pizzas to the database.  
  
In the third section I parse the response from the server using the .json() method. If there is an error I throw a new error. Then I again open a connection to the database and clear everthing to get rid of the data that is no longer needed since its now stored on the database!
```
function uploadPizza() {

    const transaction = db.transaction(['new_pizza'], 'readwrite');   ===========
                                                                               ||
    const pizzaObjectStore = transaction.objectStore('new_pizza');             || ========= 1
                                                                               ||  
    const getAll = pizzaObjectStore.getAll();                         ===========



    getAll.onsuccess = function () {                                 =======================
                                                                                          ||
        if (getAll.result.length > 0) {                                                   ||
            fetch('/api/pizzas', {                                                        ||
                method: 'POST',                                                           ||
                body: JSON.stringify(getAll.result),                                      || ========= 2
                headers: {                                                                ||
                    Accept: 'application/json, text/plain, */*',                          ||
                    'Content-Type': 'application/json'                                    ||
                }                                                                         ||
            })                                                        ======================      
                .then(response => response.json())                    ========================= 
                .then(serverResponse => {                                                    ||
                    if (serverResponse.message) {                                            ||
                        throw new Error(serverResponse);                                     ||
                    }                                                                        ||
                                                                                             ||
                    const transaction = db.transaction(['new_pizza'], 'readwrite');          ||
                                                                                             ||
                    const pizzaObjectStore = transaction.objectStore('new_pizza');           || 
                                                                                             ||
                    pizzaObjectStore.clear();                                                || ========== 3
                                                                                             ||
                    alert('All saved pizza has been submitted!');                            || 
                })                                                                           ||
                .catch(err => {                                                              ||
                    console.log(err);                                                        ||
                });                                                                          ||
        }                                                                                    ||
    };                                                                                       ||
}                                                                     =========================
```
You can see this code and comments in /public/assets/js/idb.js




## Technologies Used

---

Node  
Express  
Mongoose  
JavaScript  
Fetch API  
CSS  
HTML  
Deployed with Heroku  


## Questions

---

If you have any questions feel free to email me or find me on LinkedIn

[greywhitt@gmail.com](mailto:greywhitt@gmail.com)

[My LinkedIn](https://www.linkedin.com/in/grey-whittenberger)

[www.greywhitt.com](https://www.greywhitt.com/)

## Contributing:

---

[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-v2.1%20adopted-ff69b4.svg)](./uploads/CODE_OF_CONDUCT.md)

Contributions, issues and feature requests are welcome.

## License:

---

![MIT License](https://img.shields.io/badge/license-MIT-blue)

Copyright 2022 Grey Whittenberger

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

For more information about licenses, please visit:
[License](https://opensource.org/licenses/MIT)
