---
title: Kohls Shopping API
layout: default
permalink: /data/shopapi
image: /images/teamlogo.png
tags: [javascript]
---
<body style="background-color: #F8F2F0" >
</body>

<!-- HTML table fragment for page -->

<table>
  <thead>
  <tr>
    <th>Product</th>
  
    <th>Rating</th>
    <th>Price</th>
  </tr>
  </thead>
  <tbody id="result">
    <!-- generated rows -->
  </tbody>
</table>

<!-- Script is layed out in a sequence (no function) and will execute when page is loaded -->
<script>
  // prepare HTML result container for new output
  const resultContainer = document.getElementById("result");

  // prepare fetch options
  const url = "https://vase.nighthawkcoders.tk/api/shopapi/daily";

  const options = {
    method: 'GET', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'omit', // include, *same-origin, omit
    headers: {
      'Content-Type': 'application/json'
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
  };

  // fetch the API
  fetch(url, options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors
      if (response.status !== 200) {
          const errorMsg = 'Database response error: ' + response.status;
          console.log(errorMsg);
          const tr = document.createElement("tr");
          const td = document.createElement("td");
          td.innerHTML = errorMsg;
          tr.appendChild(td);
          resultContainer.appendChild(tr);
          return;
      }
      // valid response will have json data
      response.json().then(data => {
          console.log(data);

          // Country data
          for (const row of data.payload.products) {
            // Hello please work
            console.log(row);

            // tr for each row
            const tr = document.createElement("tr");
            // td for each column
            const product = document.createElement("td");
         
            const rating = document.createElement("td");
            const price = document.createElement("td");

            // data is specific to the API
            product.innerHTML = row.productTitle;
           
            rating.innerHTML = row.rating.avgRating; 
            price.innerHTML = row.valueAddedBadges.realTime; 

            // this build td's into tr
            tr.appendChild(product);
         
            tr.appendChild(rating);
            tr.appendChild(price);

            // add HTML to container
            resultContainer.appendChild(tr);
          }
      })
  })
  // catch fetch errors (ie ACCESS to server blocked)
  .catch(err => {
    console.error(err);
    const tr = document.createElement("tr");
    const td = document.createElement("td");
    td.innerHTML = err;
    tr.appendChild(td);
    resultContainer.appendChild(tr);
  });
</script>