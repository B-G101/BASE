---
title: Rate Popular Products
layout: default
permalink: /products
image: /images/teamlogo.png
tags: [javascript]
---

<!-- HTML table fragment for page -->
<table>
  <thead>
  <tr>
    <th>Product</th>
    <th>Buy</th>
    <th>Skip</th>
  </tr>
  </thead>
  <tbody id="result">
    <!-- javascript generated data -->
  </tbody>
</table>

<!-- Script is layed out in a sequence (without a function) and will execute when page is loaded -->
<script>

  // prepare HTML defined "result" container for new output
  const resultContainer = document.getElementById("result");

  // keys for product reactions
  const YA = "Ya";
  const NA = "Na";

  // prepare fetch urls
  // const url = "https://flask.nighthawkcodingsociety.com/api/products";
  const url = "https://vase.nighthawkcoders.tk/api/products";
  const get_url = url +"/";
  const ya_url = url + "/buy/";  
  const na_url = url + "/skip/";  

  // prepare fetch GET options
  const options = {
    method: 'GET', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, omit
    headers: {
      'Content-Type': 'application/json'
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
  };
  // prepare fetch PUT options, clones with JS Spread Operator (...)
  const put_options = {...options, method: 'PUT'}; // clones and replaces method

  // fetch the API
  fetch(get_url, options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors
      if (response.status !== 200) {
          error('GET API response failure: ' + response.status);
          return;
      }
      // valid response will have JSON data
      response.json().then(data => {
          console.log(data);
          for (const row of data) {
            // make "tr element" for each "row of data"
            const tr = document.createElement("tr");
            
            // td for product cell
            const product = document.createElement("td");
              product.innerHTML = row.id + ". " + row.product;  // add fetched data to innerHTML

            // td for ya cell with onclick actions
            const ya = document.createElement("td");
              const ya_but = document.createElement('button');
              ya_but.id = YA+row.id   // establishes a ya JS id for cell
              ya_but.innerHTML = row.ya;  // add fetched "ya count" to innerHTML
              ya_but.onclick = function () {
                // onclick function call with "like parameters"
                reaction(YA, like_url+row.id, ya_but.id);  
              };
              ya.appendChild(ya_but);  // add "ya button" to ya cell

            // td for na cell with onclick actions
            const na = document.createElement("td");
              const na_but = document.createElement('button');
              na_but.id = NA+row.id  // establishes a na JS id for cell
              na_but.innerHTML = row.na;  // add fetched "na count" to innerHTML
              na_but.onclick = function () {
                // onclick function call with "na parameters"
                reaction(NA, na_url+row.id, na_but.id);  
              };
              na.appendChild(na_but);  // add "na button" to na cell
             
            // this builds ALL td's (cells) into tr (row) element
            tr.appendChild(product);
            tr.appendChild(ya);
            tr.appendChild(na);

            // this adds all the tr (row) work above to the HTML "result" container
            resultContainer.appendChild(tr);
          }
      })
  })
  // catch fetch errors (ie Nginx ACCESS to server blocked)
  .catch(err => {
    error(err + " " + get_url);
  });

  // Reaction function to likes or nas user actions
  function reaction(type, put_url, elemID) {

    // fetch the API
    fetch(put_url, put_options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors
      if (response.status !== 200) {
          error("PUT API response failure: " + response.status)
          return;  // api failure
      }
      // valid response will have JSON data
      response.json().then(data => {
          console.log(data);
          // Likes or nas updated/incremented
          if (type === YA) // like data element
            document.getElementById(elemID).innerHTML = data.ya;  // fetched ya data assigned to ya Document Object Model (DOM)
          else if (type === NA) // na data element
            document.getElementById(elemID).innerHTML = data.na;  // fetched na data assigned to na Document Object Model (DOM)
          else
            error("unknown type: " + type);  // should never occur
      })
    })
    // catch fetch errors (ie Nginx ACCESS to server blocked)
    .catch(err => {
      error(err + " " + put_url);
    });
    
  }

  // Something went wrong with actions or responses
  function error(err) {
    // log as Error in console
    console.error(err);
    // append error to resultContainer
    const tr = document.createElement("tr");
    const td = document.createElement("td");
    td.innerHTML = err;
    tr.appendChild(td);
    resultContainer.appendChild(tr);
  }

</script>
