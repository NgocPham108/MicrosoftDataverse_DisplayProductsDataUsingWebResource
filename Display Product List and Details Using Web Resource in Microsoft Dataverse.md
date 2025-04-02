Display Product List and Details Using Web Resource in Microsoft Dataverse\*\*

An HTML web resource page can only accept a single custom parameter called `data`. To pass multiple values in the `data` parameter, you need to encode the parameters and decode them in your page. My article demonstrates how to:

- Retrieve a list of products from Microsoft Dataverse.
- Pass product details dynamically to a web resource using the `data` parameter.
- Encode and decode the `data` parameter to handle multiple values.
- Display product details dynamically in a web resource.

---

## **1️⃣ Introduction**

Microsoft Dataverse is a powerful platform for managing product data in enterprise applications. When building a sales webpage, you may need to **display a list of products from Dataverse**, and when a customer **clicks on a product**, the product details should **automatically display** through a web resource.

**🎯 Objectives of this guide:**
✔ **Connect and retrieve product data from Dataverse.**  
✔ **Pass dynamic data to a web resource** when a user clicks on a product.  
✔ **Encode and decode parameters to optimize data transmission.**  
✔ **Explain each function in detail for better understanding.**

💡 _If you're building a Power Platform application, this technique is essential for optimizing the user experience!_

---

## **2️⃣ Product Data Structure in Dataverse**

You need to create a **Products table** in **Dataverse** with the following columns:

| **Column Name** | **Data Type**   | **Description**      |
| --------------- | --------------- | -------------------- |
| `Name`          | Text            | Product name         |
| `Price`         | Currency        | Product price        |
| `Description`   | Multi-line Text | Product description  |
| `Manufacturer`  | Text            | Product manufacturer |
| `StockQuantity` | Number          | Stock quantity       |
| `ImageUrl`      | URL             | Product image URL    |

📌 **Real-world application:** When a customer selects a product, the system will **retrieve data from Dataverse** and **display detailed information in a web resource**.

---

## **3️⃣ Display Product List from Dataverse**

We will create an HTML page to display the product list. **Data will be retrieved from the Dataverse API** using JavaScript.

### **JavaScript Code to Retrieve Product List**

```javascript
async function loadProducts() {
  const response = await fetch(
    "https://yourserver/api/data/v9.0/Products?$select=name,price,description,manufacturer,stock,imageUrl"
  );
  const data = await response.json();

  let tableHtml =
    "<table><tr><th>Image</th><th>Name</th><th>Price</th><th>Details</th></tr>";

  data.value.forEach((product) => {
    tableHtml += `<tr>
            <td><img src="${product.imageUrl}" alt="${product.name}" width="100"></td>
            <td>${product.name}</td>
            <td>${product.price}</td>
            <td><button onclick="openProductDetails('${product.name}', '${product.price}', '${product.description}', '${product.manufacturer}', '${product.stock}', '${product.imageUrl}')">View Details</button></td>
        </tr>`;
  });

  document.getElementById("productList").innerHTML = tableHtml;
}

loadProducts();
```

### **Explanation of `loadProducts()`**

✔ **Retrieve product data from the Dataverse API.**  
✔ **Convert the response to JSON** for easy data manipulation.  
✔ **Display products in a table format, including product images.**  
✔ **The "View Details" button triggers an event to open a web resource displaying product details.**

---

## **4️⃣ Encode Product Data Before Passing to Web Resource**

### **JavaScript Code for Handling Click Events**

```javascript
function openProductDetails(
  name,
  price,
  description,
  manufacturer,
  stock,
  imageUrl
) {
  const data = `name=${name}&price=${price}&description=${description}&manufacturer=${manufacturer}&stock=${stock}&imageUrl=${imageUrl}`;
  const encodedData = encodeURIComponent(data);
  const webResourceUrl = `https://yourserver/WebResources/ProductDetails.htm?data=${encodedData}`;

  window.open(webResourceUrl, "_blank");
}
```

### **Explanation of `openProductDetails()`**

✔ **Create a data string** containing product details such as name, price, description, manufacturer, stock, and image URL.  
✔ **Use `encodeURIComponent()` to encode the data**, ensuring no errors occur when passing it through the URL.  
✔ **Open the web resource** with the encoded product details, enabling the display of product information.

---

## **5️⃣ Decode and Display Data in Web Resource**

When the web resource receives the data, it needs to **decode the data** to display the correct product information.

### **JavaScript Code in ProductDetails.htm**

```javascript
function getDataParam() {
  let params = new URLSearchParams(window.location.search);
  let dataParam = params.get("data");

  if (dataParam) {
    parseDataValue(dataParam);
  } else {
    document.body.innerHTML =
      "<p style='color: red;'>Error: No product data provided!</p>";
  }
}

function parseDataValue(datavalue) {
  let decodedParams = decodeURIComponent(datavalue);
  let paramArray = decodedParams.split("&");

  let table = "<table><tr><th>Information</th><th>Value</th></tr>";

  paramArray.forEach((param) => {
    let keyValue = param.split("=");
    table += `<tr><td>${keyValue[0]}</td><td>${keyValue[1]}</td></tr>`;
  });

  table += "</table>";
  document.body.innerHTML = table;
}
```

### **Explanation of Functions**

#### **`getDataParam()`**

✔ **Retrieve data from the URL** and check if the `data` parameter exists.  
✔ **If data exists, call `parseDataValue()`** to process it.  
✔ **If no data is found, display an error message.**

#### **`parseDataValue()`**

✔ **Decode the data from the URL using `decodeURIComponent()`.**  
✔ **Split the data string into key-value pairs** (e.g., `name`, `price`, `description`, etc.).  
✔ **Iterate through the values and display them in an HTML table.**

---

## **🚀 Summary**

✔ **Display a product list from Dataverse on a webpage.**  
✔ **Encode product data before passing it to a web resource.**  
✔ **Decode and display product data in the web resource.**  
✔ **Integrate product images to enhance the user interface.**
